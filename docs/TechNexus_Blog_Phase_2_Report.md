# TechNexus Blog Website: Phase 2 Report - System Design & Architecture

This report details the outcomes of Phase 2: System Design & Architecture for the TechNexus Blog Website, based on the `TechNexus_Blog_system_design.md` and `TechNexus_Blog_tech_stack.md` documents.

## 1. Detailed System Architecture

### Microservices Architecture
The system is designed with a microservices approach, separating concerns into distinct services:
- **Authentication Service**: Handles user registration, login, logout, token management, and OAuth integrations.
- **Content Service**: Manages articles, categories, tags, and media assets.
- **User Service**: Manages user profiles, preferences, and saved content.
- **Search Service**: Provides advanced content search and indexing using Elasticsearch.
- **Analytics Service**: Collects and processes user behavior and performance metrics.
- **Recommendation Service**: Delivers personalized content recommendations.
- **Notification Service**: Manages email and in-app notifications.
- **Interactive Content Service**: Supports interactive elements like code playgrounds.

### Interactions
- **Client Layer**: Web Application (Next.js) and Mobile Web interface interact with the API Gateway via HTTP/HTTPS.
- **API Gateway Layer**: Acts as a single entry point, routing requests to either GraphQL or REST endpoints, which then forward to the appropriate microservices.
- **Services Layer**: Microservices communicate with each other and with the Data Layer.
- **Data Layer**: Services interact with MongoDB (for content and user data), Elasticsearch (for search indexing), Redis (for caching and sessions), and InfluxDB (for analytics time-series data).
- **External Services**: CDN for media assets, Email Service for notifications, OAuth Providers for authentication, and Analytics Platforms for comprehensive tracking.

## 2. Database Schema Design

The MongoDB schema design is flexible and supports various content types and relationships:

### Users Collection
```javascript
{
  _id: ObjectId,
  username: String,
  email: String,
  passwordHash: String,
  firstName: String,
  lastName: String,
  role: String, // READER, CONTRIBUTOR, EDITOR, ADMIN
  createdAt: Date,
  updatedAt: Date,
  preferences: {
    favoriteCategories: [String], // Category IDs
    savedArticles: [String], // Article IDs
    followedAuthors: [String], // Author IDs
    preferredLevel: String, // BEGINNER, INTERMEDIATE, ADVANCED
    darkModeEnabled: Boolean,
    emailNotificationsEnabled: Boolean,
    preferredFormats: [String] // ARTICLE, TUTORIAL, etc.
  }
}
```

### Authors Collection
```javascript
{
  _id: ObjectId,
  userId: ObjectId, // Reference to Users collection
  bio: String,
  expertise: String,
  specializations: [String],
  avatarUrl: String,
  socialLinks: [
    { platform: String, url: String }
  ],
  joinedDate: Date
}
```

### Articles Collection
```javascript
{
  _id: ObjectId,
  title: String,
  slug: String,
  excerpt: String,
  content: [
    {
      type: String, // text, image, code, video, interactive
      content: String,
      metadata: Object // Specific to content type
    }
  ],
  authorId: ObjectId, // Reference to Authors collection
  primaryCategoryId: ObjectId, // Reference to Categories collection
  secondaryCategories: [ObjectId], // References to Categories collection
  tags: [String],
  expertiseLevel: String, // BEGINNER, INTERMEDIATE, ADVANCED
  format: String, // ARTICLE, TUTORIAL, CASE_STUDY, etc.
  publishedDate: Date,
  updatedDate: Date,
  readTimeMinutes: Number,
  meta: {
    title: String,
    description: String,
    keywords: [String],
    ogImage: String
  },
  statistics: {
    views: Number,
    uniqueVisitors: Number,
    likes: Number,
    shares: Number,
    bookmarks: Number,
    commentCount: Number,
    averageTimeOnPage: Number,
    scrollDepthPercentage: Number
  },
  isTopPick: Boolean, // Added for "Top Picks" section
  topPickOrder: Number, // Added for explicit ordering of "Top Picks"
  status: String // DRAFT, REVIEW, PUBLISHED, ARCHIVED
}
```

### Categories Collection
```javascript
{
  _id: ObjectId,
  name: String,
  slug: String,
  description: String,
  iconUrl: String,
  parentCategoryId: ObjectId, // Reference to Categories collection for hierarchy
  order: Number, // For custom sorting
  isActive: Boolean
}
```

### Comments Collection
```javascript
{
  _id: ObjectId,
  articleId: ObjectId, // Reference to Articles collection
  userId: ObjectId, // Reference to Users collection
  content: String,
  parentCommentId: ObjectId, // For threaded comments
  createdAt: Date,
  isApproved: Boolean,
  upvotes: Number,
  downvotes: Number,
  reports: Number // For moderation
}
```

### LearningPaths Collection
```javascript
{
  _id: ObjectId,
  title: String,
  slug: String,
  description: String,
  startLevel: String, // BEGINNER, INTERMEDIATE, ADVANCED
  endLevel: String, // BEGINNER, INTERMEDIATE, ADVANCED
  articles: [
    { articleId: ObjectId, order: Number, isRequired: Boolean }
  ],
  estimatedHours: Number,
  createdBy: ObjectId, // Reference to Users collection
  createdAt: Date,
  updatedAt: Date,
  isActive: Boolean
}
```

