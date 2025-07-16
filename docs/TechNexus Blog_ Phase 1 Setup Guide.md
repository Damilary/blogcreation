# TechNexus Blog: Phase 1 Setup Guide

This document provides a comprehensive guide to the Phase 1 setup of the TechNexus Blog project, including the Next.js frontend and Express/GraphQL backend.

## Project Overview

The TechNexus Blog has been initialized as a modern web application with:

- **Frontend**: Next.js 14+ with TypeScript, App Router, and Tailwind CSS
- **Backend**: Node.js/Express with TypeScript, Apollo GraphQL, and MongoDB

## Directory Structure

```bash
technexus-blog/
├── src/                      # Frontend source code
│   ├── app/                  # Next.js App Router pages
│   ├── components/           # React components
│   │   ├── ui/               # Reusable UI components
│   │   ├── layout/           # Layout components
│   │   └── features/         # Feature-specific components
│   ├── lib/                  # Utility libraries
│   ├── store/                # Zustand state management
│   ├── styles/               # Global styles
│   ├── hooks/                # Custom React hooks
│   └── utils/                # Utility functions
├── backend/                  # Backend source code
│   ├── src/
│   │   ├── controllers/      # Express route controllers
│   │   ├── models/           # Mongoose data models
│   │   ├── routes/           # Express routes
│   │   ├── services/         # Business logic services
│   │   ├── middleware/       # Express middleware
│   │   ├── utils/            # Utility functions
│   │   ├── config/           # Configuration files
│   │   ├── graphql/          # GraphQL schema and resolvers
│   │   └── index.ts          # Entry point
│   └── package.json          # Backend dependencies
├── public/                   # Static assets
├── tailwind.config.js        # Tailwind CSS configuration
├── package.json              # Frontend dependencies
└── .gitignore                # Git ignore rules
```

## Frontend Setup

### 1. Next.js with TypeScript

The project uses Next.js 14+ with TypeScript and the App Router architecture for optimal performance and developer experience.

### 2. Tailwind CSS Configuration

Tailwind CSS has been configured according to the TechNexus Blog design system, including:

- Custom color palette with primary, secondary, accent, and semantic colors
- Typography scale and font families (Inter and JetBrains Mono)
- Spacing, border radius, and other design tokens
- Dark mode support

### 3. Core Frontend Libraries

The following libraries have been installed:

- **State Management**: Zustand for global state
- **Data Fetching**: React Query and SWR
- **Form Handling**: React Hook Form with Zod validation
- **UI Enhancement**: Framer Motion for animations
- **Content Rendering**: React Markdown with Prism.js for syntax highlighting

### 4. Utility Functions

Basic utility functions have been created in `src/lib/utils.ts`:

```typescript
// Combines multiple class names and merges Tailwind classes
export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs));
}

// Formats a date string to a readable format
export function formatDate(dateString: string): string {
  const date = new Date(dateString);
  return new Intl.DateTimeFormat('en-US', {
    year: 'numeric',
    month: 'long',
    day: 'numeric',
  }).format(date);
}

// Truncates text to a specified length and adds ellipsis
export function truncateText(text: string, maxLength: number): string {
  if (text.length <= maxLength) return text;
  return text.slice(0, maxLength) + '...';
}
```

### 5. Authentication Store

A Zustand store for authentication has been created in `src/store/useAuthStore.ts`:

```typescript
export const useAuthStore = create<AuthState>()(
  persist(
    (set) => ({
      user: null,
      isLoading: false,
      error: null,
      login: async (email, password) => {
        // Implementation details...
      },
      logout: async () => {
        // Implementation details...
      },
      signup: async (email, password, name) => {
        // Implementation details...
      },
    }),
    {
      name: 'auth-storage',
      partialize: (state) => ({ user: state.user }),
    }
  )
);
```

## Backend Setup

### 1. Express with TypeScript

The backend is set up with Express.js and TypeScript, providing a solid foundation for the API.

### 2. Apollo GraphQL

Apollo Server is configured for GraphQL API endpoints, with initial type definitions and resolvers.

### 3. MongoDB Integration

The backend is prepared for MongoDB integration using Mongoose ODM.

### 4. GraphQL Schema

Initial GraphQL schema has been defined with types for:

- Users
- Articles
- Categories
- Tags
- Authentication

## Getting Started

### Frontend Development

1. Navigate to the project directory:

   ```bash
   cd technexus-blog
   ```

2. Install dependencies:

   ```bash
   pnpm install
   ```

3. Start the development server:

   ```bash
   pnpm dev
   ```

4. Open your browser to

<!-- <http://localhost:3000> -->

### Backend Development

1. Navigate to the backend directory:

   ```bash
   cd technexus-blog/backend
   ```

2. Install dependencies:

   ```bash
   pnpm install
   ```

3. Create a `.env` file with the following variables:

   ```bash
   PORT=4000
   MONGODB_URI=mongodb://localhost:27017/technexus-blog
   JWT_SECRET=your_jwt_secret_here
   ```

4. Start the development server:

   ```bash
   pnpm dev
   ```

5. Access the GraphQL playground at

<!-- <http://localhost:4000/graphql> -->

## Next Steps

With Phase 1 complete, you can now proceed to Phase 2: Core UI & Navigation Implementation, which will involve:

1. Implementing the UI components from the Component Migration Guide
2. Building the header and footer components
3. Setting up the navigation system
4. Creating the homepage layout

## Additional Resources

- [Next.js Documentation](https://nextjs.org/docs)
- [Tailwind CSS Documentation](https://tailwindcss.com/docs)
- [React Query Documentation](https://tanstack.com/query/latest)
- [Zustand Documentation](https://github.com/pmndrs/zustand)
- [Apollo Server Documentation](https://www.apollographql.com/docs/apollo-server/)
