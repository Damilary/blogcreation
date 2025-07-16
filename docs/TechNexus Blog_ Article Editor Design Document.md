# TechNexus Blog: Article Editor Design Document

## 1. Component Structure & Hierarchy

### 1.1 Component Hierarchy Diagram

```
ArticleEditorContainer
├── EditorHeader
│   ├── ArticleTitleInput
│   ├── EditorStatusIndicator
│   └── EditorActionButtons
├── EditorMainContent
│   ├── EditorSidebar (desktop only)
│   │   ├── ArticleMetadataPanel
│   │   │   ├── CategorySelector
│   │   │   ├── TagInput
│   │   │   └── ExcerptTextarea
│   │   ├── FeaturedImagePanel
│   │   │   └── ImageUploader
│   │   └── PublishPanel
│   │       ├── StatusSelector
│   │       ├── VisibilitySelector
│   │       └── PublishButton
│   └── EditorWorkspace
│       ├── EditorToolbar
│       │   ├── FormattingControls
│       │   ├── InsertControls
│       │   └── ViewControls
│       ├── TabNavigation (mobile only)
│       ├── MarkdownEditor
│       └── MarkdownPreview
└── EditorFooter
    ├── WordCount
    ├── ReadTimeEstimate
    └── LastSavedIndicator

ArticleManagementPage
├── ArticleManagementHeader
│   ├── CreateArticleButton
│   └── ArticleSearchBar
├── ArticleFilterBar
│   ├── StatusFilter
│   ├── CategoryFilter
│   └── DateFilter
├── ArticlesTabs
│   ├── DraftsTab
│   └── PublishedTab
└── ArticlesList
    └── ArticleListItem (multiple)
        ├── ArticleMetaInfo
        ├── StatusBadge
        └── ArticleActions
```

### 1.2 Component Responsibilities

#### Editor Components

**ArticleEditorContainer**
- Top-level component for the editor page
- Manages overall editor state and layout
- Handles responsive layout switching between desktop/mobile views
- Coordinates auto-save functionality

**EditorHeader**
- Contains title input and primary action buttons
- Displays current editor status (saving, saved, etc.)
- Sticky positioning at top of page

**EditorMainContent**
- Manages layout of main editing area
- Handles sidebar visibility on desktop
- Contains editor workspace

**EditorSidebar**
- Contains metadata, featured image, and publish panels
- Collapsible on desktop
- Moves to tabs/accordion on mobile

**EditorWorkspace**
- Contains the markdown editor and preview components
- Manages split view on desktop
- Handles tab switching on mobile

**MarkdownEditor**
- Wrapper around chosen markdown editor library
- Handles text input and formatting
- Manages cursor position and selection

**MarkdownPreview**
- Renders markdown content as HTML
- Updates in real-time as content changes
- Handles image rendering and responsive layout

#### Management Components

**ArticleManagementPage**
- Top-level component for article management
- Handles filtering and search state
- Manages tab selection

**ArticlesList**
- Renders list of articles based on filters
- Handles pagination
- Manages empty states

**ArticleListItem**
- Displays individual article summary
- Shows status and actions
- Handles click events for editing

## 2. State Management

### 2.1 State Structure

```typescript
// Editor Page State
interface EditorState {
  // Core article data
  article: ArticleDraft;
  
  // UI state
  isLoading: boolean;
  isSaving: boolean;
  lastSavedAt: string | null;
  isAutoSaved: boolean;
  activeView: 'edit' | 'preview' | 'split';
  sidebarVisible: boolean;
  activeTab: 'content' | 'metadata' | 'publish';
  
  // Validation state
  errors: {
    title?: string;
    content?: string;
    excerpt?: string;
    featuredImage?: string;
    categories?: string;
  };
  
  // Image upload state
  uploadingImages: {
    [key: string]: {
      progress: number;
      status: 'uploading' | 'success' | 'error';
      error?: string;
    }
  };
}

// Article Management Page State
interface ArticleManagementState {
  articles: {
    drafts: ArticleSummary[];
    published: ArticleSummary[];
  };
  filters: {
    status: ArticleStatus | 'all';
    category: string | null;
    search: string;
    dateRange: [Date | null, Date | null];
  };
  pagination: {
    currentPage: number;
    totalPages: number;
    totalItems: number;
  };
  isLoading: boolean;
  activeTab: 'drafts' | 'published';
}
```

### 2.2 State Management Approach

For the article editor, we'll use a combination of:

1. **React Context**: Create an `ArticleEditorContext` to manage the editor state and provide it to all child components
2. **React Query**: For API interactions (fetching article data, saving drafts, publishing)
3. **Local Component State**: For UI-specific state that doesn't need to be shared

This approach allows for:
- Centralized state management for the editor
- Optimized API interactions with caching and background updates
- Isolation of UI concerns to specific components

## 3. Data Flow

### 3.1 Editor Data Flow

```
┌─────────────────┐         ┌─────────────────┐         ┌─────────────────┐
│                 │         │                 │         │                 │
│  User Actions   │ ──────> │  Editor State   │ ──────> │  API Requests   │
│                 │         │                 │         │                 │
└─────────────────┘         └─────────────────┘         └─────────────────┘
        │                           ▲                           │
        │                           │                           │
        │                           │                           │
        │                           │                           │
        ▼                           │                           ▼
┌─────────────────┐         ┌─────────────────┐         ┌─────────────────┐
│                 │         │                 │         │                 │
│ UI Components   │ <────── │ State Updates   │ <────── │  API Responses  │
│                 │         │                 │         │                 │
└─────────────────┘         └─────────────────┘         └─────────────────┘
```

1. **User Actions**: User types in editor, changes metadata, uploads images, etc.
2. **Editor State**: Updates based on user actions
3. **API Requests**: Triggered by state changes (auto-save) or explicit user actions (publish)
4. **API Responses**: Update state with server data
5. **State Updates**: Propagate changes to all components
6. **UI Components**: Re-render based on updated state

### 3.2 Auto-Save Flow

```
┌─────────────────┐         ┌─────────────────┐         ┌─────────────────┐
│                 │         │                 │         │                 │
│  Content Edit   │ ──────> │  Debounce       │ ──────> │  Save Trigger   │
│                 │         │  (30 seconds)   │         │                 │
└─────────────────┘         └─────────────────┘         └─────────────────┘
                                                                │
                                                                │
                                                                ▼
┌─────────────────┐         ┌─────────────────┐         ┌─────────────────┐
│                 │         │                 │         │                 │
│  Local Backup   │ <────── │  Save Draft     │ ──────> │  API Request    │
│                 │         │                 │         │                 │
└─────────────────┘         └─────────────────┘         └─────────────────┘
                                                                │
                                                                │
                                                                ▼
┌─────────────────┐         ┌─────────────────┐         ┌─────────────────┐
│                 │         │                 │         │                 │
│  UI Indicator   │ <────── │  Update State   │ <────── │  API Response   │
│                 │         │                 │         │                 │
└─────────────────┘         └─────────────────┘         └─────────────────┘
```

## 4. Data Models (TypeScript Interfaces)

### 4.1 Core Data Models

```typescript
// Base Article interface
interface Article {
  id: string;
  title: string;
  slug: string;
  content: string;
  excerpt: string;
  featuredImage?: {
    url: string;
    alt: string;
    width: number;
    height: number;
  };
  status: 'draft' | 'published' | 'archived';
  authorId: string;
  author?: {
    id: string;
    name: string;
    avatar?: string;
  };
  categoryIds: string[];
  categories?: Array<{
    id: string;
    name: string;
    slug: string;
  }>;
  tags: string[];
  createdAt: string;
  updatedAt: string;
  publishedAt?: string;
  readTime?: number;
  viewCount?: number;
}

// Draft-specific extensions
interface ArticleDraft extends Article {
  originalArticleId?: string;
  lastSavedAt: string;
  isAutoSaved: boolean;
  publishScheduledFor?: string;
  isDraft: true;
}

// Article summary for lists
interface ArticleSummary {
  id: string;
  title: string;
  slug: string;
  excerpt: string;
  featuredImage?: {
    url: string;
    alt: string;
  };
  status: 'draft' | 'published' | 'archived';
  author: {
    id: string;
    name: string;
    avatar?: string;
  };
  categories: Array<{
    id: string;
    name: string;
  }>;
  createdAt: string;
  updatedAt: string;
  publishedAt?: string;
  readTime?: number;
  viewCount?: number;
}

// Image within article content
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

// Category for articles
interface ArticleCategory {
  id: string;
  name: string;
  slug: string;
  description?: string;
  parentCategoryId?: string;
}
```

### 4.2 API Request/Response Models