### Newsletters Collection
```javascript
{
  _id: ObjectId,
  name: String,
  description: String,
  categories: [ObjectId], // References to Categories collection
  level: String, // BEGINNER, INTERMEDIATE, ADVANCED
  subscriberCount: Number,
  lastSentDate: Date,
  createdAt: Date,
  updatedAt: Date
}
```

## 3. API Design (REST & GraphQL)

### REST API Endpoints
A comprehensive set of RESTful endpoints are defined for various functionalities:
- **Authentication**: `/api/auth/register`, `/api/auth/login`, `/api/auth/logout`, `/api/auth/me`, `/api/auth/refresh-token`, `/api/auth/forgot-password`, `/api/auth/reset-password`, `/api/auth/oauth/:provider`.
- **Users & Authors**: `/api/users/:id`, `/api/users/:id/preferences`, `/api/authors/:id`, `/api/authors/:id/articles`.
- **Articles & Content**: `/api/articles`, `/api/articles/featured`, `/api/articles/:slug`, `/api/articles/top-picks`, `/api/articles/:id/related`, `/api/articles/:id/comments`, `/api/articles/:id/like`, `/api/articles/:id/save`, `/api/articles/:id/view`. (Includes endpoints for creating, updating, and deleting articles for authenticated users).
- **Categories & Tags**: `/api/categories`, `/api/categories/:slug`, `/api/categories/:slug/articles`, `/api/tags`, `/api/tags/:slug/articles`.
- **Search**: `/api/search`, `/api/search/suggestions`.
- **Interactive Content**: `/api/code-playgrounds/:id`, `/api/code-playgrounds/:id/execute`, `/api/code-playgrounds/:id/save`, `/api/interactive/:id`, `/api/interactive/:id/state`.
- **Learning**: `/api/learning-paths`, `/api/learning-paths/:slug`, `/api/learning-paths/:id/enroll`, `/api/learning-paths/:id/progress`.
- **Newsletters**: `/api/newsletters`, `/api/newsletters/subscribe`, `/api/newsletters/unsubscribe`.
- **Admin**: `/api/admin/dashboard`, `/api/admin/users`, `/api/admin/content`, `/api/admin/comments`.

### GraphQL Schema
The GraphQL schema provides flexible data fetching and mutations:
- **Query Types**: `articles`, `article`, `topPicksArticles`, `featuredArticles`, `categories`, `category`, `me`, `author`, `search`, `recommendedArticles`, `similarArticles`, `personalizedFeed`.
- **Mutation Types**: `login`, `register`, `updateUserProfile`, `updateUserPreferences`, `createComment`, `likeArticle`, `saveArticle`, `createArticle`, `updateArticle`.
- **Key Types**: `Article`, `ContentBlock`, `Category`, `User`, `Author` are defined with their respective fields and relationships.

### Security & Rate Limiting
- **Authentication**: JWT-based authentication with short expiry and refresh tokens. OAuth integration for social logins.
- **Authorization**: Role-based access control (RBAC) for content management.
- **API Security**: Rate limiting to prevent abuse, CORS configuration, input validation and sanitization, prevention of common vulnerabilities (XSS, CSRF, injection).
- **Data Protection**: HTTPS enforcement, data encryption at rest, PII handling in compliance with GDPR and CCPA, regular security audits.

## 4. Technology Stack Selection

### Frontend Technologies
- **Core Libraries**: Next.js (v14+), React (v18+), TypeScript (v5+), Tailwind CSS (v3+).
- **State Management & Data Fetching**: React Query (v5+), Zustand, SWR.
- **Interactive Components**: Monaco Editor, Chart.js, Framer Motion, React Markdown, Prism.js.
- **Form Management & Validation**: React Hook Form, Zod.

### Backend Technologies
- **Core Framework**: Node.js (v18+ LTS), Express.js, Apollo Server, TypeScript.
- **Database & ORM**: MongoDB, Mongoose, Elasticsearch, Redis, InfluxDB.
- **Authentication & Security**: Passport.js, JWT, bcrypt, helmet, rate-limiter-flexible.
- **Job Processing**: Bull MQ, node-scheduler.

### Infrastructure & DevOps
- **Containerization & Orchestration**: Docker, Kubernetes, Helm.
- **CI/CD Pipeline**: GitHub Actions / GitLab CI for automated testing (Jest, Cypress), code quality checks (ESLint, Prettier), and performance monitoring (Lighthouse CI).
- **Monitoring & Observability**: Prometheus, Grafana, Sentry, OpenTelemetry.

### Third-party Integrations
- **Email Notifications**: SendGrid / Mailgun.
- **Authentication Providers**: Google OAuth, GitHub OAuth, Instagram OAuth, Facebook OAuth, X (formerly Twitter) OAuth, LinkedIn OAuth.
- **Analytics & Monitoring**: Google Analytics, Mixpanel, DataDog / New Relic.
- **Content Delivery Optimization**: Cloudflare / AWS CloudFront (CDN), Cloudinary (Image Optimization), AWS S3 / Google Cloud Storage (Object Storage).