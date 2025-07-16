# TechNexus Blog: Setup and Deployment Guide

This guide will walk you through setting up the TechNexus Blog project in VSCode, running it locally, and deploying it to a hosting platform.

## Table of Contents

1. [Setting Up in VSCode](#setting-up-in-vscode)
2. [Running the Project Locally](#running-the-project-locally)
3. [Previewing the Site](#previewing-the-site)
4. [Deployment Options](#deployment-options)
5. [Troubleshooting](#troubleshooting)

## Setting Up in VSCode

### Prerequisites

- [Node.js](https://nodejs.org/) (v18.17.0 or higher)
- [VSCode](https://visualstudio.com/)
- [Git](https://git-scm.com/downloads) (optional, for version control)

### Step 1: Extract the Project Files

1. Locate the `technexus-blog.zip` file you downloaded
2. Extract it to a location of your choice (e.g., your Documents folder)

### Step 2: Open the Project in VSCode

1. Open VSCode
2. Go to File > Open Folder
3. Navigate to the extracted `technexus-blog` folder and select it
4. VSCode will load the project

### Step 3: Install Recommended Extensions

For the best development experience, install these VSCode extensions:

- ESLint
- Prettier
- Tailwind CSS IntelliSense
- TypeScript Vue Plugin (Volar)

You can install them by:

1. Going to the Extensions view (Ctrl+Shift+X or Cmd+Shift+X)
2. Searching for each extension
3. Clicking "Install"

### Step 4: Install Dependencies

Open a terminal in VSCode (Terminal > New Terminal) and run:

```bash
# Using npm
npm install

# OR using yarn
yarn install

# OR using pnpm
pnpm install
```

This will install all the necessary dependencies for the project.

## Running the Project Locally

### Starting the Development Server

In the VSCode terminal, run:

```bash
# Using npm
npm run dev

# OR using yarn
yarn dev

# OR using pnpm
pnpm dev
```

This will start the Next.js development server, typically on port 3000.

### Accessing the Local Site

Open your browser and navigate to:

```bash
http://localhost:3000
```

You should now see the TechNexus Blog homepage with the dynamic HeroSection we implemented.

### Working with Mock Data

The project is configured to use mock data by default. To toggle between mock and real API:

1. Open `/src/lib/api/fetchGraphQL.ts`
2. Change `const USE_MOCK_DATA = true;` to `false` to use a real API
3. Update the API URL in `/src/lib/api/graphqlClient.ts` to point to your actual GraphQL endpoint

## Previewing the Site

### Development Mode Features

While running in development mode, you can:

- See real-time updates as you edit code
- View detailed error messages
- Use React Developer Tools for debugging

### Testing Different States

To test different states of the HeroSection:

1. **Loading State**: Modify the mock data handler to add a longer delay
2. **Error State**: Change the mock handler to throw an error
3. **Empty State**: Return null from the mock handler

Example modification in `/src/lib/api/mockData.ts`:

```typescript
// For testing loading state (longer delay)
await new Promise(resolve => setTimeout(resolve, 3000));

// For testing error state
throw new Error('Mock API error');

// For testing empty state
return { heroArticle: null };
```

## Deployment Options

### Option 1: Vercel (Recommended for Next.js)

Vercel is the platform built by the creators of Next.js and offers the simplest deployment experience.

1. Create an account at [vercel.com](https://vercel.com)
2. Install the Vercel CLI:

   ```bash
   npm install -g vercel
   ```

3. In your project directory, run:

   ```bash
   vercel
   ```

4. Follow the prompts to deploy your project
5. Once deployed, Vercel will provide you with a URL to access your site

### Option 2: Netlify

Netlify is another excellent option for hosting Next.js applications.

1. Create an account at [netlify.com](https://netlify.com)
2. Install the Netlify CLI:

   ```bash
   npm install -g netlify-cli
   ```

3. Build your project:

   ```bash
   npm run build
   ```

4. Deploy using the Netlify CLI:

   ```bash
   netlify deploy
   ```

5. Follow the prompts to complete the deployment

### Option 3: GitHub Pages

For a static export of your Next.js site:

1. Modify your `next.config.ts` to enable static exports:

   ```typescript
   const nextConfig = {
     output: 'export',
     // other config options...
   };
   ```

2. Build your project:

   ```bash
   npm run build
   ```

3. The static files will be in the `out` directory
4. You can then deploy these files to GitHub Pages or any static hosting service

## Troubleshooting

### Common Issues and Solutions

#### "Module not found" errors

If you see errors about missing modules:

```bash
npm install
```

#### Port 3000 already in use

If port 3000 is already in use, you can specify a different port:

```bash
npm run dev -- -p 3001
```

#### TypeScript errors

If you encounter TypeScript errors:

1. Make sure you have the correct TypeScript version:

   ```bash
   npm install typescript@latest
   ```

2. Run the TypeScript checker:

   ```bash
   npx tsc --noEmit
   ```

#### Image optimization issues

If you encounter issues with image optimization:

1. Make sure you have the required packages:

   ```bash
   npm install sharp
   ```

2. Check that your images are in the correct format and location

### Getting Help

If you encounter issues not covered here:

1. Check the [Next.js documentation](https://nextjs.org/docs)
2. Search for solutions on [Stack Overflow](https://stackoverflow.com/)
3. Join the [Next.js Discord community](https://discord.gg/nextjs)

## Next Steps

Now that you have the TechNexus Blog running locally, you can:

1. Continue implementing dynamic data fetching for other homepage sections
2. Create additional pages like Article detail, Category, and User Profile
3. Connect to a real backend API
4. Customize the design to match your brand
5. Add analytics and SEO optimization

Happy coding!
