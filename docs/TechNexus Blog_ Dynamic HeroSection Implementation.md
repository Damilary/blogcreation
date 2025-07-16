# TechNexus Blog: Dynamic HeroSection Implementation

## Overview

This document provides a comprehensive overview of the dynamic HeroSection implementation for the TechNexus Blog. The HeroSection now fetches data from a GraphQL API (with mock support for development) and includes proper loading, error, and success states.

## Implementation Details

### 1. Placeholder Images

- Created `/public/images` directory
- Added placeholder images for the hero and article cards:
  - `/public/images/placeholder-hero.jpg`: AI-themed hero image
  - `/public/images/placeholder-1.jpg`: Web development themed image
  - `/public/images/placeholder-2.jpg`: Quantum computing themed image
  - `/public/images/placeholder-3.jpg`: Cybersecurity themed image
  - `/public/images/placeholder-4.jpg`: Next.js development themed image

### 2. GraphQL API Integration

- Defined GraphQL queries in `/src/lib/graphql/queries.ts`
- Created a reusable `ARTICLE_FRAGMENT` for consistent data structure
- Implemented specific queries for different homepage sections:
  - `GET_HERO_ARTICLE`
  - `GET_FEATURED_ARTICLES`
  - `GET_ARTICLES_BY_CATEGORY`
  - `GET_TOP_PICKS`
  - `GET_LATEST_ARTICLES`

### 3. React Query Integration

- Set up React Query with `QueryClientProvider` in `/src/app/providers.tsx`
- Integrated with the app layout in `/src/app/layout.tsx`
- Configured with sensible defaults (5-minute stale time, disabled window focus refetching)

### 4. Mock Data Support

- Created mock data in `/src/lib/api/mockData.ts`
- Implemented a mock GraphQL handler that simulates API responses
- Added a toggle mechanism in `/src/lib/api/fetchGraphQL.ts` to switch between mock and real API

### 5. HeroSection Component

The HeroSection component now includes:

- TypeScript interfaces for type safety
- Data fetching with React Query
- Three distinct UI states:
  1. **Loading State**: Skeleton UI with animation
  2. **Error State**: User-friendly error message with refresh option
  3. **Success State**: Properly styled article display

## Development Workflow

### Using Mock Data

The current implementation uses mock data by default. This is controlled by the `USE_MOCK_DATA` flag in `/src/lib/api/fetchGraphQL.ts`.

To toggle between mock and real API:

1. Open `/src/lib/api/fetchGraphQL.ts`
2. Change `const USE_MOCK_DATA = true;` to `false` to use the real API

### Adding More Mock Data

To add more mock data for other sections:

1. Extend the mock data objects in `/src/lib/api/mockData.ts`
2. Update the `mockGraphQLHandler` function to handle additional queries

## Next Steps

1. Apply the same data fetching pattern to other homepage sections
2. Create a more robust error handling strategy
3. Implement server-side rendering (SSR) for initial data
4. Add unit and integration tests for the components

## Technical Considerations

- The current implementation uses client-side rendering. For production, consider implementing SSR for better SEO and performance.
- Image paths are currently hardcoded. In a production environment, these would come from a CMS or API.
- Error handling is basic. Consider implementing more sophisticated error tracking and recovery mechanisms.
