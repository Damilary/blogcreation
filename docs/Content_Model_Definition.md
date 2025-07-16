# Content Model Definition for TechNexus Blog

This document defines the core content models for the TechNexus Blog, specifically focusing on `Article` and `Category`, as implemented in the Prisma schema (`backend/prisma/schema.prisma`). These models form the foundation of the headless CMS.

## 1. Article Model

The `Article` model represents a blog post or a piece of content. It includes fields necessary for content display, search, categorization, and author information.

**Prisma Schema Definition (from `backend/prisma/schema.prisma`):**

```prisma
model Article {
  id           String        @id @default(cuid())
  title        String
  slug         String        @unique
  excerpt      String
  content      String
  tags         String[]      @default([]) // Added for search and filtering
  coverImage   String        // Added for article cards
  readingTime  Int           // Added for article metadata
  status       ArticleStatus @default(DRAFT)
  isFeatured   Boolean       @default(false) // Added for featured query
  isTopPick    Boolean       @default(false) // Added for top picks query
  topPickOrder Int?          // Added for explicit ordering of "Top Picks"
  expertiseLevel ExpertiseLevel? // Added for content filtering
  format       ContentFormat?  // Added for content type filtering
  views        Int           @default(0) // Added for article statistics
  likes        Int           @default(0) // Added for article statistics
  shares       Int           @default(0) // Added for article statistics
  commentCount Int           @default(0) // Added for article statistics

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

enum ArticleStatus {
  DRAFT
  PUBLISHED
  ARCHIVED
}
```

**Field Descriptions:**

- `id`: Unique identifier for the article (CUID).
- `title`: The main title of the article.
- `slug`: A URL-friendly unique identifier for the article (e.g., `how-to-learn-prisma`).
- `excerpt`: A short summary or preview of the article content.
- `topPickOrder`: Optional integer for explicit ordering of "Top Picks" articles.
- `expertiseLevel`: Categorizes the article's difficulty (BEGINNER, INTERMEDIATE, ADVANCED).
- `format`: Specifies the content type (ARTICLE, TUTORIAL, CASE_STUDY, VIDEO, INTERACTIVE).
- `views`: Number of times the article has been viewed.
- `likes`: Number of likes the article has received.
- `shares`: Number of times the article has been shared.
- `commentCount`: Total number of comments on the article.
- `content`: The full content of the article, typically in Markdown or a rich text format.
- `tags`: An array of strings for keywords, used for search and filtering.
- `coverImage`: URL to the main image associated with the article.
- `readingTime`: Estimated reading time in minutes.
- `status`: Current publication status of the article (`DRAFT`, `PUBLISHED`, `ARCHIVED`).
- `isFeatured`: Boolean flag to mark articles for prominent display (e.g., on a homepage carousel).
- `isTopPick`: Boolean flag to mark articles as editor's top picks.
- `topPickOrder`: Optional integer for explicit ordering of "Top Picks" articles.
- `expertiseLevel`: Categorizes the article's difficulty (BEGINNER, INTERMEDIATE, ADVANCED).
- `format`: Specifies the content type (ARTICLE, TUTORIAL, CASE_STUDY, VIDEO, INTERACTIVE).
- `views`: Number of times the article has been viewed.
- `likes`: Number of likes the article has received.
- `shares`: Number of times the article has been shared.
- `commentCount`: Total number of comments on the article.
- `authorId`: Foreign key linking to the `User` model (the author of the article).
- `author`: Relation field to the `User` model, allowing direct access to author details.
- `categoryId`: Foreign key linking to the `Category` model.
- `category`: Relation field to the `Category` model, allowing direct access to category details.
- `createdAt`: Timestamp when the article was created.
- `updatedAt`: Timestamp of the last update to the article.
- `publishedAt`: Optional timestamp when the article was published.
- `comments`: Relation to `Comment` model, representing comments on the article.
- `codePlaygrounds`: Relation to `CodePlayground` model, for interactive code snippets within the article.
- `learningPathArticles`: Relation to `LearningPathArticle` model, for articles included in learning paths.

## 2. Category Model

The `Category` model is used to organize articles into logical groups.

**Prisma Schema Definition (from `backend/prisma/schema.prisma`):**

```prisma
model Category {
  id          String    @id @default(cuid())
  name        String    @unique
  slug        String    @unique
  description String?
  image       String?   // Renamed from iconUrl and made optional for showcase
  articles    Article[] // Relation to articles
  order       Int?      // For custom sorting of categories
  isActive    Boolean   @default(true) // Flag to activate/deactivate categories

  @@map("categories")
}
```

**Field Descriptions:**

- `id`: Unique identifier for the category (CUID).
- `order`: Optional integer for custom sorting of categories.
- `isActive`: Boolean flag to control the visibility or active status of a category.
- `name`: The name of the category (e.g., "Web Development", "Cloud Computing").
- `slug`: A URL-friendly unique identifier for the category (e.g., `web-development`).
- `description`: Optional brief description of the category.
- `image`: Optional URL to an image representing the category (e.g., for category showcase sections).
- `articles`: Relation to `Article` model, representing articles belonging to this category.
- `order`: Optional integer for custom sorting of categories.
- `isActive`: Boolean flag to control the visibility or active status of a category.

## 3. Other Related Models (Briefly)

While `Article` and `Category` are primary content models, they interact with other models:

- **`User`**: Represents authors and users, including roles (`UserRole` enum).
- **`Comment`**: Represents user comments on articles, with `CommentStatus` enum.
- **`CodePlayground`**: Defines interactive code snippets.
- **`LearningPath`**: Organizes articles into structured learning paths.
- **`LearningPathArticle`**: Junction table for many-to-many relationship between `LearningPath` and `Article`.
- **`UserLearningProgress`**: Tracks user progress in learning paths.
- **`UserCodeProgress`**: Tracks user progress in code playgrounds.

These models collectively form the data structure for the TechNexus Blog's content.
