# Development Environment Setup Guide

This guide provides a comprehensive overview of setting up your local development environment for the TechNexus Blog project. It covers Git branching strategy, repository setup, Docker Compose for local services, and Vitest for testing.

## 1. Git Branching Strategy: GitFlow

We will be adopting the GitFlow branching model for this project to ensure a structured and organized development workflow.

### Core Branches:
*   **`main`**: This branch holds the production-ready code. Only stable, fully tested releases are merged into `main`.
*   **`develop`**: This branch integrates all the completed features. It's the main integration branch for ongoing development.

### Supporting Branches:
*   **`feature/*`**: Created from `develop` for new features. Merged back into `develop` upon completion.
*   **`release/*`**: Created from `develop` when preparing a new release. Used for minor bug fixes and preparing for production. Merged into `main` and `develop`.
*   **`hotfix/*`**: Created from `main` to quickly address critical bugs in production. Merged back into `main` and `develop`.

### Workflow Summary:
1.  **Start a new feature**: `git checkout develop` then `git checkout -b feature/your-feature-name`
2.  **Work on your feature**: Commit regularly to your feature branch.
3.  **Finish a feature**: Merge your feature branch back into `develop`.
4.  **Prepare a release**: Create a `release/vX.Y.Z` branch from `develop`.
5.  **Hotfix**: Create a `hotfix/your-hotfix-name` branch from `main`.

## 2. Initial Repository Setup

The TechNexus Blog project will be structured into three main repositories:

*   **`technexus-blog-frontend`**: Contains the React-based frontend application.
*   **`technexus-blog-backend`**: Contains the Node.js backend API.
*   **`technexus-blog-shared`**: Contains shared utilities, types, and components used by both frontend and backend.

### Steps to Clone Repositories:
```bash
# Navigate to your desired development directory
cd ~/dev

# Clone the repositories (replace with actual repository URLs)
git clone [frontend-repo-url] technexus-blog-frontend
git clone [backend-repo-url] technexus-blog-backend
git clone [shared-repo-url] technexus-blog-shared
```

## 3. Local Backend and Database Services with Docker Compose

We use Docker Compose to set up our local backend and PostgreSQL database, ensuring a consistent development environment.

### Prerequisites:
*   Docker Desktop (or Docker Engine and Docker Compose) installed on your machine.

### `docker-compose.yml` Overview:
The `docker-compose.yml` file in the project root defines two services: `backend` and `db`.

```yaml
# docker-compose.yml
version: '3.8'

services:
  backend:
    build:
      context: ./technexus-blog-backend # Path to your backend Dockerfile
      dockerfile: Dockerfile
    ports:
      - "3000:3000" # Maps host port 3000 to container port 3000
    environment:
      DATABASE_URL: postgres://user:password@db:5432/technexus_blog
    depends_on:
      - db
    volumes:
      - ./technexus-blog-backend:/app # Mounts your local backend code into the container
      - /app/node_modules # Excludes node_modules from host mount to prevent issues
    networks:
      - technexus-network

  db:
    image: postgres:14-alpine # Using PostgreSQL 14 Alpine image
    restart: always
    environment:
      POSTGRES_DB: technexus_blog
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
    volumes:
      - db_data:/var/lib/postgresql/data # Persistent data volume for the database
      - ./db/init.sql:/docker-entrypoint-initdb.d/init.sql # Optional: for initial schema/seed data
    ports:
      - "5432:5432" # Maps host port 5432 to container port 5432
    networks:
      - technexus-network

volumes:
  db_data: # Defines the named volume for database persistence

networks:
  technexus-network: # Defines a custom network for services to communicate
    driver: bridge
```

### Initial Database Setup (`db/init.sql` - Optional):
If you need to run initial SQL scripts (e.g., for schema creation or seeding data), create a file named `init.sql` inside a `db` directory at the project root. This file will be automatically executed when the `db` container starts for the first time.

Example `db/init.sql`:
```sql
-- Create a simple 'articles' table
CREATE TABLE IF NOT EXISTS articles (
    id SERIAL PRIMARY KEY,
    title VARCHAR(255) NOT NULL,
    content TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Insert some sample data
INSERT INTO articles (title, content) VALUES
('First Blog Post', 'This is the content of the first blog post.'),
('Second Blog Post', 'This is the content of the second blog post.');
```

