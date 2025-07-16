# Project Code Structure Documentation

This document outlines the structure and key components of the React.js blog template project.

## Project Overview

The project is a modern blog frontend built with React.js, Vite as the build tool, and Tailwind CSS for styling. It features a clean, responsive design with functionalities for displaying articles, categories, and author information.

### Directory Structure

The main project files are organized as follows:

```plaintext
/home/ubuntu/project_workspace/react_template/
├── public/
│   └── vite.svg  // Example static asset
├── src/
│   ├── assets/
│   │   └── react.svg // Example static asset
│   ├── components/
│   │   ├── common/     // Reusable UI components (e.g., Button.jsx)
│   │   ├── home/       // Components specific to the homepage (e.g., ArticleGrid.jsx, HeroSection.jsx)
│   │   └── layout/     // Layout components (e.g., Footer.jsx, Navbar.jsx, PageLayout.jsx)
│   ├── data/
│   │   └── articles.js // Sample article data for development
│   ├── pages/
│   │   ├── ArticlePage.jsx   // Page for displaying a single article
│   │   ├── CategoryPage.jsx  // Page for displaying articles by category
│   │   ├── HomePage.jsx      // The main landing page of the blog
│   │   └── NotFoundPage.jsx  // Page displayed for 404 errors
│   ├── styles/
│   │   └── global.css  // Global CSS styles (though most styling is via Tailwind)
│   ├── App.jsx         // Main application component, sets up routing
│   ├── index.css       // Main CSS entry point, imports Tailwind CSS
│   └── main.jsx        // Entry point of the React application, renders App
├── .eslintrc.cjs       // ESLint configuration
├── .gitignore          // Specifies intentionally untracked files that Git should ignore
├── index.html          // Main HTML file, entry point for Vite
├── package.json        // Project metadata, dependencies, and scripts
├── pnpm-lock.yaml      // PNPM lock file for dependency management
├── postcss.config.js   // PostCSS configuration (used by Tailwind CSS)
├── README.md           // Project README file
└── tailwind.config.js  // Tailwind CSS configuration
└── vite.config.js      // Vite build tool configuration
```

### Key Components and Functionality

1. **`main.jsx`**: This is the JavaScript entry point for the application. It imports React, ReactDOM, and the main `App` component. It then uses `ReactDOM.createRoot()` to render the `App` component into the `div#root` element in `index.html`.

2. **`App.jsx`**: This component sets up the application-level routing using `react-router-dom`. It defines routes for the `HomePage`, `ArticlePage`, `CategoryPage`, and a `NotFoundPage` for handling invalid URLs. It typically wraps these routes within a `PageLayout` component that includes the `Navbar` and `Footer`.

3. **`PageLayout.jsx` (`src/components/layout/`)**: This component provides the overall structure for most pages, including the navigation bar (`Navbar.jsx`) at the top and the footer (`Footer.jsx`) at the bottom. The actual page content is rendered as children within this layout.

4. **`Navbar.jsx` (`src/components/layout/`)**: Displays the site logo, navigation links (e.g., Home, Categories), and potentially a search bar or subscription button. It handles responsive behavior for different screen sizes.

5. **`Footer.jsx` (`src/components/layout/`)**: Contains copyright information, social media links, or other relevant links typically found at the bottom of a webpage.

6. **`HomePage.jsx` (`src/pages/`)**: This is the main landing page. It usually features a hero section (`HeroSection.jsx`), a grid of featured or recent articles (`ArticleGrid.jsx`), and potentially other sections like a newsletter signup.

7. **`ArticlePage.jsx` (`src/pages/`)**: Displays the full content of a single blog post. It fetches article data based on a slug from the URL parameters. Key elements include:

   - Article title, category, author information, publication date, and reading time.
   - Cover image (hero image).
   - Main article content (rendered from the `content` array in `articles.js`).
   - Tags associated with the article.
   - Author biography section.
   - A section for related articles, typically filtered by category.

8. **`CategoryPage.jsx` (`src/pages/`)**: Displays a list or grid of articles belonging to a specific category. The category is usually determined from the URL parameter.

9. **`ArticleGrid.jsx` (`src/components/home/`)**: A reusable component that displays a collection of articles in a grid layout. It takes an array of article objects as a prop and renders individual article cards.

10. **`Button.jsx` (`src/components/common/`)**: A generic, reusable button component that can be styled with different variants (e.g., primary, secondary) and sizes. It often uses `Link` from `react-router-dom` for internal navigation.

11. **`articles.js` (`src/data/`)**: This file contains an array of sample article objects. In a real-world application, this data would typically be fetched from a backend API or a Content Management System (CMS). Each article object includes properties like `id`, `title`, `slug`, `excerpt`, `content` (an array of paragraphs), `category`, `coverImage` URL, `publishedAt` date, `readingTime`, `author` details, and `tags`.

### Data Flow

- **Static Data**: For this template, article data is sourced statically from `src/data/articles.js`.
- **Routing**: `react-router-dom` handles navigation. When a user navigates to a URL like `/article/:slug`, the `ArticlePage` component is rendered.
- **Props Drilling**: Components like `HomePage` and `CategoryPage` might fetch or filter the list of articles from `articles.js` and then pass individual article data down to child components (e.g., `ArticleGrid`, article cards) via props.
- **State Management**: Component-level state is managed using React hooks like `useState` and `useEffect` (e.g., in `ArticlePage` to store the currently displayed article and related articles).

### Styling

- **Tailwind CSS**: The primary styling mechanism. Utility classes are used directly in the JSX of components. The configuration for Tailwind is in `tailwind.config.js`.
- **PostCSS**: Used by Tailwind CSS for processing CSS. Configuration is in `postcss.config.js`.
- **`index.css`**: Imports Tailwind's base styles, components, and utilities.
- **`global.css` (`src/styles/`)**: Can be used for any global styles or custom CSS that doesn't fit well with Tailwind's utility-first approach, though it's minimally used in this template.

### Build Process

- **Vite**: Used as the development server and build tool. Vite provides fast Hot Module Replacement (HMR) for a smooth development experience and optimizes the build for production.
- **`vite.config.js`**: Configures Vite, including plugins (e.g., `@vitejs/plugin-react`).
- **Scripts in `package.json`**:
  - `dev`: Starts the Vite development server.
    - `build`: Creates an optimized production build in the `dist/` directory.
    - `lint`: Runs ESLint to check for code quality and style issues.
    - `preview`: Serves the production build locally for previewing.

This documentation provides a high-level understanding of the project's architecture. For more detailed information, refer to the specific component files and their inline comments.
