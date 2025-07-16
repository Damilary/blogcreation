# TechNexus Blog: Article Creation/Editor Workflow Requirements

## Overview

This document outlines the detailed requirements and technical specifications for implementing the Article Creation/Editor workflow in the TechNexus Blog platform. This feature will enable users to create, edit, save drafts, and publish technical articles with rich markdown content, metadata management, and image embedding capabilities.

## 1. Component Structure

### 1.1 Article Editor Page (`/articles/editor/[id]` and `/articles/editor/new`)

- **Layout Structure**:
  - Two-column layout (editor on left, preview on right) for desktop
  - Tab-based layout (switch between edit/preview) for mobile
  - Sticky header with save/publish actions and status indicator

- **Core Components**:
  - `ArticleEditorContainer`: Main wrapper component managing state and layout
  - `MarkdownEditor`: Text editor with markdown syntax support
  - `MarkdownPreview`: Real-time rendered preview of markdown content
  - `ArticleMetadataForm`: Form for title, excerpt, categories, tags
  - `FeaturedImageUploader`: Component for uploading and cropping featured images
  - `EditorToolbar`: Formatting controls and editor actions
  - `PublishModal`: Confirmation dialog with final checks before publishing
  - `DraftSaveIndicator`: Status indicator for auto-save/manual save operations

### 1.2 Article Management Page (`/dashboard/articles`)

- **Layout Structure**:
  - List/grid view of user's articles with filtering options
  - Status indicators for drafts vs published articles
  - Sort and search functionality

- **Core Components**:
  - `ArticlesList`: Tabbed interface showing drafts and published articles
  - `ArticleListItem`: Individual article entry with metadata and actions
  - `ArticleFilters`: Controls for filtering by status, category, date
  - `ArticleSearchBar`: Search functionality for user's articles
  - `CreateArticleButton`: Prominent button to start a new article

### 1.3 Shared Components

- `ImageUploader`: Reusable component for uploading and embedding images in articles
- `CategorySelector`: Multi-select dropdown for article categories
- `TagInput`: Input for adding and managing article tags
- `ConfirmationDialog`: Reusable dialog for confirming destructive actions
- `StatusBadge`: Visual indicator for article status (draft, published, etc.)

## 2. Data Models

### 2.1 Article

```typescript
interface Article {
  id: string;
  title: string;
  slug: string; // URL-friendly version of title
  content: string; // Markdown content
  excerpt: string; // Short summary
  featuredImage?: string; // URL to image
  status: 'draft' | 'published' | 'archived';
  authorId: string;
  categoryIds: string[];
  tags: string[];
  createdAt: string; // ISO date
  updatedAt: string; // ISO date
  publishedAt?: string; // ISO date, only for published articles
  readTime?: number; // Estimated read time in minutes
  viewCount?: number; // Number of views
  isDraft: boolean; // Quick check for draft status
}
```

### 2.2 ArticleDraft (Extended from Article)

```typescript
interface ArticleDraft extends Article {
  originalArticleId?: string; // If this is a draft of an existing article
  lastSavedAt: string; // ISO date of last auto/manual save
  isAutoSaved: boolean; // Whether last save was automatic
  publishScheduledFor?: string; // ISO date for scheduled publishing
}
```

### 2.3 ArticleImage

```typescript
interface ArticleImage {
  id: string;
  articleId: string;
  url: string;
  alt: string;
  caption?: string;
  width: number;
  height: number;
  createdAt: string;
}
```

### 2.4 ArticleCategory

```typescript
interface ArticleCategory {
  id: string;
  name: string;
  slug: string;
  description?: string;
  parentCategoryId?: string; // For hierarchical categories
}
```

## 3. User Flows

### 3.1 Creating a New Article

1. User navigates to `/dashboard/articles` and clicks "Create New Article"
2. System redirects to `/articles/editor/new` with empty editor
3. User enters title, which triggers auto-generation of slug (editable)
4. User writes content in markdown editor, seeing real-time preview
5. System auto-saves draft every 30 seconds if changes detected
6. User can manually save draft at any point
7. User adds metadata: excerpt, categories, tags
8. User uploads featured image (optional)
9. User can either:
   - Continue editing (draft remains saved)
   - Publish immediately (changes status to published)
   - Schedule for publishing (sets future publish date)
   - Exit editor (draft is preserved)

### 3.2 Editing an Existing Article

1. User navigates to `/dashboard/articles` and selects article to edit
2. System loads article data into editor at `/articles/editor/[id]`
3. For published articles, system creates a working draft
4. User makes changes to content and/or metadata
5. System auto-saves changes to draft
6. User can:
   - Save changes to draft
   - Publish changes (updates the published article)
   - Discard changes (reverts to published version)

### 3.3 Managing Drafts

1. User navigates to `/dashboard/articles` and views "Drafts" tab
2. System displays all saved drafts with last modified date
3. User can:
   - Continue editing a draft
   - Delete a draft
   - Create a new article from draft
   - Publish a draft directly

### 3.4 Publishing Workflow

1. User clicks "Publish" in the editor
2. System displays publish confirmation modal with:
   - Preview of how article will appear
   - SEO suggestions
   - Category/tag confirmation
   - Featured image check
3. User confirms publish action
4. System changes article status to "published"
5. System generates notification of successful publish
6. User is redirected to the published article view

### 3.5 Embedding Images

