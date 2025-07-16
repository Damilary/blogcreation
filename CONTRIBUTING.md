# Contributing to TechNexus Blog

Thank you for considering contributing to TechNexus Blog! This document outlines the process for contributing to the project.

## Code of Conduct

By participating in this project, you are expected to uphold our [Code of Conduct](CODE_OF_CONDUCT.md).

## Development Process

We follow a feature branch workflow:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## Commit Message Guidelines

We follow the [Conventional Commits](https://www.conventionalcommits.org/) specification for our commit messages:

```
<type>[optional scope]: <description>

[optional body]

[optional footer(s)]
```

### Types

- `feat`: A new feature
- `fix`: A bug fix
- `docs`: Documentation only changes
- `style`: Changes that do not affect the meaning of the code (white-space, formatting, etc)
- `refactor`: A code change that neither fixes a bug nor adds a feature
- `perf`: A code change that improves performance
- `test`: Adding missing tests or correcting existing tests
- `build`: Changes that affect the build system or external dependencies
- `ci`: Changes to our CI configuration files and scripts
- `chore`: Other changes that don't modify src or test files
- `revert`: Reverts a previous commit

### Examples

```
feat(articles): add support for draft articles

fix(auth): resolve issue with token refresh

docs: update README with new API information
```

## Pull Request Process

1. Update the README.md or documentation with details of changes if appropriate
2. Update the CHANGELOG.md with details of changes
3. The PR should work with all tests passing
4. PRs require approval from at least one maintainer

## Code Style

We use ESLint and Prettier to enforce code style:

```bash
# Check code style
npm run lint

# Fix code style issues
npm run lint:fix

# Format code
npm run format
```

## Testing

Please write tests for your changes:

```bash
# Run tests
npm run test

# Run tests in watch mode
npm run test:watch
```

## Storybook

We use Storybook for component development and documentation:

```bash
# Run Storybook
npm run storybook
```

## Branch Strategy

- `main`: Production-ready code
- `develop`: Integration branch for features
- `feature/*`: Feature branches
- `fix/*`: Bug fix branches
- `release/*`: Release preparation branches
- `hotfix/*`: Hotfix branches for urgent production fixes

## Release Process

1. Create a release branch (`release/vX.Y.Z`)
2. Update version in package.json
3. Update CHANGELOG.md
4. Merge into `main` and tag the release
5. Merge `main` back into `develop` 