# Phase 1 Architecture Decisions

This document outlines the finalized cloud provider and core service choices for the TechNexus Blog platform's initial phase, based on the system design and technology stack documentation. These decisions are critical for infrastructure provisioning and development.

## 1. Cloud Provider

* **Choice**: **AWS (Amazon Web Services)**
* **Rationale**: AWS provides a robust, scalable, and reliable foundation for hosting the various services required by the TechNexus Blog. It serves as the underlying infrastructure for both Vercel (frontend hosting) and Supabase (BaaS), offering a comprehensive ecosystem of managed services that align with our architectural goals.

## 2. Core Services

### 2.1 Frontend Hosting & Content Delivery Network (CDN)

* **Choice**: **Vercel (Edge Network)**
* **Rationale**: Vercel is optimized for Next.js applications, providing seamless deployment, global edge delivery for low-latency content access, and integrated CI/CD pipelines. This ensures a fast and responsive user experience for the blog's frontend.

### 2.2 Backend-as-a-Service (BaaS)

* **Choice**: **Supabase**
* **Services Utilized**:
  * **PostgreSQL**: For structured relational data storage (articles, users, categories, etc.).
  * **Supabase Auth**: For user authentication, authorization, and management, including OAuth integrations.
  * **Supabase Storage**: For storing media assets like images and other files.
  * **Supabase Edge Functions**: For custom backend logic and API endpoints that require server-side execution.
* **Rationale**: Supabase accelerates development by providing managed, scalable backend services. Its integrated features, such as Row Level Security (RLS) for fine-grained access control and real-time capabilities, simplify backend management and enhance security.

### 2.3 Search Engine

* **Choice**: **Meilisearch**
* **Rationale**: Meilisearch offers high-performance, typo-tolerant search capabilities, making it ideal for providing relevant search results across the blog's technical content. Its ease of integration and configuration further supports rapid development.

### 2.4 Caching & In-Memory Store

* **Choice**: **Redis**
* **Rationale**: Redis will be used for caching frequently accessed data, session management, and potentially for real-time features. Its in-memory nature provides extremely fast read/write operations, significantly improving application responsiveness and reducing database load.

## 3. Access for Infrastructure Provisioning

This document is stored in the `docs/` directory of the project repository. Malik can access it directly from the project's file structure for infrastructure provisioning tasks.