```typescript
// Create/Update Draft Input
interface ArticleDraftInput {
  title?: string;
  content?: string;
  excerpt?: string;
  featuredImage?: {
    url: string;
    alt: string;
  };
  categoryIds?: string[];
  tags?: string[];
  isAutoSaved?: boolean;
}

// Publish Article Input
interface PublishArticleInput {
  title: string;
  slug?: string; // Optional, will be generated from title if not provided
  content: string;
  excerpt: string;
  featuredImage?: {
    url: string;
    alt: string;
  };
  categoryIds: string[];
  tags: string[];
  publishScheduledFor?: string; // For scheduled publishing
}

// Image Upload Response
interface ImageUploadResponse {
  id: string;
  url: string;
  alt: string;
  caption?: string;
  width: number;
  height: number;
}

// Generic API Response
interface ApiResponse<T> {
  success: boolean;
  message?: string;
  data?: T;
  errors?: Array<{
    field: string;
    message: string;
  }>;
}
```

## 5. UI Layout Wireframes

### 5.1 Desktop Editor Layout

```
┌─────────────────────────────────────────────────────────────────────────┐
│ [Back] Article Title [Input]                       [Save] [Preview] [Publish] │
├─────────────────────────────────────────────────────────────────────────┤
│ ┌─────────────┐ ┌─────────────────────────────┐ ┌───────────────────┐   │
│ │             │ │                             │ │                   │   │
│ │ Metadata    │ │                             │ │                   │   │
│ │ - Categories│ │                             │ │                   │   │
│ │ - Tags      │ │                             │ │                   │   │
│ │             │ │                             │ │                   │   │
│ │ Featured    │ │        Markdown             │ │     Preview       │   │
│ │ Image       │ │        Editor               │ │                   │   │
│ │ [Upload]    │ │                             │ │                   │   │
│ │             │ │                             │ │                   │   │
│ │ Publish     │ │                             │ │                   │   │
│ │ - Status    │ │                             │ │                   │   │
│ │ - Visibility│ │                             │ │                   │   │
│ │             │ │                             │ │                   │   │
│ └─────────────┘ └─────────────────────────────┘ └───────────────────┘   │
│                                                                         │
│ Words: 1,234 | Read time: 5 min | Last saved: 2 minutes ago             │
└─────────────────────────────────────────────────────────────────────────┘
```

### 5.2 Mobile Editor Layout

```
┌─────────────────────────────────────┐
│ [Back]  Article Title [Input]  [⋮]  │
├─────────────────────────────────────┤
│ [Edit] [Preview] [Metadata]         │
├─────────────────────────────────────┤
│                                     │
│                                     │
│                                     │
│                                     │
│         Markdown Editor             │
│         or Preview                  │
│         or Metadata Form            │
│         (based on selected tab)     │
│                                     │
│                                     │
│                                     │
│                                     │
├─────────────────────────────────────┤
│ Words: 1,234 | Last saved: 2m ago   │
└─────────────────────────────────────┘
```

### 5.3 Article Management Layout

```
┌─────────────────────────────────────────────────────────────────────────┐
│ My Articles                                      [Search] [+ New Article]│
├─────────────────────────────────────────────────────────────────────────┤
│ [Drafts (5)] [Published (12)]                                           │
├─────────────────────────────────────────────────────────────────────────┤
│ Status: [All ▼]  Category: [All ▼]  Date: [Last 30 days ▼]              │
├─────────────────────────────────────────────────────────────────────────┤
│ ┌─────────────────────────────────────────────────────────────────────┐ │
│ │ Title: How to Build a React Component Library                        │ │
│ │ Last edited: June 5, 2025 | [Draft]                                  │ │
│ │ Categories: React, Frontend                                          │ │
│ │                                                [Edit] [Delete] [...]  │ │
│ └─────────────────────────────────────────────────────────────────────┘ │
│ ┌─────────────────────────────────────────────────────────────────────┐ │
│ │ Title: Introduction to GraphQL                                       │ │
│ │ Last edited: May 28, 2025 | [Draft]                                  │ │
│ │ Categories: GraphQL, API                                             │ │
│ │                                                [Edit] [Delete] [...]  │ │
│ └─────────────────────────────────────────────────────────────────────┘ │
│                                                                         │
│                              [Load More]                                │
└─────────────────────────────────────────────────────────────────────────┘
```

## 6. Technical Implementation Details

### 6.1 Markdown Editor Integration

We'll use **React-SimpleMDE** as our markdown editor, with the following customizations:

1. **Custom Toolbar**: Simplified toolbar with common markdown formatting options
2. **Autofocus**: Focus on editor when page loads
3. **Spellcheck**: Enable browser spellcheck
4. **Placeholder**: "Start writing your article..."
5. **Custom Styling**: Match TechNexus Blog design system

