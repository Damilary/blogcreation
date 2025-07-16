# TechNexus Blog: Dynamic Top Picks Implementation

## Overview

This document provides a comprehensive overview of the dynamic Top Picks section implementation for the TechNexus Blog. The Top Picks section now fetches data from a GraphQL API (with mock support for development) and includes proper loading, error, and success states, following the same robust pattern established with the HeroSection and FeaturedArticlesSection.

## Implementation Details

### 1. GraphQL Query Definition

- Utilized the existing `ARTICLE_FRAGMENT` for consistent data structure
- Used the defined query for top picks articles in `/src/lib/graphql/queries.ts`:

  ```graphql
  export const GET_TOP_PICKS = gql`
    query GetTopPicks($limit: Int = 5) {
      topPicks(limit: $limit) {
        ...ArticleFields
      }
    }
    ${ARTICLE_FRAGMENT}
  `;
  ```

- The query supports a `limit` parameter to control the number of articles returned

### 2. React Query Hook Implementation

- Created a custom hook in `/src/hooks/useTopPicks.ts`
- The hook leverages React Query for data fetching, caching, and state management
- Configurable options include:
  - `limit`: Number of articles to fetch (default: 5)
  - `enabled`: Whether the query should auto-execute
  - `initialData`: Optional initial data for SSR or static content

### 3. TopPicksSection Component Refactoring

The TopPicksSection component now includes:

- TypeScript interfaces for type safety
- Data fetching with the useTopPicks hook
- Three distinct UI states:
  1. **Loading State**: Skeleton UI with animation for each article card in the carousel
  2. **Error State**: User-friendly error message with refresh option
  3. **Success State**: Horizontally scrollable carousel of article cards with navigation controls
- Scroll navigation buttons that dynamically enable/disable based on scroll position
- Maintained the existing carousel functionality with smooth scrolling

### 4. Mock Data System Integration

- Added mock top picks articles data in `/src/lib/api/mockData.ts`
- Updated the mock GraphQL handler to support the topPicks query
- Implemented limit parameter support to match real API behavior

### 5. Homepage Integration

- Updated the HomePage component to use the dynamic TopPicksSection
- Removed static article data passing, allowing the component to fetch its own data
- Ensured seamless integration with other homepage sections

## Technical Implementation

### Loading State

The loading state displays skeleton UI elements that match the structure of the carousel:

- Animated pulse effect for visual feedback
- Placeholder elements for navigation controls, images, category tags, titles, excerpts, and author info
- Maintains the same horizontal layout as the success state for a smooth transition

### Error State

The error state provides a user-friendly experience when data fetching fails:

- Clear error message explaining the issue
- Refresh button to retry the data fetch
- Contained within the section's layout to maintain page structure

### Success State

The success state renders the fetched top picks articles in a carousel:

- Horizontally scrollable layout with navigation controls
- Each article displayed in an ArticleCard component
- Navigation buttons that dynamically enable/disable based on scroll position
- Smooth scrolling behavior for better user experience

## Development Workflow

### Using Mock Data

The implementation uses the same mock data system as the HeroSection and FeaturedArticlesSection:

- Toggle between mock and real API in `/src/lib/api/fetchGraphQL.ts`
- Mock data closely resembles what would be returned by a real API
- Simulated network delay for realistic testing

### Testing Different States

To test different states of the TopPicksSection:

1. **Loading State**: Increase the delay in the mock handler
2. **Error State**: Modify the mock handler to throw an error for the topPicks query
3. **Empty State**: Return an empty array from the mock handler

## Next Steps

1. Apply the same pattern to the remaining homepage sections (CategoryShowcase, LatestArticles)
2. Implement server-side rendering (SSR) for initial data to improve performance and SEO
3. Add more interactive features to the carousel (e.g., touch support, keyboard navigation)
4. Implement personalized recommendations for the Top Picks section

## Conclusion

The Top Picks section now follows the same robust pattern as the HeroSection and FeaturedArticlesSection, with dynamic data fetching, proper state handling, and seamless integration with the mock data system. This implementation provides a solid foundation for making the remaining homepage sections dynamic as well.
