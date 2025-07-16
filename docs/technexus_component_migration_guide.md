# TechNexus Component Migration Guide

**Design System References**:

- Colors: Primary Blue (#2563EB) for primary buttons, White (#FFFFFF) with blue border for secondary
- Typography: Inter Medium (500), 16px
- Components: Button variations in design system
- States: Hover, active, disabled states as specified

**Next.js Specific Changes**:

- Implement as a client component for interactive functionality

## 4.2. Card Component

**Current Implementation**: `src/components/common/Card.jsx`

- Basic card with title, content, and optional image
- Limited styling options

**Migration Path**: **Adapt with Significant Enhancements**

**TypeScript Interface**:

```typescript
interface CardProps {
  variant: 'standard' | 'article' | 'featured';
  title: string;
  content?: React.ReactNode;
  image?: string;
  href?: string;
  metadata?: {
    author?: string;
    date?: string;
    readingTime?: string;
  };
  category?: {
    name: string;
    slug: string;
  };
  tags?: {
    name: string;
    slug: string;
  }[];
  className?: string;
}
```

**Required Enhancements**:

1. Add article card and featured card variants
2. Implement hover states and animations
3. Add support for metadata (author, date, reading time)
4. Add support for categories and tags
5. Ensure proper image handling and aspect ratios

**Design System References**:

- Colors: White (#FFFFFF) background, Light (#E2E8F0) border
- Typography: H4 for card titles, Body Small for excerpts, Caption for metadata
- Components: Standard Card, Article Card, Featured Card in design system
- States: Hover state with increased shadow and slight scale

**Next.js Specific Changes**:

- Use Next.js Image component for optimized images
- Use Next.js Link for card navigation
- Consider server component for initial render, client component for interactive elements

## 5. Home Page Components

### 5.1. FeaturedArticle Component

**Current Implementation**: `src/components/home/FeaturedArticle.jsx`

- Basic featured article display with image, title, excerpt
- Limited styling options

**Migration Path**: **Adapt with Enhancements**

**TypeScript Interface**:

```typescript
interface FeaturedArticleProps {
  article: {
    id: string;
    title: string;
    slug: string;
    excerpt: string;
    coverImage: string;
    category: {
      name: string;
      slug: string;
    };
    author: {
      name: string;
      avatar?: string;
    };
    publishedAt: string;
    readingTime: string;
  };
}
```

**Required Enhancements**:

1. Improve visual design to match mockups
2. Add category badge and metadata display
3. Implement responsive behavior for different screen sizes
4. Add hover effects and animations
