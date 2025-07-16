# TechNexus Blog: Article Editor Integration & QA Guide

## For Developers

### Component Integration

#### Key Components
- `ArticleEditorContainer`: Main container component that orchestrates all editor functionality
- `MarkdownEditor`: Wrapper around React-SimpleMDE with theme support
- `MarkdownPreview`: Renders markdown content using React-Markdown
- `ArticleMetadataForm`: Handles title, excerpt, categories, and tags
- `PublishPanel`: Manages the publishing workflow and validation
- `ImageUploader`: Handles image upload, preview, and embedding

#### Integration Points

**1. Route Integration**
```tsx
// Already implemented:
// /src/app/editor/page.tsx - For new articles
// /src/app/editor/[draftId]/page.tsx - For editing existing drafts

// Add navigation to these routes from:
// - User profile page
// - Article listing page
// - Article detail page (for editing)
```

**2. Data Layer Integration**
```tsx
// The useArticleDraft hook handles all draft operations
import { useArticleDraft } from '@/hooks/useArticleDraft';

// For listing user's drafts (to be implemented or integrated)
import { useUserDrafts } from '@/hooks/useUserDrafts';
```

**3. Authentication Integration**
```tsx
// Ensure editor routes are protected
// Example middleware or route guard:
const EditorPage = () => {
  const { user, isLoading } = useAuth();
  
  if (isLoading) return <LoadingSpinner />;
  if (!user) {
    // Redirect to login
    redirect('/login?returnUrl=/editor');
  }
  
  return <ArticleEditorContainer />;
};
```

### API Integration

#### GraphQL Mutations

**1. Draft Operations**
```graphql
# Create Draft
mutation CreateArticleDraft($input: ArticleDraftInput!) {
  createArticleDraft(input: $input) {
    id
    title
    content
    # other fields...
  }
}

# Update Draft
mutation UpdateArticleDraft($id: ID!, $input: ArticleDraftInput!) {
  updateArticleDraft(id: $id, input: $input) {
    id
    title
    content
    # other fields...
  }
}
```

**2. Publishing**
```graphql
mutation PublishArticle($id: ID!, $input: PublishArticleInput!) {
  publishArticle(id: $id, input: $input) {
    id
    title
    slug
    status
    publishedAt
  }
}
```

**3. Image Upload**
```graphql
# Note: In production, this would likely use a file upload API
# rather than a GraphQL mutation directly
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
```

#### Mock to Real API Transition

1. **Current Mock Implementation**:
   - Located in `/src/lib/api/mockData.ts`
   - Simulates API responses and maintains in-memory state
   - Handles validation and error scenarios

2. **Transition Strategy**:
   - Replace mock handler calls with real API calls
   - Maintain the same response structure
   - Update error handling to match real API error formats

3. **Key Considerations**:
   - Real API may have different validation rules
   - Authentication/authorization needs to be integrated
   - File uploads require multipart form handling

### Testing Guidelines

#### Unit Tests

Focus areas for component testing:
- Form validation in `ArticleMetadataForm`
- State management in `ArticleEditorContainer`
- Markdown rendering in `MarkdownPreview`

Example test structure:
```tsx
describe('ArticleMetadataForm', () => {
  it('should validate required fields', () => {
    // Test validation logic
  });
  
  it('should call onChange with updated metadata', () => {
    // Test data flow
  });
});
```

#### Integration Tests

Key flows to test:
1. Create new draft → Save → Publish
2. Edit existing draft → Update metadata → Save
3. Upload image → Insert into content → Preview

#### Edge Cases

Critical scenarios to test:
- Network failure during save operation
- Browser refresh/navigation during editing
- Invalid image uploads (wrong format, too large)
- Validation error handling
- Draft recovery after crashes

## For Content Creators

### Getting Started

#### Accessing the Editor

1. **Creating a New Article**:
   - Navigate to your profile page
   - Click "New Article" button
   - Or directly visit `/editor`

