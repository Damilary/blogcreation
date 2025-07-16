# TechNexus Blog: Deployment Guide

This guide provides detailed instructions for deploying your TechNexus Blog to various hosting platforms.

## Table of Contents

1. [Deployment Preparation](#deployment-preparation)
2. [Vercel Deployment (Recommended)](#vercel-deployment-recommended)
3. [Netlify Deployment](#netlify-deployment)
4. [GitHub Pages Deployment](#github-pages-deployment)
5. [Custom Server Deployment](#custom-server-deployment)

## Deployment Preparation

Before deploying, make sure your project is ready:

1. **Test thoroughly locally**
   - Ensure all features work as expected
   - Check for any console errors
   - Test on different screen sizes

2. **Update environment variables**
   - Create a `.env.local` file for local development
   - Prepare production environment variables for your hosting platform

3. **Build your project locally to check for issues**

   ```bash
   npm run build
   ```

## Vercel Deployment (Recommended)

Vercel is the platform created by the team behind Next.js and offers the most seamless deployment experience.

### Option 1: Deploy via Vercel Dashboard

1. Create an account at [vercel.com](https://vercel.com)
2. Click "New Project"
3. Import your Git repository or upload your project files
4. Configure your project settings:
   - Framework Preset: Next.js
   - Build Command: `npm run build` (default)
   - Output Directory: `.next` (default)
   - Install Command: `npm install` (default)
5. Add any environment variables needed
6. Click "Deploy"

### Option 2: Deploy via Vercel CLI

1. Install the Vercel CLI:

   ```bash
   npm install -g vercel
   ```

2. Log in to your Vercel account:

   ```bash
   vercel login
   ```

3. Navigate to your project directory and run:

   ```bash
   vercel
   ```

4. Follow the prompts to configure your deployment
5. For subsequent deployments, use:

   ```bash
   vercel --prod
   ```

### Vercel Deployment Benefits

- Automatic HTTPS
- Global CDN
- Serverless Functions support
- Preview deployments for pull requests
- Easy rollbacks
- Built-in analytics

## Netlify Deployment

Netlify is another excellent platform for hosting Next.js applications.

### Option 1: Deploy via Netlify Dashboard

1. Create an account at [netlify.com](https://netlify.com)
2. Click "New site from Git"
3. Connect to your Git provider and select your repository
4. Configure build settings:
   - Build Command: `npm run build`
   - Publish Directory: `.next`
5. Add environment variables if needed
6. Click "Deploy site"

### Option 2: Deploy via Netlify CLI

1. Install the Netlify CLI:

   ```bash
   npm install -g netlify-cli
   ```

2. Log in to your Netlify account:

   ```bash
   netlify login
   ```

3. Initialize your site:

   ```bash
   netlify init
   ```

4. Follow the prompts to configure your site
5. Deploy your site:

   ```bash
   netlify deploy --prod
   ```

### Netlify Deployment Benefits

- Automatic HTTPS
- Global CDN
- Form handling
- Serverless Functions
- Deploy previews
- Easy rollbacks

## GitHub Pages Deployment

For static exports of your Next.js site:

1. Modify your `next.config.ts` to enable static exports:

   ```typescript
   const nextConfig = {
     output: 'export',
     // If your site is not at the root of the domain:
     basePath: '/your-repo-name',
     // Disable image optimization for static export:
     images: {
       unoptimized: true,
     },
   };
   
   export default nextConfig;
   ```

2. Add a `.github/workflows/deploy.yml` file for GitHub Actions:

   ```yaml
   name: Deploy to GitHub Pages

   on:
     push:
       branches: [main]

   jobs:
     build-and-deploy:
       runs-on: ubuntu-latest
       steps:
         - name: Checkout
           uses: actions/checkout@v3
           
         - name: Setup Node.js
           uses: actions/setup-node@v3
           with:
             node-version: 18
             
         - name: Install dependencies
           run: npm ci
           
         - name: Build
           run: npm run build
           
         - name: Deploy
           uses: JamesIves/github-pages-deploy-action@v4
           with:
             folder: out
   ```

3. In your repository settings, enable GitHub Pages and set the source to the GitHub Actions workflow

4. Push your changes to the main branch to trigger the deployment

### GitHub Pages Considerations

- Only suitable for static sites without server-side rendering
- Limited to static generation (no API routes or server components)
- Free hosting for public repositories
- Custom domains supported

## Custom Server Deployment

For deploying to your own server or VPS:

1. Build your Next.js application:

   ```bash
   npm run build
   ```

2. For a Node.js server:
   - Install PM2 for process management:

     ```bash
     npm install -g pm2
     ```

   - Create a `ecosystem.config.js` file:

     ```javascript
     module.exports = {
       apps: [
         {
           name: 'technexus-blog',
           script: 'node_modules/next/dist/bin/next',
           args: 'start',
           instances: 'max',
           exec_mode: 'cluster',
           env: {
             NODE_ENV: 'production',
             PORT: 3000
           }
         }
       ]
     };
     ```

   - Start your application:

     ```bash
     pm2 start ecosystem.config.js
     ```

3. Set up a reverse proxy with Nginx:

   ```nginx
   server {
     listen 80;
     server_name yourdomain.com;

     location / {
       proxy_pass http://localhost:3000;
       proxy_http_version 1.1;
       proxy_set_header Upgrade $http_upgrade;
       proxy_set_header Connection 'upgrade';
       proxy_set_header Host $host;
       proxy_cache_bypass $http_upgrade;
     }
   }
   ```

4. Set up SSL with Let's Encrypt:

   ```bash
   sudo certbot --nginx -d yourdomain.com
   ```

## Continuous Integration/Continuous Deployment (CI/CD)

For automated deployments:

1. **GitHub Actions**: Create a workflow file in `.github/workflows/` to automate testing and deployment
2. **GitLab CI/CD**: Create a `.gitlab-ci.yml` file to define your pipeline
3. **Jenkins**: Set up a Jenkinsfile to define your pipeline

Example GitHub Actions workflow for Vercel deployment:

```yaml
name: Deploy to Vercel

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: 18
          
      - name: Install dependencies
        run: npm ci
        
      - name: Run tests
        run: npm test
        
      - name: Deploy to Vercel
        uses: amondnet/vercel-action@v20
        with:
          vercel-token: ${{ secrets.VERCEL_TOKEN }}
          vercel-org-id: ${{ secrets.ORG_ID }}
          vercel-project-id: ${{ secrets.PROJECT_ID }}
          vercel-args: '--prod'
```

## Post-Deployment Tasks

After deploying your site:

1. **Set up monitoring**:
   - Vercel Analytics
   - Google Analytics
   - Sentry for error tracking

2. **Configure SEO**:
   - Verify site in Google Search Console
   - Create a sitemap.xml
   - Implement meta tags and structured data

3. **Performance optimization**:
   - Run Lighthouse audits
   - Optimize images and assets
   - Implement caching strategies

4. **Security**:
   - Set up Content Security Policy
   - Configure CORS headers
   - Implement rate limiting for API routes

## Troubleshooting Deployment Issues

### Common Deployment Problems

1. **Build failures**:
   - Check build logs for errors
   - Ensure all dependencies are properly installed
   - Verify environment variables are correctly set

2. **Missing environment variables**:
   - Double-check that all required environment variables are set in your hosting platform

3. **API routes not working**:
   - Ensure your hosting platform supports serverless functions
   - Check API route paths and methods

4. **Image optimization issues**:
   - For static exports, set `unoptimized: true` in the images config
   - For Vercel/Netlify, ensure proper configuration for image optimization

5. **Routing problems**:
   - Check `basePath` and `assetPrefix` in your Next.js config
   - Verify that your hosting platform is configured for SPA routing

For any other issues, consult the [Next.js deployment documentation](https://nextjs.org/docs/deployment) or reach out to your hosting provider's support.