```typescript
// Example configuration
const editorOptions = {
  autofocus: true,
  spellChecker: true,
  placeholder: "Start writing your article...",
  toolbar: [
    "bold", "italic", "heading", "|", 
    "quote", "unordered-list", "ordered-list", "|",
    "link", "image", "code", "table", "|",
    "preview", "side-by-side", "fullscreen"
  ],
  status: ["lines", "words"],
  previewRender: (markdown) => customMarkdownRenderer(markdown),
};
```

### 6.2 Auto-Save Implementation

The auto-save functionality will use:

1. **Debouncing**: Save changes 30 seconds after typing stops
2. **Local Storage Backup**: Save to localStorage as backup
3. **Save Indicators**: Clear UI feedback on save status

```typescript
// Pseudo-code for auto-save logic
const useAutoSave = (article: ArticleDraft) => {
  const { mutate: saveDraft } = useSaveDraft();
  const [lastSaved, setLastSaved] = useState<Date | null>(null);
  const [saveStatus, setSaveStatus] = useState<'idle' | 'saving' | 'saved' | 'error'>('idle');
  
  // Create debounced save function
  const debouncedSave = useCallback(
    debounce((draft: ArticleDraft) => {
      // Save to localStorage as backup
      localStorage.setItem(`draft-${draft.id}`, JSON.stringify(draft));
      
      // Save to API
      setSaveStatus('saving');
      saveDraft(draft, {
        onSuccess: () => {
          setSaveStatus('saved');
          setLastSaved(new Date());
        },
        onError: () => {
          setSaveStatus('error');
        }
      });
    }, 30000),
    []
  );
  
  // Trigger save when article changes
  useEffect(() => {
    if (article.content || article.title) {
      setSaveStatus('idle');
      debouncedSave(article);
    }
  }, [article.content, article.title, article.excerpt, article.categoryIds, article.tags]);
  
  return { saveStatus, lastSaved };
};
```

### 6.3 Image Upload Flow

The image upload process will:

1. Accept file input or paste from clipboard
2. Validate file type and size
3. Show upload progress
4. Insert markdown code at cursor position when complete

```typescript
// Pseudo-code for image upload
const handleImageUpload = async (file: File, editorInstance: any) => {
  // Validate file
  if (!['image/jpeg', 'image/png', 'image/webp'].includes(file.type)) {
    return { error: 'Invalid file type. Please upload JPG, PNG, or WebP.' };
  }
  
  if (file.size > 2 * 1024 * 1024) { // 2MB
    return { error: 'File too large. Maximum size is 2MB.' };
  }
  
  // Create upload ID for tracking
  const uploadId = uuidv4();
  setUploadingImages(prev => ({
    ...prev,
    [uploadId]: { progress: 0, status: 'uploading' }
  }));
  
  try {
    // Upload image
    const formData = new FormData();
    formData.append('file', file);
    formData.append('articleId', article.id);
    formData.append('alt', 'Image description'); // Will prompt user for this
    
    const response = await uploadArticleImage(formData, {
      onUploadProgress: (progressEvent) => {
        const progress = Math.round((progressEvent.loaded * 100) / progressEvent.total);
        setUploadingImages(prev => ({
          ...prev,
          [uploadId]: { ...prev[uploadId], progress }
        }));
      }
    });
    
    // Update status
    setUploadingImages(prev => ({
      ...prev,
      [uploadId]: { progress: 100, status: 'success' }
    }));
    
    // Insert markdown at cursor position
    const imageMarkdown = `![${response.alt}](${response.url} "${response.caption || ''}")`;
    const cm = editorInstance.codemirror;
    const cursor = cm.getCursor();
    cm.replaceRange(imageMarkdown, cursor);
    
    return { success: true, image: response };
  } catch (error) {
    setUploadingImages(prev => ({
      ...prev,
      [uploadId]: { progress: 0, status: 'error', error: error.message }
    }));
    return { error: error.message };
  }
};
```

## 7. Next Steps

1. **Review Design**: Confirm component structure and data flow
2. **Create Base Components**: Implement core editor components
3. **Integrate Markdown Editor**: Set up React-SimpleMDE with customizations
4. **Implement Auto-Save**: Build draft saving functionality
5. **Add Metadata UI**: Create forms for article metadata
6. **Build Image Upload**: Implement image embedding workflow
7. **Create Article Management**: Build the article listing and management page
8. **Test End-to-End**: Validate the complete article creation workflow

This design document provides a comprehensive blueprint for implementing the Article Creation/Editor workflow in the TechNexus Blog platform. The component structure, state management approach, data models, and UI layouts are designed to create a seamless and intuitive experience for content creators while maintaining performance and reliability.