2. **Editing Existing Drafts**:
   - From your profile, find the draft in your articles list
   - Click "Edit" on any draft
   - Or visit `/editor/[draft-id]`

#### Editor Interface

The editor is organized into four main tabs:

1. **Write**: The main markdown editor with formatting toolbar
2. **Preview**: See how your article will appear when published
3. **Metadata**: Manage title, excerpt, categories, and tags
4. **Publish**: Review, validate, and publish your article

### Writing Content

#### Markdown Basics

```markdown
# Heading 1
## Heading 2

**Bold text**
*Italic text*

[Link text](https://example.com)

- List item 1
- List item 2

1. Numbered item 1
2. Numbered item 2

> Blockquote text

`inline code`

```code block```
```

#### Adding Images

1. In the Write tab, click "Add Image"
2. Select an image file (JPG, PNG, or WebP, max 2MB)
3. Add required alt text and optional caption
4. Click "Insert Image"
5. The image will appear in your content and preview

### Managing Drafts

#### Auto-Save

- Your work is automatically saved every 30 seconds
- A save indicator shows the status (saving, saved, error)
- You can manually save at any time with the Save button

#### Draft Recovery

If your browser crashes or you accidentally navigate away:
1. Return to the editor
2. Your draft will be automatically recovered
3. You'll see a notification that changes were recovered

### Publishing Workflow

#### Before Publishing

1. Complete all required metadata:
   - Title (at least 3 characters)
   - Excerpt (at least 10 characters)
   - At least one category selected

2. Ensure your content is complete (at least 100 characters)

3. Preview your article to check formatting

#### Publishing Process

1. Navigate to the Publish tab
2. Review any validation warnings
3. Click "Publish Article"
4. Confirm in the dialog
5. After successful publishing, you'll be redirected to your published article

## QA Checklist

### Functionality Testing

- [ ] Editor loads correctly for new articles
- [ ] Editor loads and displays existing draft content
- [ ] Markdown formatting tools work as expected
- [ ] Preview renders markdown correctly
- [ ] Auto-save triggers after content changes
- [ ] Manual save works for both new and existing drafts
- [ ] Draft recovery works after simulated crash/refresh
- [ ] Metadata form saves all fields correctly
- [ ] Category selection loads and works
- [ ] Tag management allows adding/removing tags
- [ ] Image upload accepts valid files and rejects invalid ones
- [ ] Image embedding inserts correct markdown
- [ ] Publishing validation catches incomplete submissions
- [ ] Successful publish redirects to the article

### UI/UX Testing

- [ ] Responsive layout works on mobile devices
- [ ] Dark mode support functions correctly
- [ ] Loading states are clearly indicated
- [ ] Error messages are clear and helpful
- [ ] Success confirmations are visible
- [ ] Tab navigation is intuitive
- [ ] Form validation provides clear feedback

### Accessibility Testing

- [ ] Keyboard navigation works throughout the editor
- [ ] Screen reader compatibility for form elements
- [ ] Alt text is required for images
- [ ] Color contrast meets WCAG standards
- [ ] ARIA attributes are correctly implemented

### Performance Testing

- [ ] Editor initializes quickly
- [ ] Preview updates are performant
- [ ] Large markdown documents handle smoothly
- [ ] Image uploads process efficiently

## Known Limitations & Workarounds

1. **Image Management**:
   - No gallery or previously uploaded image browsing
   - Workaround: Keep images organized locally before uploading

2. **Rich Media**:
   - No support for embedded videos or interactive elements
   - Workaround: Link to external media

3. **Collaborative Editing**:
   - No real-time collaboration features
   - Workaround: Coordinate editing times between authors

4. **Version History**:
   - Limited to current draft only
   - Workaround: Manually save important versions as separate drafts

5. **SEO Tools**:
   - No built-in SEO analysis
   - Workaround: Use external SEO tools to analyze content before publishing