### Running Docker Services:
Navigate to the root of your `my-blog-technexus` project (where `docker-compose.yml` is located) and run:

```bash
docker-compose up -d
```
This command builds the backend image (if not already built), creates and starts the `backend` and `db` containers in detached mode.

To stop the services:
```bash
docker-compose down
```

## 4. Vitest for Testing

Vitest is configured to support both React component testing (frontend) and general Node.js unit/integration testing (backend).

### Installation:
Ensure you have Vitest and its necessary plugins installed in your project. If you are using Deno, ensure your `deno.jsonc` includes the following `imports`:

```json
// deno.jsonc
{
  "imports": {
    // ... other imports
    "vitest/config": "npm:vitest@^1.0.0/config",
    "@vitejs/plugin-react": "npm:@vitejs/plugin-react@^4.0.0",
    "@testing-library/jest-dom/vitest": "npm:@testing-library/jest-dom@^6.0.0/vitest"
  }
}
```

### `vitest.config.ts` Overview:
The `vitest.config.ts` file in the project root provides a unified configuration for testing.

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config';
import react from '@vitejs/plugin-react';

export default defineConfig({
  plugins: [react()], // Required for React component testing
  test: {
    globals: true, // Makes test utilities globally available (e.g., describe, it, expect)
    environment: 'jsdom', // Simulates a browser environment for DOM testing (essential for React)
    setupFiles: './vitest.setup.ts', // Path to a setup file for global test configurations
    include: ['**/*.{test,spec}.{js,mjs,cjs,ts,mts,cts,jsx,tsx}'], // Patterns for test files
    exclude: ['node_modules', 'dist', '.idea', '.git', '.cache'], // Directories/files to exclude from testing
    coverage: {
      provider: 'v8', // or 'istanbul' for coverage reporting
      reporter: ['text', 'json', 'html'], // Output formats for coverage reports
      exclude: ['node_modules/', 'dist/', '**/__tests__/', '**/*.d.ts'], // Exclude from coverage
    },
  },
});
```

### `vitest.setup.ts` Overview:
This file is used for global test setup, such as extending `expect` matchers from `@testing-library/jest-dom`.

```typescript
// vitest.setup.ts
import '@testing-library/jest-dom/vitest'; // Extends Vitest's expect with DOM matchers
```

### Running Tests:
To run all tests (both frontend and backend, based on `include` patterns):

```bash
vitest
```

To run tests in watch mode (re-runs tests on file changes):
```bash
vitest --watch
```

To generate a coverage report:
```bash
vitest run --coverage
```

### Example Test Files:

**Frontend Component Test (e.g., `technexus-blog-frontend/src/components/Button.test.tsx`):**
```tsx
// technexus-blog-frontend/src/components/Button.test.tsx
import { render, screen } from '@testing-library/react';
import { Button } from './Button'; // Assuming Button component exists
import { describe, it, expect } from 'vitest';

describe('Button', () => {
  it('renders with correct text', () => {
    render(<Button>Click Me</Button>);
    expect(screen.getByText('Click Me')).toBeInTheDocument();
  });

  it('calls onClick when clicked', () => {
    const handleClick = vi.fn(); // Vitest's mocking utility
    render(<Button onClick={handleClick}>Click Me</Button>);
    screen.getByText('Click Me').click();
    expect(handleClick).toHaveBeenCalledTimes(1);
  });
});
```

**Backend Unit Test (e.g., `technexus-blog-backend/src/utils/math.test.ts`):**
```typescript
// technexus-blog-backend/src/utils/math.test.ts
import { add } from './math'; // Assuming a simple add function
import { describe, it, expect } from 'vitest';

describe('math utilities', () => {
  it('add function correctly sums two numbers', () => {
    expect(add(2, 3)).toBe(5);
    expect(add(-1, 1)).toBe(0);
    expect(add(0, 0)).toBe(0);
  });
});
```

This guide should help new developers quickly set up their environment and start contributing to the TechNexus Blog project.
