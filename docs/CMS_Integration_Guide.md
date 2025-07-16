# CMS Integration Guide for TechNexus Blog

## 1. Proposed Headless CMS Solution: Supabase

Given the existing architecture and technology stack of the TechNexus Blog, the most suitable and efficient headless CMS solution is to leverage **Supabase directly** for content management.

### Justification

1. **Alignment with Existing Stack:** The project's [`TechNexus_Blog_system_design.md`](TechNexus_Blog_system_design.md) explicitly states Supabase as the Backend-as-a-Service (BaaS) platform, providing PostgreSQL for content storage, built-in authentication, storage for media assets, and Edge Functions for custom backend logic. The [`backend/prisma/schema.prisma`](../backend/prisma/schema.prisma) file confirms the use of PostgreSQL with Prisma ORM. Integrating a separate headless CMS like Strapi would introduce redundant database management, additional overhead, and unnecessary complexity.
2. **Unified Data Layer:** By using Supabase PostgreSQL, all content (articles, categories, users, comments, etc.) resides within a single, unified relational database. This simplifies data consistency, relationships, and querying.
3. **Prisma ORM Compatibility:** The existing backend already uses Prisma, which seamlessly integrates with PostgreSQL. This allows for type-safe and efficient database interactions without needing to adapt to a new ORM or data access layer for content.
4. **Built-in Features:** Supabase offers a comprehensive suite of features (Auth, Storage, Realtime, Edge Functions) that can be directly utilized for content management workflows, reducing the need for external services or custom implementations for common CMS functionalities.
5. **Scalability and Performance:** Supabase is designed for scalability, and its PostgreSQL backend can handle significant content volumes and traffic. Prisma's efficient query generation further optimizes performance.
6. **Simplified Deployment:** Leveraging Supabase as the primary backend simplifies deployment and infrastructure management, as content and application logic are tightly integrated within the Supabase ecosystem.

## 2. Supabase Content Model Setup

Content models for `articles` and `categories` will be defined directly as tables within the Supabase PostgreSQL database. These definitions are managed via Prisma schema, ensuring a single source of truth for the database structure.

### Existing Prisma Schema (`backend/prisma/schema.prisma`)

The [`schema.prisma`](../backend/prisma/schema.prisma) file already defines the `Article` and `Category` models, which align with the requirements outlined in [`TechNexus_Blog_system_design.md`](TechNexus_Blog_system_design.md).

#### Article Model (`Article` table)

```prisma
model Article {
  id           String        @id @default(cuid())
  title        String
  slug         String        @unique
  excerpt      String
  content      String
  tags         String[]      @default([])
  coverImage   String
  readingTime  Int
  status       ArticleStatus @default(DRAFT)
  isFeatured   Boolean       @default(false)
  isTopPick    Boolean       @default(false)

  authorId     String
  author       User          @relation(fields: [authorId], references: [id])

  categoryId   String
  category     Category      @relation(fields: [categoryId], references: [id])

  createdAt    DateTime      @default(now())
  updatedAt    DateTime      @updatedAt
  publishedAt  DateTime?
  comments     Comment[]
  codePlaygrounds CodePlayground[]
  learningPathArticles LearningPathArticle[]

  @@map("articles")
}
```

**Mapping to [`TechNexus_Blog_system_design.md`](TechNexus_Blog_system_design.md):**

* Most fields directly map.
* `content`: In `system_design.md`, `content` is an array of objects with `type`, `content`, and `metadata`. In `schema.prisma`, it's a single `String`. For simplicity and to leverage markdown, the `content` field in Prisma will store the full markdown content of the article. Frontend components (like `React Markdown` and `Monaco Editor`) will parse and render this string, handling different content types (text, image, code, interactive) as specified in the `system_design.md`'s frontend rendering note.
* `authorId`: References the `User` model (which implicitly acts as the `Author` in the current schema).
* `primaryCategoryId`: Mapped to `categoryId` in Prisma, representing the main category. `secondaryCategories` from `system_design.md` is not explicitly modeled as a direct relation in Prisma, but `tags` can serve a similar purpose for additional categorization or keywords.
* `meta` and `statistics` objects from `system_design.md` are now partially modeled with `views`, `likes`, `shares`, and `commentCount` fields directly in the `Article` model. `isTopPick` is present, and `topPickOrder`, `expertiseLevel`, and `format` have been added for better alignment.

