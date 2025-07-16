# TechNexus Blog

This is a full-stack blog application built with Next.js, TypeScript, and a GraphQL backend.

## Key Technologies

* **Frontend:** Next.js, React, TypeScript, Tailwind CSS
* **Backend:** Node.js, GraphQL, Prisma
* **Testing:** Vitest, Jest

## Getting Started

To get the development environment running, you will likely need to install dependencies and start the development servers for both the frontend and backend.

### Frontend

From the root directory:

```bash
npm install
npm run dev
```

### Backend

From the `backend` directory:

```bash
cd backend
npm install
# You may need to run database migrations
npx prisma migrate dev
# Start the backend server
npm run dev
```