1. User clicks image icon in editor toolbar
2. System displays image upload dialog
3. User either:
   - Uploads a new image from device
   - Selects previously uploaded image
   - Enters URL of external image
4. User adds alt text and optional caption
5. System inserts markdown image code at cursor position
6. Image appears in preview pane
7. User can resize or align image using markdown syntax

## 4. API Contract Suggestions

### 4.1 GraphQL Queries

```graphql
# Get user's articles with filtering
query GetUserArticles($status: ArticleStatus, $categoryId: ID, $search: String, $limit: Int, $offset: Int) {
  userArticles(status: $status, categoryId: $categoryId, search: $search, limit: $limit, offset: $offset) {
    id
    title
    excerpt
    status
    featuredImage
    createdAt
    updatedAt
    publishedAt
    readTime
    viewCount
    categories {
      id
      name
    }
  }
}

# Get single article for editing
query GetArticleForEditing($id: ID!) {
  article(id: $id) {
    id
    title
    slug
    content
    excerpt
    featuredImage
    status
    categories {
      id
      name
    }
    tags
    createdAt
    updatedAt
    publishedAt
    isDraft
  }
}

# Get article categories for selection
query GetArticleCategories {
  categories {
    id
    name
    slug
    description
    parentCategoryId
  }
}
```

### 4.2 GraphQL Mutations

```graphql
# Create new article draft
mutation CreateArticleDraft($input: ArticleDraftInput!) {
  createArticleDraft(input: $input) {
    id
    title
    slug
    lastSavedAt
  }
}

# Update article draft
mutation UpdateArticleDraft($id: ID!, $input: ArticleDraftInput!) {
  updateArticleDraft(id: $id, input: $input) {
    id
    title
    slug
    lastSavedAt
    isAutoSaved
  }
}

# Publish article
mutation PublishArticle($id: ID!, $input: PublishArticleInput!) {
  publishArticle(id: $id, input: $input) {
    id
    title
    slug
    status
    publishedAt
  }
}

# Upload article image
mutation UploadArticleImage($articleId: ID!, $file: Upload!, $alt: String!, $caption: String) {
  uploadArticleImage(articleId: $articleId, file: $file, alt: $alt, caption: $caption) {
    id
    url
    alt
    caption
    width
    height
  }
}

# Delete article draft
mutation DeleteArticleDraft($id: ID!) {
  deleteArticleDraft(id: $id) {
    success
    message
  }
}
```

## 5. Dependencies & Technical Considerations

### 5.1 Markdown Editor Libraries

**Recommended Options:**
- **React-SimpleMDE**: Simple yet powerful markdown editor with good customization
- **React-Markdown**: For rendering the preview pane
- **Remark/Rehype**: For markdown processing and extensions

**Key Features Needed:**
- Syntax highlighting
- Toolbar customization
- Keyboard shortcuts
- Paste handling (especially for images)
- Line numbers and focus mode

### 5.2 Image Handling

**Upload Strategy:**
- Client-side image resizing/compression before upload
- Support for drag-and-drop and clipboard paste
- Progress indicator for uploads
- Temporary storage for draft images
- Permanent storage only when article is published

**Technical Considerations:**
- Image optimization for different device sizes
- Lazy loading for images in preview
- Fallback for failed image loads
- Accessibility requirements (alt text enforcement)

### 5.3 Draft Saving Strategy

**Auto-save Implementation:**
- Debounced saves (30-second interval after changes)
- Visual indicator of save status
- Local storage backup in case of connection issues
- Conflict resolution if multiple tabs are editing

**Recovery Options:**
- Version history (last 5 auto-saves)
- Restore from local backup if server save fails
- Conflict resolution UI if multiple editors

### 5.4 Performance Considerations

- Lazy loading of editor components
- Throttling of preview rendering for large documents
- Pagination for article lists
- Optimistic UI updates for save operations
- Caching of article data for faster loads

### 5.5 Accessibility Requirements

- Keyboard navigation throughout editor
- Screen reader compatibility for editor and preview
- Color contrast compliance
- Focus management
- Alternative text enforcement for images

## 6. Mock Implementation Strategy

For the initial implementation, we'll create:

1. **Mock Article Data**: Pre-populated articles and drafts in various states
2. **Mock GraphQL Handler**: Extended to support all article operations
3. **Local Storage Integration**: For draft backup and recovery
4. **Simulated Delays**: To test loading states and auto-save indicators

The mock implementation will simulate all backend operations while maintaining state during the user session, allowing for complete testing of the workflow.

## 7. Future Enhancements (Post-MVP)

- Collaborative editing
- Comment integration within editor for review
- Version history and comparison
- Advanced SEO tools and recommendations
- Rich embeds (code sandboxes, videos, tweets)
- AI-assisted writing tools
- Analytics dashboard for published articles

## 8. Success Metrics

The Article Creation/Editor implementation will be considered successful when:

1. Users can create, edit, and publish articles with a smooth workflow
2. Markdown content is correctly rendered in preview and published view
3. Drafts are reliably saved and recovered
4. Images can be uploaded and embedded with proper formatting
5. The editor is responsive across device sizes
6. All accessibility requirements are met

## Next Steps

1. Review and approve these requirements
2. Create wireframes for key screens
3. Implement core editor components
4. Develop draft saving functionality
5. Add metadata and publishing workflow
6. Implement image upload and embedding
7. Test and refine the complete workflow
