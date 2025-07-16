# Frontend Component Library Documentation

This document outlines the usage and props for essential React components used in the TechNexus Blog frontend. These components are designed to be reusable and integrate seamlessly with the Supabase CMS data.

## 1. `ArticleBody` Component

Renders the main content of an article, supporting markdown and interactive code playgrounds.

- **File Path:** `src/components/articles/ArticleBody.tsx`

### Props

| Prop Name       | Type                                  | Description                                                              | Required |
| :-------------- | :------------------------------------ | :----------------------------------------------------------------------- | :------- |
| `content`       | `ArticleContentBlock[]`               | An array of content blocks, each containing `type` (e.g., 'markdown', 'codePlayground') and `content` or `playgroundId`. | Yes      |
| `codePlaygrounds` | `CodePlaygroundType[]`                | An array of code playground objects, used to render interactive code blocks. | Yes      |
| `user`          | `User \| null`                        | The currently authenticated user object, used for interactive features like code playgrounds. | Yes      |

### Usage Example

```jsx
import { ArticleBody } from '@/components/articles/ArticleBody';

// Assuming 'article' and 'user' objects are fetched from your data source
const articleContent = [
  { id: '1', type: 'markdown', content: '## Introduction\nThis is some **markdown** content.' },
  { id: '2', type: 'codePlayground', playgroundId: 'playground-123' },
];

const codePlaygroundsData = [
  { id: 'playground-123', initialCode: 'console.log("Hello, World!");', language: 'javascript', expectedOutput: 'Hello, World!\n' },
];

const currentUser = { id: 'user-abc', name: 'John Doe' }; // Example user object

<ArticleBody 
  content={articleContent} 
  codePlaygrounds={codePlaygroundsData} 
  user={currentUser} 
/>
```

## 2. `ArticleCard` Component

Displays a summary of an article, typically used in lists or grids on the homepage or category pages.

- **File Path:** `src/components/articles/ArticleCard.tsx`

### Props

| Prop Name | Type      | Description                                                              | Required |
| :-------- | :-------- | :----------------------------------------------------------------------- | :------- |
| `article` | `Article` | The article object containing `id`, `title`, `slug`, `excerpt`, `coverImage`, `publishedAt`, `readingTime`, `author`, and `tags`. | Yes      |

### Usage Example

```jsx
import { ArticleCard } from '@/components/articles/ArticleCard';

const sampleArticle = {
  id: '1',
  title: 'Understanding React Hooks',
  slug: 'understanding-react-hooks',
  excerpt: 'A comprehensive guide to React Hooks, including useState, useEffect, and custom hooks.',
  coverImage: '/images/placeholder-1.jpg',
  publishedAt: '2023-04-15T10:00:00Z',
  readingTime: 10,
  author: { id: 'author-1', name: 'Jane Doe', avatar: '/images/avatar.jpg', bio: 'Frontend Developer' },
  category: { id: 'cat-1', name: 'Web Development', slug: 'web-development' },
  tags: ['React', 'Hooks', 'Frontend'],
  content: [], // Not used in ArticleCard
  codePlaygrounds: [], // Not used in ArticleCard
};

<ArticleCard article={sampleArticle} />
```

## 3. `ArticlePageLayout` Component

Provides the overall layout for a single article page, including header, breadcrumbs, author info, social sharing, and related sections. The actual article content is passed as children.

- **File Path:** `src/components/layout/ArticlePage.tsx`

### Props

| Prop Name | Type      | Description                                                              | Required |
| :-------- | :-------- | :----------------------------------------------------------------------- | :------- |
| `article` | `Article` | The article object containing all necessary metadata for the page header and sidebars. | Yes      |
| `children` | `React.ReactNode` | The main content of the article (e.g., rendered by `ArticleBody`). | Yes      |

### Usage Example

```jsx
import ArticlePageLayout from '@/components/layout/ArticlePage';
import { ArticleBody } from '@/components/articles/ArticleBody';

// Assuming 'article' and 'user' objects are fetched from your data source
const articleData = {
  id: '1',
  title: 'My Awesome Article',
  slug: 'my-awesome-article',
  excerpt: 'This is an excerpt of my awesome article.',
  coverImage: '/images/article-cover.jpg',
  publishedAt: '2023-06-29T12:00:00Z',
  readingTime: 15,
  author: { id: 'author-1', name: 'Alice Smith', avatar: '/images/alice.jpg', bio: 'Tech Writer' },
  category: { id: 'cat-2', name: 'AI/ML', slug: 'ai-ml' },
  tags: ['AI', 'Machine Learning'],
  content: [
    { id: 'block-1', type: 'markdown', content: 'Content of the article goes here...' },
  ],
  codePlaygrounds: [],
};

const currentUser = { id: 'user-abc', name: 'John Doe' }; // Example user object

<ArticlePageLayout article={articleData}>
  <ArticleBody 
    content={articleData.content} 
    codePlaygrounds={articleData.codePlaygrounds} 
    user={currentUser} 
  />
</ArticlePageLayout>
