# TechNexus Blog

![TechNexus Banner](public/images/banner.png)

TechNexus Blog is a modern web application built with Next.js 15 and React 19, powered by GraphQL and Prisma. It provides a platform for publishing and consuming technology-related content.

## Features

- 🚀 Built with Next.js 15 and React 19
- 🎨 Styled with Tailwind CSS v4
- 🔍 GraphQL API with type safety
- 📱 Fully responsive design
- 🌙 Dark mode support
- ✅ Comprehensive testing with Jest and React Testing Library
- 📚 Component documentation with Storybook
- 🔄 MSW for API mocking

## Project Documentation

This project is documented using several files.

- **[Project Documents](./docs)**: All project documents, including implementation plans, design documents, and validation reports are located in the `docs` directory.
- **[Changelog](./CHANGELOG.md)**: A detailed log of all changes in the project.
- **[Contributing Guide](./CONTRIBUTING.md)**: Guidelines for contributing to the project.
- **[Code of Conduct](./CODE_OF_CONDUCT.md)**: The code of conduct for all contributors.

## Getting Started

### Prerequisites

- Node.js 18.17 or later
- npm, yarn, or pnpm

### Installation

1. Clone the repository:

```bash
git clone https://github.com/Damilary/technexus-blog.git
cd technexus-blog
```

2. Install dependencies:

```bash
npm install
# or
yarn
# or
pnpm install
```

3. Create a `.env.local` file in the project root:

```
NEXT_PUBLIC_API_URL=https://api.technexus-blog.com/graphql
NEXT_PUBLIC_APP_URL=http://localhost:3000
NEXT_PUBLIC_API_MOCKING=enabled
```

### Development

Start the development server:

```bash
npm run dev
# or
yarn dev
# or
pnpm dev
```

Open [http://localhost:3000](http://localhost:3000) in your browser to see the result.

### Testing

Run tests:

```bash
npm run test
# or
yarn test
# or
pnpm test
```

### Storybook

Run Storybook to view and develop components in isolation:

```bash
npm run storybook
# or
yarn storybook
# or
pnpm storybook
```

## Project Structure

```
technexus-blog/
├── .husky/             # Git hooks
├── .storybook/         # Storybook configuration
├── docs/               # Project documentation
├── public/             # Static assets
├── src/
│   ├── app/            # Next.js app directory
│   ├── components/     # React components
│   │   ├── ui/         # UI components
│   │   ├── layout/     # Layout components
│   │   └── features/   # Feature-specific components
│   ├── hooks/          # Custom React hooks
│   ├── lib/            # Utility libraries
│   │   ├── api/        # API related code
│   │   └── ...
│   ├── mocks/          # MSW API mocks
│   ├── store/          # State management
│   └── types/          # TypeScript type definitions
└── ...
```

## Contributing

Please read our [Contributing Guide](./CONTRIBUTING.md) and the [Code of Conduct](./CODE_OF_CONDUCT.md) for details on our code of conduct and the process for submitting pull requests.

## License

This project is licensed under the ISC License - see the [LICENSE](LICENSE) file for details.
