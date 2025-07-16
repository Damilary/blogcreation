# TechNexus Blog Authentication

This document outlines the JWT-based authentication system for the TechNexus Blog.

## Token Structure

The JWT is signed with the `JWT_SECRET` environment variable and contains the following payload:

```json
{
  "userId": "cuid-or-uuid-of-the-user",
  "iat": 1678886400,
  "exp": 1679491200
}
```

- **userId**: The unique identifier for the user.
- **iat**: "Issued At" timestamp (epoch time).
- **exp**: "Expiration Time" timestamp (epoch time). The token is valid for 7 days.

## HTTP-Only Cookie

The JWT is stored in an HTTP-only cookie named `token`. This provides protection against XSS attacks because the cookie cannot be accessed by client-side JavaScript.

- **Name**: `token`
- **HttpOnly**: `true`
- **Secure**: `true` in production, `false` in development.
- **SameSite**: `Lax`
- **Max-Age**: 7 days

## GraphQL Endpoints

### Queries

- **`me`**: Retrieves the currently authenticated user's profile. It returns a `User` object or `null` if not authenticated.

  ```graphql
  query Me {
    me {
      id
      email
      username
      role
    }
  }
  ```

### Mutations

- **`signup(input: CreateUserInput!): AuthPayload!`**: Creates a new user and returns a JWT and user object.
- **`login(email: String!, password: String!): AuthPayload!`**: Authenticates a user and returns a JWT and user object.
- **`logout: Boolean!`**: Clears the authentication cookie.

## Protected Routes & Middleware

The backend uses a middleware to protect GraphQL resolvers. On every request, the middleware inspects the `token` cookie, verifies the JWT, and attaches the authenticated user's data to the GraphQL context.

If a resolver requires authentication, it can check for the `user` object in the context. If the user is not present, the resolver should throw an `UNAUTHENTICATED` error.

Example of a protected resolver:
```typescript
// Example from article resolvers
createArticle: async (
  _: never,
  { input }: { input: ArticleInput },
  { prisma, user }: Context
) => {
  if (!user) {
    throw new GraphQLError("Authentication required.", {
      extensions: { code: "UNAUTHENTICATED" },
    });
  }
  // ... rest of the logic
}
``` 