# Authentication Implementation Guide

This document outlines the implementation details for user authentication within the TechNexus Blog platform, covering registration, login, JWT handling, and security considerations.

## 1. Authentication Flow

The authentication system uses a combination of traditional email/password login and JSON Web Tokens (JWT) for securing API access.

### 1.1. User Registration

1. **Frontend (Client):**
    * User provides `name`, `email`, and `password` through the registration form (`src/components/auth/RegisterForm.tsx`).
    * Client-side validation is performed using Zod.
    * A `POST` request is sent to the `/register` endpoint on the backend.

2. **Backend (`backend/src/index.ts`):**
    * Receives the `name`, `email`, and `password`.
    * Validates input and checks if a user with the provided email already exists.
    * Hashes the password using `bcryptjs` (10 salt rounds).
    * Creates a new `User` record in the PostgreSQL database via Prisma.
    * Generates a JWT containing `userId` and `userRole` (defaulting to `USER`). The token is signed with `process.env.JWT_SECRET` and set to expire in 1 hour.
    * Returns the JWT and basic user information upon successful registration.

### 1.2. User Login

1. **Frontend (Client):**
    * User provides `email` and `password` through the login form (`src/components/auth/LoginForm.tsx`).
    * Client-side validation is performed using Zod.
    * A `POST` request is sent to the `/login` endpoint on the backend.

2. **Backend (`backend/src/index.ts`):**
    * Receives the `email` and `password`.
    * Retrieves the user record from the database based on the email.
    * Compares the provided password with the stored hashed password using `bcrypt.compare()`.
    * If credentials are valid, generates a new JWT containing `userId` and `userRole`. The token is signed with `process.env.JWT_SECRET` and set to expire in 1 hour.
    * Returns the JWT and basic user information upon successful login.

## 2. Token Handling (JWT)

* **Generation:** JWTs are generated on the backend using `jsonwebtoken` upon successful user registration or login.
* **Storage (Frontend):** The generated JWT is now stored in HTTP-only cookies for enhanced security against XSS attacks.
* **Transmission:** The JWT is automatically sent with subsequent API requests via cookies.
* **Validation (Backend):**
  * In `backend/src/index.ts`, the Apollo Server context extracts the token from the incoming request's cookies.
  * `jwt.verify()` is used to validate the token against `process.env.JWT_SECRET`.
  * If valid, the `userId` from the token is used to fetch the user from the database, and the user object (including roles) is attached to the `context` for GraphQL resolvers.
  * If the token is invalid or expired, the `user` object in the context will be `null`, leading to unauthorized access for protected routes.

## 3. Security Considerations

### 3.1. Password Security

* **Hashing:** Passwords are hashed using `bcryptjs` with a salt round of 10. This makes it computationally intensive to crack passwords even if the database is compromised.
* **No Plaintext Storage:** Passwords are never stored in plaintext.

### 3.2. JWT Security

* **Secret Key:** The `JWT_SECRET` is stored as an environment variable (`.env`) and is crucial for signing and verifying tokens. It must be kept confidential and never exposed in client-side code. A check is in place in `backend/src/index.ts` to ensure `JWT_SECRET` is defined.
* **Expiration:** Tokens have a short expiration time (1 hour) to limit the window of opportunity for token misuse if intercepted.
* **No Sensitive Data in Payload:** Only non-sensitive user `id` and `role` are stored in the JWT payload. Sensitive information is fetched from the database using the `userId` from the token.
* **HTTPS:** All communication between the frontend and backend should occur over HTTPS to prevent man-in-the-middle attacks and ensure token confidentiality during transit.

### 3.3. API Endpoint Security

* **Authentication Middleware:** The Apollo Server context (`backend/src/index.ts`) includes logic to extract and validate the JWT from incoming request cookies.
* **Authorization Checks:** GraphQL mutations (e.g., `createArticle`, `updateArticle`, `deleteArticle` in `backend/src/graphql/resolvers/article.ts`) explicitly check the `user` object in the context and its `roles` to ensure only authorized users (e.g., `ADMIN`) can perform certain actions.
* **Input Validation:** Frontend forms use Zod for client-side validation, and backend endpoints use `validateInput` with Zod schemas (`backend/src/validation/articleValidation.ts`) to prevent common vulnerabilities like injection attacks.
* **Rate Limiting:** (Future consideration, as per `TechNexus_Blog_tech_stack.md`) Implement rate limiting on authentication endpoints to prevent brute-force attacks.
* **Helmet:** The Express app uses `helmet` middleware to set various HTTP security headers, protecting against common web vulnerabilities.

## 4. Frontend Integration

* **Forms:** `LoginForm.tsx` and `RegisterForm.tsx` provide the UI for user input.
* **Page Integration:** The `src/app/auth/page.tsx` serves as the entry point for authentication, allowing users to switch between login and registration forms.
* **Redirection:** Upon successful login or registration, the user is redirected to a relevant page (e.g., `/dashboard`).
* **Toast Notifications:** `react-hot-toast` is used to provide user feedback on success or failure of authentication attempts.

## 5. Future Enhancements

* **Refresh Tokens:** Implement refresh tokens for a more seamless user experience without compromising security (allowing longer session times without frequent re-logins).
* **OAuth Integration:** Integrate with third-party OAuth providers (Google, GitHub, etc.) as outlined in `TechNexus_Blog_tech_stack.md`.
* **MFA:** Add Multi-Factor Authentication for enhanced security.
* **Password Reset:** Implement a secure password reset mechanism.
* **Account Verification:** Email verification for new registrations.
