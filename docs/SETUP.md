# TechNexus Blog – Setup & Development Guide

## 1. Service Descriptions

- **app**: Next.js frontend application (SSR/SSG, API routes, etc.)
- **postgres**: PostgreSQL database for all app, Supabase, and Prisma data
- **supabase**: Self-hosted Supabase backend (Auth, Storage, Realtime, etc.)
- **meilisearch**: Fast, typo-tolerant search engine
- **redis**: In-memory cache and queue backend
- **mailhog**: Local SMTP server for email testing (optional)
- **adminer/pgadmin**: Web GUIs for PostgreSQL management (optional)
- **prometheus**: Metrics collection and alerting system
- **grafana**: Data visualization and dashboarding tool for metrics

## 2. Starting & Stopping the Stack

Start all services:

```sh
docker compose up --build
```

Stop all services:

```sh
docker compose down
```

Start a specific service (e.g., app):

```sh
docker compose up app
```

## 3. Running Dev Tools

- **Next.js Dev Server:**

  ```sh
  npm run dev
  ```

- **Build for Production:**

  ```sh
  npm run build && npm run start
  ```

- **Run Tests (Vitest):**

  ```sh
  npm run test
  ```

- **Storybook:**

  ```sh
  npm run storybook
  ```

- **Prisma Studio (DB GUI):**

  ```sh
  npm run prisma:studio
  ```

- **Prisma Migrate:**

  ```sh
  npm run prisma:migrate
  ```

- **MSW (Mock Service Worker):**

  ```sh
  npm run msw
  ```

- **Lint & Format:**

  ```sh
  npm run lint
  npm run format
  ```

## 4. Monitoring & Error Reporting

### Sentry (Error Monitoring)

Sentry is integrated for real-time error tracking and performance monitoring.

1. **Installation:** The `@sentry/nextjs` SDK has been added to `package.json`.
2. **Configuration:**
    - Obtain your Sentry DSN (Data Source Name) from your Sentry project settings.
    - Add your DSN to your `.env` file: `SENTRY_DSN=your_sentry_dsn_here`.
    - Initialize Sentry in your Next.js application (e.g., in `sentry.client.config.js`, `sentry.server.config.js`, and `sentry.edge.config.js` if applicable). Refer to the [Sentry Next.js documentation](https://docs.sentry.io/platforms/javascript/guides/nextjs/) for detailed setup.
    - For backend services (e.g., Node.js, Supabase functions), ensure the appropriate Sentry SDK is used and configured with the same DSN.

### Prometheus & Grafana (Metrics/Monitoring)

Prometheus and Grafana are set up via Docker Compose for collecting and visualizing application metrics.

1. **Setup:**
    - The `prometheus` and `grafana` services have been added to `.devcontainer/docker-compose.yml`.
    - A `prometheus.yml` configuration file is located in `.devcontainer/prometheus.yml`, set up to scrape metrics.
2. **Usage:**
    - Start the monitoring stack using `docker compose up prometheus grafana` from the `.devcontainer` directory, or by rebuilding your dev container.
    - Access Grafana at `http://localhost:3001`.
    - Log in with default credentials (`admin`/`admin`). You will be prompted to change the password.
    - Add Prometheus as a data source in Grafana:
        - Go to `Connections` -> `Data sources` -> `Add new data source` -> `Prometheus`.
        - Set the URL to `http://prometheus:9090` (this is the service name within the Docker network).
        - Save & Test.
    - You can now create dashboards in Grafana to visualize metrics scraped by Prometheus.
3. **Exposing Metrics:**
    - For your Next.js application or Node.js backend, you will need to expose a `/metrics` endpoint. Libraries like `prom-client` can be used for Node.js applications to easily expose custom metrics.

## 5. Environment Variables

- All core variables are in `.env` (backend/services) and `.env.local`
  (frontend).
- To add a new variable:
  - For backend/services: add to `.env` and reference in Docker Compose or
    service config.
  - For frontend: add to `.env.local` and prefix with `NEXT_PUBLIC_` for browser
    access.
- After changing `.env`, restart affected services.

## 5. Security Notes (Production)

- **Never use default passwords or secrets in production.**
- Use strong, unique values for DB, Supabase, Meilisearch, and Redis secrets.
- Store secrets securely (e.g., GitHub Actions secrets, .env.production not
  committed to git).
- Restrict exposed ports in production (use firewalls, reverse proxies).
- For Supabase, decouple the database from Docker Compose and use a managed DB
  if possible.
- Secure Supabase Studio and Adminer/pgAdmin with authentication or VPN.
- Regularly update images and dependencies.

## 6. Troubleshooting & Tips

- Check logs with:

  ```sh
  docker compose logs <service>
  ```

- If a service fails to start, check for port conflicts or missing env vars.
- For persistent data, named volumes are used (see `docker-compose.yml`).
- For more info, see official docs:
  - [Supabase Self-Hosting](https://supabase.com/docs/guides/self-hosting/docker)
  - [Meilisearch Docker](https://www.meilisearch.com/docs/guides/misc/docker)
  - [Prisma Deployment Guides](https://www.prisma.io/docs/guides/deployment) (Note: The specific Docker guide link was broken; this is a general deployment guide. Please verify the exact Docker link on Prisma's official documentation.)
  - [Next.js Docker](https://nextjs.org/docs/deployment#docker-image)

---

For onboarding, workflow, or stack questions, see this file or contact the
maintainers.