#### Category Model (`Category` table)

```prisma
model Category {
  id          String    @id @default(cuid())
  name        String    @unique
  slug        String    @unique
  description String?
  image       String?
  articles    Article[]

  @@map("categories")
}
```

**Mapping to [`TechNexus_Blog_system_design.md`](TechNexus_Blog_system_design.md):**

* Most fields directly map.
* `iconUrl`: Mapped to `image` in Prisma.
* `parentCategoryId`: Not explicitly modeled in Prisma. If hierarchical categories are a strict requirement, a self-referencing relationship would need to be added to the `Category` model. For now, a flat category structure is assumed based on the current Prisma schema. `order` and `isActive` fields have been added to the `Category` model for custom sorting and activation/deactivation.

## 3. Backend API Endpoints for CRUD Operations

Instead of implementing new REST endpoints for a separate CMS, the existing **Apollo GraphQL Server** will be extended to provide CRUD (Create, Read, Update, Delete) operations for `articles` and `categories` by interacting directly with the Supabase PostgreSQL database via Prisma.

The [`backend/src/index.ts`](../backend/src/index.ts) file already sets up the Apollo Server with `PrismaClient` available in the `Context`. The [`backend/src/graphql/resolvers/`](../backend/src/graphql/resolvers/) directory will contain the logic for these operations.

### Example GraphQL Operations

#### Articles

* **Create Article:**
  * **Mutation:** `createArticle(input: ArticleInput!): Article`
  * **Resolver Logic:** The resolver for `createArticle` would use `prisma.article.create()` to insert a new article record into the `articles` table. Input validation (e.g., using Zod as per [`TechNexus_Blog_tech_stack.md`](TechNexus_Blog_tech_stack.md)) would be applied.
* **Read Articles (List/Single):**
  * **Query:** `articles(filters: ArticleFilters): ArticlePagination`
  * **Query:** `article(slug: String!): Article`
  * **Resolver Logic:** Resolvers would use `prisma.article.findMany()` for listing articles (with filtering, pagination, and sorting) and `prisma.article.findUnique()` for fetching a single article by its slug.
* **Update Article:**
  * **Mutation:** `updateArticle(id: ID!, input: ArticleInput!): Article`
  * **Resolver Logic:** The resolver for `updateArticle` would use `prisma.article.update()` to modify an existing article record.
* **Delete Article:**
  * **Mutation:** `deleteArticle(id: ID!): Boolean` (or `Article`)
  * **Resolver Logic:** The resolver for `deleteArticle` would use `prisma.article.delete()`.

#### Categories

* **Create Category:**
  * **Mutation:** `createCategory(input: CategoryInput!): Category`
  * **Resolver Logic:** The resolver would use `prisma.category.create()`.
* **Read Categories (List/Single):**
  * **Query:** `categories: [Category!]`
  * **Query:** `category(slug: String!): Category`
  * **Resolver Logic:** Resolvers would use `prisma.category.findMany()` and `prisma.category.findUnique()`.
* **Update Category:**
  * **Mutation:** `updateCategory(id: ID!, input: CategoryInput!): Category`
  * **Resolver Logic:** The resolver would use `prisma.category.update()`.
* **Delete Category:**
  * **Mutation:** `deleteCategory(id: ID!): Boolean` (or `Category`)
  * **Resolver Logic:** The resolver would use `prisma.category.delete()`.

### Implementation Details

* **GraphQL Schema (`backend/src/graphql/schema.graphql`):** The GraphQL schema will need to be updated to include the necessary `ArticleInput`, `CategoryInput` types, and the CRUD mutations/queries.
* **Resolvers (`backend/src/graphql/resolvers/`):** New resolver functions will be added or existing ones modified to handle the Prisma interactions for articles and categories.
* **Authentication & Authorization:** Existing authentication middleware in [`backend/src/index.ts`](../backend/src/index.ts) will ensure that only authorized users (e.g., `EDITOR`, `ADMIN` roles) can perform create, update, and delete operations. Prisma's capabilities can be extended with Row Level Security (RLS) in Supabase for fine-grained access control if needed.
* **Error Handling:** Robust error handling will be implemented in resolvers to manage database errors, validation failures, and unauthorized access.
