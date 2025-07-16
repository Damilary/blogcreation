# TechNexus Blog: Component Migration & Reuse Guide (Enhanced)

**Date:** May 18, 2025

## 1. Introduction

This document provides a detailed, component-by-component guide for migrating from the current React/Vite codebase to the Next.js/TypeScript implementation as outlined in the TechNexus Blog implementation plan. It serves as a practical roadmap for developers, clarifying which components can be reused, which need adaptation, and which require complete rebuilding.

## 2. Migration Approach Overview

### 2.1. General Migration Strategy

1. **TypeScript Conversion**: All components will need TypeScript interfaces and type definitions.
2. **Next.js Adaptation**: Components will need to be adapted for Next.js patterns, including:
   - Server Components vs. Client Components
   - App Router structure
   - Next.js-specific features (Image, Link, etc.)
3. **Design System Implementation**: All components must adhere to the TechNexus Blog design system.
4. **Component Organization**: Follow a structured organization pattern for components:
   ```
   /components
     /ui (reusable UI components)
     /layout (layout components)
     /features (feature-specific components)
     /[feature-name] (components for specific features)
   ```

### 2.2. TypeScript Interface Pattern

For each component, we'll define TypeScript interfaces following this pattern:

```typescript
// Example for a Button component
interface ButtonProps {
  variant: 'primary' | 'secondary' | 'tertiary' | 'icon';
  size?: 'sm' | 'md' | 'lg';
  children: React.ReactNode;
  onClick?: () => void;
  disabled?: boolean;
  className?: string;
  // Additional props as needed
}
```

### 2.3. State Management Approach

The TechNexus Blog will use a combination of state management approaches:

1. **Local Component State**: For UI state that only affects a single component
2. **React Context**: For state that needs to be shared among a limited component tree
3. **Zustand**: For global application state
4. **React Query/SWR**: For server state and data fetching

Each component in this guide includes specific state management recommendations.

### 2.4. Testing Strategy Overview

Testing will follow a comprehensive approach:

1. **Unit Tests**: For individual components and utility functions
2. **Component Tests**: For component rendering and interactions
3. **Integration Tests**: For component interactions and data flow
4. **End-to-End Tests**: For critical user flows

Each component in this guide includes specific testing recommendations.

## 3. Layout Components

### 3.1. Header Component

**Current Implementation**: `src/components/layout/Header.jsx`
- Basic header with logo, navigation links, search input, and user icon
- Mobile hamburger menu with basic functionality

**Migration Path**: **Adapt with Significant Enhancements**

**TypeScript Interface**:
```typescript
interface HeaderProps {
  navigation: {
    main: {
      name: string;
      href: string;
      subCategories?: {
        name: string;
        href: string;
      }[];
    }[];
  };
  user?: {
    id: string;
    name: string;
    avatar?: string;
  } | null;
}
```

**Code Example**:
```tsx
// components/layout/Header.tsx
'use client';

import { useState } from 'react';
import Image from 'next/image';
import Link from 'next/link';
import { useTheme } from '@/hooks/useTheme';
import { useAuth } from '@/hooks/useAuth';
import { SearchBar } from '@/components/ui/SearchBar';
import { Button } from '@/components/ui/Button';
import { UserMenu } from '@/components/features/auth/UserMenu';
import { MobileMenu } from '@/components/layout/MobileMenu';
import { ThemeToggle } from '@/components/ui/ThemeToggle';
import { NewsletterModal } from '@/components/features/newsletter/NewsletterModal';

export const Header = ({ navigation }: HeaderProps) => {
  const { user } = useAuth();
  const { theme, toggleTheme } = useTheme();
  const [mobileMenuOpen, setMobileMenuOpen] = useState(false);
  const [newsletterModalOpen, setNewsletterModalOpen] = useState(false);
  
  return (
    <header className="bg-white dark:bg-gray-900 shadow-sm">
      <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
        <div className="flex justify-between h-16">
          {/* Logo */}
          <div className="flex-shrink-0 flex items-center">
            <Link href="/">
              <Image 
                src="/logo.svg" 
                alt="TechNexus" 
                width={150} 
                height={40} 
                className="h-8 w-auto" 
              />
            </Link>
          </div>
          
          {/* Desktop Navigation */}
          <nav className="hidden md:flex space-x-8">
            {navigation.main.map((item) => (
              <div key={item.name} className="relative group">
                <Link 
                  href={item.href}
                  className="inline-flex items-center px-1 pt-1 text-sm font-medium text-gray-900 dark:text-gray-100"
                >
                  {item.name}
                  {item.subCategories && (
                    <svg className="ml-2 h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                      <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M19 9l-7 7-7-7" />
                    </svg>
                  )}
                </Link>
                
                {/* Dropdown for subcategories */}
                {item.subCategories && (
                  <div className="absolute z-10 hidden group-hover:block pt-2 w-48">
                    <div className="rounded-md shadow-lg bg-white dark:bg-gray-800 ring-1 ring-black ring-opacity-5">
                      <div className="py-1">
                        {item.subCategories.map((subItem) => (
                          <Link
                            key={subItem.name}
                            href={subItem.href}
                            className="block px-4 py-2 text-sm text-gray-700 dark:text-gray-200 hover:bg-gray-100 dark:hover:bg-gray-700"
                          >
                            {subItem.name}
                          </Link>
                        ))}
                      </div>
                    </div>
                  </div>
                )}
              </div>
            ))}
          </nav>
          
          {/* Right side items */}
          <div className="flex items-center space-x-4">
            <SearchBar className="hidden md:block" />
            <ThemeToggle theme={theme} toggleTheme={toggleTheme} />
            <Button 
              variant="tertiary"
              onClick={() => setNewsletterModalOpen(true)}
              className="hidden md:block"
            >
              Subscribe
            </Button>
            
            {user ? (
              <UserMenu user={user} />
            ) : (
              <div className="hidden md:flex space-x-2">
                <Button variant="secondary" href="/login">
                  Log in
                </Button>
                <Button variant="primary" href="/signup">
                  Sign up
                </Button>
              </div>
            )}
            
            {/* Mobile menu button */}
            <button
              type="button"
              className="md:hidden p-2 rounded-md text-gray-400 hover:text-gray-500 hover:bg-gray-100 dark:hover:bg-gray-700"
              onClick={() => setMobileMenuOpen(true)}
            >
              <span className="sr-only">Open main menu</span>
              <svg className="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M4 6h16M4 12h16M4 18h16" />
              </svg>
            </button>
          </div>
        </div>
      </div>
      
      {/* Mobile menu */}
      <MobileMenu 
        isOpen={mobileMenuOpen} 
        setIsOpen={setMobileMenuOpen} 
        navigation={navigation} 
        user={user} 
      />
      
      {/* Newsletter modal */}
      <NewsletterModal 
        isOpen={newsletterModalOpen} 
        setIsOpen={setNewsletterModalOpen} 
      />
    </header>
  );
};
```

**State Management**:
- **Local State**: For mobile menu toggle, newsletter modal toggle
- **Zustand Store**: For user authentication state, theme state
- **Example Zustand Store**:
```typescript
// store/useAuthStore.ts
import { create } from 'zustand';
import { persist } from 'zustand/middleware';

interface User {
  id: string;
  name: string;
  avatar?: string;
}

interface AuthState {
  user: User | null;
  isLoading: boolean;
  error: string | null;
  login: (email: string, password: string) => Promise<void>;
  logout: () => Promise<void>;
  signup: (email: string, password: string, name: string) => Promise<void>;
}

export const useAuthStore = create<AuthState>()(
  persist(
    (set) => ({
      user: null,
      isLoading: false,
      error: null,
      login: async (email, password) => {
        set({ isLoading: true, error: null });
        try {
          // API call to login
          const user = await loginApi(email, password);
          set({ user, isLoading: false });
        } catch (error) {
          set({ error: error.message, isLoading: false });
        }
      },
      logout: async () => {
        set({ isLoading: true });
        try {
          // API call to logout
          await logoutApi();
          set({ user: null, isLoading: false });
        } catch (error) {
          set({ error: error.message, isLoading: false });
        }
      },
      signup: async (email, password, name) => {
        set({ isLoading: true, error: null });
        try {
          // API call to signup
          const user = await signupApi(email, password, name);
          set({ user, isLoading: false });
        } catch (error) {
          set({ error: error.message, isLoading: false });
        }
      },
    }),
    {
      name: 'auth-storage',
      partialize: (state) => ({ user: state.user }),
    }
  )
);
```

**Required Enhancements**:
1. Add dropdown menus for categories with sub-category previews
2. Implement user authentication area (login/signup or user profile)
3. Add dark mode toggle
4. Add newsletter subscription button with modal trigger
5. Enhance mobile menu with sub-category previews and user-specific links

**Design System References**:
- Colors: Primary Blue (#2563EB) for active links
- Typography: Inter Medium (500) for navigation links
- Components: Dropdown menus, buttons, search bar
- Mockups: `header_desktop.png`, `header_mobile.png`

**Next.js Specific Changes**:
- Use Next.js Link component for navigation
- Split into server and client components (server for initial render, client for interactive elements)
- Use Next.js Image component for logo

**Testing Strategy**:
- **Unit Tests**: Test individual functions like menu toggle, theme toggle
- **Component Tests**: Test rendering of navigation items, dropdowns, and responsive behavior
- **Integration Tests**: Test user menu interactions, search functionality
- **Test Example**:
```typescript
// __tests__/components/layout/Header.test.tsx
import { render, screen, fireEvent } from '@testing-library/react';
import { Header } from '@/components/layout/Header';

// Mock the hooks
jest.mock('@/hooks/useAuth', () => ({
  useAuth: () => ({ user: null })
}));

jest.mock('@/hooks/useTheme', () => ({
  useTheme: () => ({ theme: 'light', toggleTheme: jest.fn() })
}));

const mockNavigation = {
  main: [
    { name: 'Home', href: '/' },
    { 
      name: 'Web Development', 
      href: '/web-development',
      subCategories: [
        { name: 'Frontend', href: '/web-development/frontend' },
        { name: 'Backend', href: '/web-development/backend' }
      ]
    }
  ]
};

describe('Header Component', () => {
  it('renders the logo and navigation links', () => {
    render(<Header navigation={mockNavigation} />);
    
    expect(screen.getByAltText('TechNexus')).toBeInTheDocument();
    expect(screen.getByText('Home')).toBeInTheDocument();
    expect(screen.getByText('Web Development')).toBeInTheDocument();
  });
  
  it('opens mobile menu when hamburger is clicked', () => {
    render(<Header navigation={mockNavigation} />);
    
    const menuButton = screen.getByLabelText('Open main menu');
    fireEvent.click(menuButton);
    
    // Check if mobile menu is visible
    expect(screen.getByRole('dialog')).toBeInTheDocument();
  });
  
  it('shows login and signup buttons when user is not authenticated', () => {
    render(<Header navigation={mockNavigation} />);
    
    expect(screen.getByText('Log in')).toBeInTheDocument();
    expect(screen.getByText('Sign up')).toBeInTheDocument();
  });
});
```

### 3.2. Footer Component

**Current Implementation**: `src/components/layout/Footer.jsx`
- Basic footer with logo, site description, social links, and multi-column links

**Migration Path**: **Adapt with Moderate Enhancements**

**TypeScript Interface**:
```typescript
interface FooterProps {
  navigation: {
    section: string;
    links: {
      name: string;
      href: string;
    }[];
  }[];
  socialLinks: {
    platform: string;
    href: string;
    icon: React.ComponentType<any>;
  }[];
}
```

**Code Example**:
```tsx
// components/layout/Footer.tsx
import Link from 'next/link';
import Image from 'next/image';
import { 
  FaGithub, 
  FaInstagram, 
  FaFacebook, 
  FaTwitter, 
  FaLinkedin, 
  FaEnvelope 
} from 'react-icons/fa';

export const Footer = ({ navigation, socialLinks }: FooterProps) => {
  return (
    <footer className="bg-gray-900 text-white">
      <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-12">
        <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-8">
          {/* Logo and description */}
          <div className="col-span-1 md:col-span-2 lg:col-span-1">
            <Link href="/">
              <Image 
                src="/logo-white.svg" 
                alt="TechNexus" 
                width={150} 
                height={40} 
                className="h-8 w-auto" 
              />
            </Link>
            <p className="mt-4 text-gray-400 text-sm">
              TechNexus is your go-to resource for the latest in technology, 
              providing in-depth articles, tutorials, and insights across various domains.
            </p>
            
            {/* Social links */}
            <div className="mt-6 flex space-x-4">
              {socialLinks.map((item) => {
                const Icon = item.icon;
                return (
                  <a 
                    key={item.platform} 
                    href={item.href} 
                    target="_blank" 
                    rel="noopener noreferrer"
                    className="text-gray-400 hover:text-white transition-colors"
                  >
                    <span className="sr-only">{item.platform}</span>
                    <Icon className="h-6 w-6" />
                  </a>
                );
              })}
            </div>
          </div>
          
          {/* Navigation sections */}
          {navigation.map((section) => (
            <div key={section.section}>
              <h3 className="text-sm font-medium text-gray-200 uppercase tracking-wider">
                {section.section}
              </h3>
              <ul className="mt-4 space-y-2">
                {section.links.map((item) => (
                  <li key={item.name}>
                    <Link 
                      href={item.href}
                      className="text-gray-400 hover:text-white transition-colors"
                    >
                      {item.name}
                    </Link>
                  </li>
                ))}
              </ul>
            </div>
          ))}
        </div>
        
        <div className="mt-12 pt-8 border-t border-gray-800 flex flex-col md:flex-row justify-between items-center">
          <p className="text-gray-400 text-sm">
            &copy; {new Date().getFullYear()} TechNexus. All rights reserved.
          </p>
          <div className="mt-4 md:mt-0 flex space-x-6">
            <Link 
              href="/privacy-policy"
              className="text-gray-400 hover:text-white text-sm"
            >
              Privacy Policy
            </Link>
            <Link 
              href="/terms-of-service"
              className="text-gray-400 hover:text-white text-sm"
            >
              Terms of Service
            </Link>
          </div>
        </div>
      </div>
    </footer>
  );
};
```

**State Management**:
- **None Required**: Footer is primarily a static component
- **Data Source**: Navigation and social links data should be fetched at the page level and passed as props

**Required Enhancements**:
1. Update social media links to include all specified platforms
2. Ensure all footer links point to actual pages
3. Improve responsive behavior for mobile devices

**Design System References**:
- Colors: Dark (#1E293B) for text, Medium (#64748B) for secondary text
- Typography: Inter Regular (400) for links, Inter Medium (500) for section headings
- Components: Multi-column layout, social media icons
- Mockups: Footer section in homepage mockup

**Next.js Specific Changes**:
- Use Next.js Link component for navigation
- Implement as a server component for better performance

**Testing Strategy**:
- **Unit Tests**: Verify rendering of all navigation sections and links
- **Snapshot Testing**: Ensure consistent visual appearance
- **Test Example**:
```typescript
// __tests__/components/layout/Footer.test.tsx
import { render, screen } from '@testing-library/react';
import { Footer } from '@/components/layout/Footer';

const mockNavigation = [
  {
    section: 'Resources',
    links: [
      { name: 'Blog', href: '/blog' },
      { name: 'Tutorials', href: '/tutorials' }
    ]
  },
  {
    section: 'Company',
    links: [
      { name: 'About', href: '/about' },
      { name: 'Contact', href: '/contact' }
    ]
  }
];

const mockSocialLinks = [
  { platform: 'GitHub', href: 'https://github.com', icon: FaGithub },
  { platform: 'LinkedIn', href: 'https://linkedin.com', icon: FaLinkedin }
];

describe('Footer Component', () => {
  it('renders all navigation sections and links', () => {
    render(<Footer navigation={mockNavigation} socialLinks={mockSocialLinks} />);
    
    expect(screen.getByText('Resources')).toBeInTheDocument();
    expect(screen.getByText('Company')).toBeInTheDocument();
    expect(screen.getByText('Blog')).toBeInTheDocument();
    expect(screen.getByText('About')).toBeInTheDocument();
  });
  
  it('renders all social links', () => {
    render(<Footer navigation={mockNavigation} socialLinks={mockSocialLinks} />);
    
    expect(screen.getByLabelText('GitHub')).toBeInTheDocument();
    expect(screen.getByLabelText('LinkedIn')).toBeInTheDocument();
  });
  
  it('displays the current year in the copyright notice', () => {
    render(<Footer navigation={mockNavigation} socialLinks={mockSocialLinks} />);
    
    const currentYear = new Date().getFullYear().toString();
    expect(screen.getByText(new RegExp(`© ${currentYear} TechNexus`))).toBeInTheDocument();
  });
});
```

### 3.3. Top Picks Section (New Component)

**Current Implementation**: Not present in current codebase

**Migration Path**: **Build New**

**TypeScript Interface**:
```typescript
interface TopPicksProps {
  articles: {
    id: string;
    title: string;
    slug: string;
    excerpt?: string;
    coverImage?: string;
    category: {
      name: string;
      slug: string;
    };
  }[];
}
```

**Code Example**:
```tsx
// components/features/home/TopPicks.tsx
'use client';

import { useState, useRef } from 'react';
import Image from 'next/image';
import Link from 'next/link';
import { motion } from 'framer-motion';
import { CategoryTag } from '@/components/ui/CategoryTag';

export const TopPicks = ({ articles }: TopPicksProps) => {
  const [currentIndex, setCurrentIndex] = useState(0);
  const carouselRef = useRef<HTMLDivElement>(null);
  
  const nextSlide = () => {
    setCurrentIndex((prevIndex) => 
      prevIndex === articles.length - 1 ? 0 : prevIndex + 1
    );
  };
  
  const prevSlide = () => {
    setCurrentIndex((prevIndex) => 
      prevIndex === 0 ? articles.length - 1 : prevIndex - 1
    );
  };
  
  return (
    <section className="bg-gradient-to-r from-orange-50 to-orange-100 dark:from-gray-800 dark:to-gray-900 py-12">
      <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
        <div className="flex justify-between items-center mb-8">
          <h2 className="text-3xl font-semibold text-gray-900 dark:text-white">
            Top Picks
          </h2>
          <div className="flex space-x-2">
            <button
              onClick={prevSlide}
              className="p-2 rounded-full bg-white dark:bg-gray-700 shadow-md hover:bg-gray-100 dark:hover:bg-gray-600 transition-colors"
            >
              <svg className="h-5 w-5 text-gray-700 dark:text-gray-200" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M15 19l-7-7 7-7" />
              </svg>
            </button>
            <button
              onClick={nextSlide}
              className="p-2 rounded-full bg-white dark:bg-gray-700 shadow-md hover:bg-gray-100 dark:hover:bg-gray-600 transition-colors"
            >
              <svg className="h-5 w-5 text-gray-700 dark:text-gray-200" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M9 5l7 7-7 7" />
              </svg>
            </button>
          </div>
        </div>
        
        <div className="relative overflow-hidden" ref={carouselRef}>
          <div 
            className="flex transition-transform duration-500 ease-in-out"
            style={{ transform: `translateX(-${currentIndex * 100}%)` }}
          >
            {articles.map((article) => (
              <div 
                key={article.id}
                className="w-full flex-shrink-0 px-4"
              >
                <motion.div 
                  className="bg-white dark:bg-gray-800 rounded-lg shadow-lg overflow-hidden flex flex-col md:flex-row"
                  whileHover={{ scale: 1.02 }}
                  transition={{ duration: 0.3 }}
                >
                  <div className="md:w-2/5 relative h-64 md:h-auto">
                    <Image
                      src={article.coverImage || '/placeholder-image.jpg'}
                      alt={article.title}
                      fill
                      className="object-cover"
                    />
                  </div>
                  <div className="md:w-3/5 p-6">
                    <div className="mb-3">
                      <CategoryTag category={article.category} />
                    </div>
                    <h3 className="text-xl font-semibold mb-3 text-gray-900 dark:text-white">
                      <Link href={`/articles/${article.slug}`}>
                        {article.title}
                      </Link>
                    </h3>
                    {article.excerpt && (
                      <p className="text-gray-600 dark:text-gray-300 mb-4 line-clamp-3">
                        {article.excerpt}
                      </p>
                    )}
                    <Link 
                      href={`/articles/${article.slug}`}
                      className="text-orange-500 hover:text-orange-600 dark:text-orange-400 dark:hover:text-orange-300 font-medium inline-flex items-center"
                    >
                      Read more
                      <svg className="ml-2 h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                        <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M14 5l7 7m0 0l-7 7m7-7H3" />
                      </svg>
                    </Link>
                  </div>
                </motion.div>
              </div>
            ))}
          </div>
          
          {/* Indicators */}
          <div className="flex justify-center mt-6 space-x-2">
            {articles.map((_, index) => (
              <button
                key={index}
                onClick={() => setCurrentIndex(index)}
                className={`h-2 w-2 rounded-full transition-colors ${
                  index === currentIndex 
                    ? 'bg-orange-500 w-4' 
                    : 'bg-gray-300 dark:bg-gray-600'
                }`}
              />
            ))}
          </div>
        </div>
      </div>
    </section>
  );
};
```

**State Management**:
- **Local State**: For carousel current index and controls
- **Data Source**: Articles data should be fetched at the page level and passed as props

**Implementation Requirements**:
1. Create a visually distinct section to be positioned above the footer
2. Implement carousel/slider functionality for multiple items
3. Ensure responsive design for all screen sizes

**Design System References**:
- Colors: Consider using Accent Orange (#F97316) for visual distinction
- Typography: H2 (Section Title) for the section heading
- Components: Article cards, carousel/slider
- Mockups: "Top Picks" section in homepage mockup

**Next.js Specific Changes**:
- Implement as a client component for interactive carousel functionality
- Use Next.js Image component for article images

**Testing Strategy**:
- **Unit Tests**: Test carousel navigation functionality
- **Component Tests**: Verify rendering of article cards and responsive behavior
- **Test Example**:
```typescript
// __tests__/components/features/home/TopPicks.test.tsx
import { render, screen, fireEvent } from '@testing-library/react';
import { TopPicks } from '@/components/features/home/TopPicks';

const mockArticles = [
  {
    id: '1',
    title: 'First Article',
    slug: 'first-article',
    excerpt: 'This is the first article',
    coverImage: '/images/article1.jpg',
    category: { name: 'Web Development', slug: 'web-development' }
  },
  {
    id: '2',
    title: 'Second Article',
    slug: 'second-article',
    excerpt: 'This is the second article',
    coverImage: '/images/article2.jpg',
    category: { name: 'AI/ML', slug: 'ai-ml' }
  }
];

describe('TopPicks Component', () => {
  it('renders the section title and articles', () => {
    render(<TopPicks articles={mockArticles} />);
    
    expect(screen.getByText('Top Picks')).toBeInTheDocument();
    expect(screen.getByText('First Article')).toBeInTheDocument();
  });
  
  it('navigates to the next slide when next button is clicked', () => {
    render(<TopPicks articles={mockArticles} />);
    
    // First article should be visible initially
    expect(screen.getByText('First Article')).toBeVisible();
    
    // Click next button
    const nextButton = screen.getByLabelText('Next slide');
    fireEvent.click(nextButton);
    
    // Second article should now be visible
    expect(screen.getByText('Second Article')).toBeVisible();
  });
  
  it('shows the correct indicator for the active slide', () => {
    render(<TopPicks articles={mockArticles} />);
    
    // First indicator should be active initially
    const indicators = screen.getAllByRole('button', { name: /slide/i });
    expect(indicators[0]).toHaveClass('bg-orange-500');
    expect(indicators[1]).toHaveClass('bg-gray-300');
    
    // Click second indicator
    fireEvent.click(indicators[1]);
    
    // Second indicator should now be active
    expect(indicators[0]).toHaveClass('bg-gray-300');
    expect(indicators[1]).toHaveClass('bg-orange-500');
  });
});
```

## 4. Common UI Components

### 4.1. Button Component

**Current Implementation**: `src/components/common/Button.jsx`
- Basic button with primary and secondary variants
- Limited styling options

**Migration Path**: **Adapt with Enhancements**

**TypeScript Interface**:
```typescript
interface ButtonProps {
  variant: 'primary' | 'secondary' | 'tertiary' | 'icon';
  size?: 'sm' | 'md' | 'lg';
  children?: React.ReactNode;
  onClick?: () => void;
  disabled?: boolean;
  className?: string;
  icon?: React.ReactNode;
  iconPosition?: 'left' | 'right';
  fullWidth?: boolean;
  href?: string;
  type?: 'button' | 'submit' | 'reset';
  ariaLabel?: string;
}
```

**Code Example**:
```tsx
// components/ui/Button.tsx
'use client';

import { forwardRef } from 'react';
import Link from 'next/link';
import { cva, type VariantProps } from 'class-variance-authority';
import { cn } from '@/lib/utils';

const buttonVariants = cva(
  "inline-flex items-center justify-center rounded-md font-medium transition-colors focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-offset-2 disabled:opacity-50 disabled:pointer-events-none",
  {
    variants: {
      variant: {
        primary: "bg-primary-600 text-white hover:bg-primary-700 active:bg-primary-800",
        secondary: "bg-white text-primary-600 border border-primary-600 hover:bg-primary-50 active:bg-primary-100",
        tertiary: "bg-transparent text-primary-600 hover:bg-primary-50 active:bg-primary-100",
        icon: "bg-transparent text-gray-700 hover:bg-gray-100 p-2",
      },
      size: {
        sm: "text-sm h-8 px-3",
        md: "text-base h-10 px-4",
        lg: "text-lg h-12 px-6",
      },
      fullWidth: {
        true: "w-full",
        false: "",
      },
    },
    defaultVariants: {
      variant: "primary",
      size: "md",
      fullWidth: false,
    },
  }
);

export interface ButtonProps extends React.ButtonHTMLAttributes<HTMLButtonElement>, VariantProps<typeof buttonVariants> {
  href?: string;
  icon?: React.ReactNode;
  iconPosition?: 'left' | 'right';
}

const Button = forwardRef<HTMLButtonElement, ButtonProps>(
  ({ className, variant, size, fullWidth, children, href, icon, iconPosition = 'left', ...props }, ref) => {
    const Comp = href ? Link : 'button';
    const buttonProps = href ? { href } : { ...props };
    
    return (
      <Comp
        className={cn(buttonVariants({ variant, size, fullWidth, className }))}
        ref={ref}
        {...buttonProps}
      >
        {icon && iconPosition === 'left' && (
          <span className="mr-2">{icon}</span>
        )}
        {children}
        {icon && iconPosition === 'right' && (
          <span className="ml-2">{icon}</span>
        )}
      </Comp>
    );
  }
);
Button.displayName = "Button";

export { Button, buttonVariants };
```

**State Management**:
- **None Required**: Button is a stateless UI component
- **Props**: All behavior is controlled via props

**Required Enhancements**:
1. Add tertiary and icon button variants
2. Implement size variations
3. Add icon support (left/right positioning)
4. Ensure proper hover, focus, and disabled states

**Design System References**:
- Colors: Primary Blue (#2563EB) for primary buttons, White (#FFFFFF) with blue border for secondary
- Typography: Inter Medium (500), 16px
- Components: Button variations in design system
- States: Hover, active, disabled states as specified

**Next.js Specific Changes**:
- Implement as a client component for interactive functionality
- Support for both button and Link rendering based on href prop

**Testing Strategy**:
- **Unit Tests**: Test rendering of different variants and sizes
- **Component Tests**: Test click handlers and disabled state
- **Test Example**:
```typescript
// __tests__/components/ui/Button.test.tsx
import { render, screen, fireEvent } from '@testing-library/react';
import { Button } from '@/components/ui/Button';

describe('Button Component', () => {
  it('renders children correctly', () => {
    render(<Button>Click me</Button>);
    expect(screen.getByText('Click me')).toBeInTheDocument();
  });
  
  it('applies the correct class for primary variant', () => {
    render(<Button variant="primary">Primary</Button>);
    const button = screen.getByText('Primary');
    expect(button).toHaveClass('bg-primary-600');
  });
  
  it('applies the correct class for secondary variant', () => {
    render(<Button variant="secondary">Secondary</Button>);
    const button = screen.getByText('Secondary');
    expect(button).toHaveClass('border-primary-600');
  });
  
  it('renders as a link when href is provided', () => {
    render(<Button href="/test">Link Button</Button>);
    const link = screen.getByText('Link Button');
    expect(link.tagName).toBe('A');
    expect(link).toHaveAttribute('href', '/test');
  });
  
  it('calls onClick handler when clicked', () => {
    const handleClick = jest.fn();
    render(<Button onClick={handleClick}>Click me</Button>);
    
    fireEvent.click(screen.getByText('Click me'));
    expect(handleClick).toHaveBeenCalledTimes(1);
  });
  
  it('renders with an icon in the correct position', () => {
    const icon = <svg data-testid="test-icon" />;
    
    // Test left position (default)
    render(<Button icon={icon}>With Icon</Button>);
    const iconElement = screen.getByTestId('test-icon');
    const iconContainer = iconElement.parentElement;
    expect(iconContainer).toHaveClass('mr-2');
    
    // Test right position
    render(<Button icon={icon} iconPosition="right">With Icon</Button>);
    const rightIconElement = screen.getAllByTestId('test-icon')[1];
    const rightIconContainer = rightIconElement.parentElement;
    expect(rightIconContainer).toHaveClass('ml-2');
  });
  
  it('is disabled when disabled prop is true', () => {
    render(<Button disabled>Disabled</Button>);
    expect(screen.getByText('Disabled')).toBeDisabled();
  });
});
```

### 4.2. Card Component

**Current Implementation**: `src/components/common/Card.jsx`
- Basic card with title, content, and optional image
- Limited styling options

**Migration Path**: **Adapt with Significant Enhancements**

**TypeScript Interface**:
```typescript
interface CardProps {
  variant: 'standard' | 'article' | 'featured';
  title: string;
  content?: React.ReactNode;
  image?: string;
  href?: string;
  metadata?: {
    author?: string;
    date?: string;
    readingTime?: string;
  };
  category?: {
    name: string;
    slug: string;
  };
  tags?: {
    name: string;
    slug: string;
  }[];
  className?: string;
  onClick?: () => void;
}
```

**Code Example**:
```tsx
// components/ui/Card.tsx
'use client';

import { useState } from 'react';
import Image from 'next/image';
import Link from 'next/link';
import { motion } from 'framer-motion';
import { CategoryTag } from '@/components/ui/CategoryTag';
import { TagList } from '@/components/ui/TagList';
import { formatDate } from '@/lib/utils';

export const Card = ({
  variant = 'standard',
  title,
  content,
  image,
  href,
  metadata,
  category,
  tags,
  className = '',
  onClick
}: CardProps) => {
  const [isHovered, setIsHovered] = useState(false);
  
  const CardWrapper = ({ children }: { children: React.ReactNode }) => {
    const baseClasses = `
      bg-white dark:bg-gray-800 rounded-lg overflow-hidden
      ${variant === 'featured' ? 'border-l-4 border-primary-600 dark:border-primary-500' : 'border border-gray-200 dark:border-gray-700'}
      ${className}
    `;
    
    if (href) {
      return (
        <Link href={href} className={baseClasses} onClick={onClick}>
          {children}
        </Link>
      );
    }
    
    return (
      <div className={baseClasses} onClick={onClick}>
        {children}
      </div>
    );
  };
  
  const cardContent = (
    <motion.div
      className="h-full flex flex-col"
      onHoverStart={() => setIsHovered(true)}
      onHoverEnd={() => setIsHovered(false)}
      whileHover={{ 
        scale: href || onClick ? 1.02 : 1,
        boxShadow: href || onClick ? '0 10px 15px -3px rgba(0, 0, 0, 0.1), 0 4px 6px -2px rgba(0, 0, 0, 0.05)' : 'none'
      }}
      transition={{ duration: 0.2 }}
    >
      {/* Image for article and featured variants */}
      {image && (variant === 'article' || variant === 'featured') && (
        <div className="relative h-48 w-full">
          <Image
            src={image}
            alt={title}
            fill
            className="object-cover"
          />
          {category && variant === 'article' && (
            <div className="absolute top-4 left-4">
              <CategoryTag category={category} />
            </div>
          )}
        </div>
      )}
      
      <div className={`p-6 flex flex-col ${variant === 'article' ? 'flex-grow' : ''}`}>
        {/* Category for standard and featured variants */}
        {category && variant !== 'article' && (
          <div className="mb-3">
            <CategoryTag category={category} />
          </div>
        )}
        
        {/* Title */}
        <h3 className={`font-medium ${variant === 'featured' ? 'text-xl' : 'text-lg'} mb-3 text-gray-900 dark:text-white`}>
          {title}
        </h3>
        
        {/* Content */}
        {content && (
          <div className="text-gray-600 dark:text-gray-300 mb-4">
            {content}
          </div>
        )}
        
        {/* Tags */}
        {tags && tags.length > 0 && (
          <div className="mt-auto pt-4">
            <TagList tags={tags} />
          </div>
        )}
        
        {/* Metadata */}
        {metadata && (
          <div className="mt-4 flex items-center text-sm text-gray-500 dark:text-gray-400">
            {metadata.author && (
              <span className="mr-4">{metadata.author}</span>
            )}
            {metadata.date && (
              <span className="mr-4">{formatDate(metadata.date)}</span>
            )}
            {metadata.readingTime && (
              <span>{metadata.readingTime}</span>
            )}
          </div>
        )}
      </div>
    </motion.div>
  );
  
  return (
    <CardWrapper>
      {cardContent}
    </CardWrapper>
  );
};
```

**State Management**:
- **Local State**: For hover state to control animations
- **Props**: All content and behavior controlled via props

**Required Enhancements**:
1. Add article card and featured card variants
2. Implement hover states and animations
3. Add support for metadata (author, date, reading time)
4. Add support for categories and tags
5. Ensure proper image handling and aspect ratios

**Design System References**:
- Colors: White (#FFFFFF) background, Light (#E2E8F0) border
- Typography: H4 for card titles, Body Small for excerpts, Caption for metadata
- Components: Standard Card, Article Card, Featured Card in design system
- States: Hover state with increased shadow and slight scale

**Next.js Specific Changes**:
- Use Next.js Image component for optimized images
- Use Next.js Link for card navigation
- Consider server component for initial render, client component for interactive elements

**Testing Strategy**:
- **Unit Tests**: Test rendering of different variants and content
- **Component Tests**: Test link behavior and hover states
- **Test Example**:
```typescript
// __tests__/components/ui/Card.test.tsx
import { render, screen, fireEvent } from '@testing-library/react';
import { Card } from '@/components/ui/Card';

describe('Card Component', () => {
  it('renders the title correctly', () => {
    render(<Card variant="standard" title="Test Card" />);
    expect(screen.getByText('Test Card')).toBeInTheDocument();
  });
  
  it('renders content when provided', () => {
    render(
      <Card 
        variant="standard" 
        title="Test Card" 
        content={<p>Test content</p>} 
      />
    );
    expect(screen.getByText('Test content')).toBeInTheDocument();
  });
  
  it('renders as a link when href is provided', () => {
    render(
      <Card 
        variant="standard" 
        title="Link Card" 
        href="/test" 
      />
    );
    const link = screen.getByText('Link Card').closest('a');
    expect(link).toHaveAttribute('href', '/test');
  });
  
  it('renders category tag when category is provided', () => {
    render(
      <Card 
        variant="standard" 
        title="Test Card" 
        category={{ name: 'Web Development', slug: 'web-dev' }} 
      />
    );
    expect(screen.getByText('Web Development')).toBeInTheDocument();
  });
  
  it('renders metadata when provided', () => {
    render(
      <Card 
        variant="article" 
        title="Test Card" 
        metadata={{
          author: 'John Doe',
          date: '2025-05-18',
          readingTime: '5 min read'
        }} 
      />
    );
    expect(screen.getByText('John Doe')).toBeInTheDocument();
    expect(screen.getByText('May 18, 2025')).toBeInTheDocument();
    expect(screen.getByText('5 min read')).toBeInTheDocument();
  });
  
  it('calls onClick handler when clicked', () => {
    const handleClick = jest.fn();
    render(
      <Card 
        variant="standard" 
        title="Clickable Card" 
        onClick={handleClick} 
      />
    );
    
    fireEvent.click(screen.getByText('Clickable Card'));
    expect(handleClick).toHaveBeenCalledTimes(1);
  });
});
```

## 5. Home Page Components

### 5.1. FeaturedArticle Component

**Current Implementation**: `src/components/home/FeaturedArticle.jsx`
- Basic featured article display with image, title, excerpt
- Limited styling options

**Migration Path**: **Adapt with Enhancements**

**TypeScript Interface**:
```typescript
interface FeaturedArticleProps {
  article: {
    id: string;
    title: string;
    slug: string;
    excerpt: string;
    coverImage: string;
    category: {
      name: string;
      slug: string;
    };
    author: {
      name: string;
      avatar?: string;
    };
    publishedAt: string;
    readingTime: string;
  };
}
```

**Code Example**:
```tsx
// components/features/home/FeaturedArticle.tsx
import Image from 'next/image';
import Link from 'next/link';
import { CategoryTag } from '@/components/ui/CategoryTag';
import { formatDate } from '@/lib/utils';

export const FeaturedArticle = ({ article }: FeaturedArticleProps) => {
  return (
    <div className="relative h-[500px] md:h-[600px] rounded-xl overflow-hidden">
      {/* Background image with gradient overlay */}
      <div className="absolute inset-0">
        <Image
          src={article.coverImage}
          alt={article.title}
          fill
          className="object-cover"
          priority
        />
        <div className="absolute inset-0 bg-gradient-to-t from-black via-black/70 to-transparent" />
      </div>
      
      {/* Content */}
      <div className="relative h-full flex flex-col justify-end p-6 md:p-10">
        <div className="mb-4">
          <CategoryTag category={article.category} />
        </div>
        
        <h1 className="text-3xl md:text-5xl font-bold text-white mb-4">
          <Link href={`/articles/${article.slug}`}>
            {article.title}
          </Link>
        </h1>
        
        <p className="text-gray-200 text-lg mb-6 max-w-3xl">
          {article.excerpt}
        </p>
        
        <div className="flex items-center">
          {article.author.avatar && (
            <div className="mr-4">
              <Image
                src={article.author.avatar}
                alt={article.author.name}
                width={40}
                height={40}
                className="rounded-full"
              />
            </div>
          )}
          
          <div className="text-gray-300">
            <div className="font-medium">{article.author.name}</div>
            <div className="text-sm">
              {formatDate(article.publishedAt)} · {article.readingTime}
            </div>
          </div>
        </div>
        
        <Link 
          href={`/articles/${article.slug}`}
          className="mt-6 inline-flex items-center px-6 py-3 bg-primary-600 hover:bg-primary-700 text-white font-medium rounded-md transition-colors"
        >
          Read Article
          <svg className="ml-2 h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M14 5l7 7m0 0l-7 7m7-7H3" />
          </svg>
        </Link>
      </div>
    </div>
  );
};
```

**State Management**:
- **None Required**: FeaturedArticle is primarily a presentational component
- **Data Source**: Article data should be fetched at the page level and passed as props

**Required Enhancements**:
1. Improve visual design to match mockups
2. Add category badge and metadata display
3. Implement responsive behavior for different screen sizes
4. Add hover effects and animations

**Design System References**:
- Colors: Consider using gradient overlays for text readability on images
- Typography: H1 (Page Title) for featured article title
- Components: Category Tag, metadata styling
- Mockups: Hero section in homepage mockup

**Next.js Specific Changes**:
- Use Next.js Image component for optimized images
- Use Next.js Link for navigation
- Consider server component for initial render

**Testing Strategy**:
- **Unit Tests**: Verify rendering of article data and responsive behavior
- **Snapshot Testing**: Ensure consistent visual appearance
- **Test Example**:
```typescript
// __tests__/components/features/home/FeaturedArticle.test.tsx
import { render, screen } from '@testing-library/react';
import { FeaturedArticle } from '@/components/features/home/FeaturedArticle';

const mockArticle = {
  id: '1',
  title: 'Featured Article Title',
  slug: 'featured-article',
  excerpt: 'This is a featured article excerpt',
  coverImage: '/images/featured.jpg',
  category: { name: 'Web Development', slug: 'web-dev' },
  author: {
    name: 'John Doe',
    avatar: '/images/avatar.jpg'
  },
  publishedAt: '2025-05-18',
  readingTime: '5 min read'
};

describe('FeaturedArticle Component', () => {
  it('renders article title and excerpt', () => {
    render(<FeaturedArticle article={mockArticle} />);
    
    expect(screen.getByText('Featured Article Title')).toBeInTheDocument();
    expect(screen.getByText('This is a featured article excerpt')).toBeInTheDocument();
  });
  
  it('renders category tag', () => {
    render(<FeaturedArticle article={mockArticle} />);
    
    expect(screen.getByText('Web Development')).toBeInTheDocument();
  });
  
  it('renders author information', () => {
    render(<FeaturedArticle article={mockArticle} />);
    
    expect(screen.getByText('John Doe')).toBeInTheDocument();
    expect(screen.getByAltText('John Doe')).toBeInTheDocument();
  });
  
  it('renders formatted date and reading time', () => {
    render(<FeaturedArticle article={mockArticle} />);
    
    expect(screen.getByText(/May 18, 2025/)).toBeInTheDocument();
    expect(screen.getByText(/5 min read/)).toBeInTheDocument();
  });
  
  it('includes a link to the article', () => {
    render(<FeaturedArticle article={mockArticle} />);
    
    const links = screen.getAllByRole('link');
    const articleLinks = links.filter(link => 
      link.getAttribute('href') === '/articles/featured-article'
    );
    
    expect(articleLinks.length).toBeGreaterThan(0);
    expect(screen.getByText('Read Article')).toBeInTheDocument();
  });
});
```

## 6. New Atomic Components

### 6.1. CategoryTag Component (New Component)

**Current Implementation**: Not present in current codebase

**Migration Path**: **Build New**

**TypeScript Interface**:
```typescript
interface CategoryTagProps {
  category: {
    name: string;
    slug: string;
  };
  size?: 'sm' | 'md';
  className?: string;
}
```

**Code Example**:
```tsx
// components/ui/CategoryTag.tsx
import Link from 'next/link';
import { cva, type VariantProps } from 'class-variance-authority';
import { cn } from '@/lib/utils';

const categoryTagVariants = cva(
  "inline-flex items-center rounded-full bg-gray-100 dark:bg-gray-800 text-gray-800 dark:text-gray-200 font-medium",
  {
    variants: {
      size: {
        sm: "text-xs px-2 py-0.5",
        md: "text-sm px-3 py-1",
      },
    },
    defaultVariants: {
      size: "md",
    },
  }
);

export interface CategoryTagProps extends VariantProps<typeof categoryTagVariants> {
  category: {
    name: string;
    slug: string;
  };
  className?: string;
}

export const CategoryTag = ({ category, size, className }: CategoryTagProps) => {
  return (
    <Link
      href={`/categories/${category.slug}`}
      className={cn(categoryTagVariants({ size, className }))}
    >
      {category.name}
    </Link>
  );
};
```

**State Management**:
- **None Required**: CategoryTag is a stateless UI component
- **Props**: All content and behavior controlled via props

**Implementation Requirements**:
1. Create a visually distinct tag for category display
2. Support different sizes for various contexts
3. Link to category page

**Design System References**:
- Colors: Light (#F1F5F9) background, Dark (#1E293B) text
- Typography: Inter Medium (500), 14px
- Components: Category Tag in design system

**Next.js Specific Changes**:
- Use Next.js Link for navigation
- Can be implemented as a server component

**Testing Strategy**:
- **Unit Tests**: Verify rendering and link behavior
- **Test Example**:
```typescript
// __tests__/components/ui/CategoryTag.test.tsx
import { render, screen } from '@testing-library/react';
import { CategoryTag } from '@/components/ui/CategoryTag';

describe('CategoryTag Component', () => {
  it('renders the category name', () => {
    render(
      <CategoryTag 
        category={{ name: 'Web Development', slug: 'web-dev' }} 
      />
    );
    
    expect(screen.getByText('Web Development')).toBeInTheDocument();
  });
  
  it('links to the correct category page', () => {
    render(
      <CategoryTag 
        category={{ name: 'Web Development', slug: 'web-dev' }} 
      />
    );
    
    const link = screen.getByText('Web Development');
    expect(link).toHaveAttribute('href', '/categories/web-dev');
  });
  
  it('applies the correct size class', () => {
    render(
      <CategoryTag 
        category={{ name: 'Web Development', slug: 'web-dev' }}
        size="sm"
      />
    );
    
    const tag = screen.getByText('Web Development');
    expect(tag).toHaveClass('text-xs');
  });
});
```

### 6.2. TagList Component (New Component)

**Current Implementation**: Not present in current codebase

**Migration Path**: **Build New**

**TypeScript Interface**:
```typescript
interface TagListProps {
  tags: {
    name: string;
    slug: string;
  }[];
  maxDisplay?: number;
  size?: 'sm' | 'md';
  className?: string;
}
```

**Code Example**:
```tsx
// components/ui/TagList.tsx
import Link from 'next/link';
import { cva, type VariantProps } from 'class-variance-authority';
import { cn } from '@/lib/utils';

const tagVariants = cva(
  "inline-flex items-center rounded-md bg-gray-100 dark:bg-gray-800 text-gray-600 dark:text-gray-300 hover:bg-gray-200 dark:hover:bg-gray-700 transition-colors",
  {
    variants: {
      size: {
        sm: "text-xs px-2 py-0.5 mr-1 mb-1",
        md: "text-sm px-2.5 py-0.5 mr-2 mb-2",
      },
    },
    defaultVariants: {
      size: "md",
    },
  }
);

export interface TagListProps extends VariantProps<typeof tagVariants> {
  tags: {
    name: string;
    slug: string;
  }[];
  maxDisplay?: number;
  className?: string;
}

export const TagList = ({ tags, maxDisplay, size, className }: TagListProps) => {
  const displayTags = maxDisplay ? tags.slice(0, maxDisplay) : tags;
  const hasMore = maxDisplay && tags.length > maxDisplay;
  
  return (
    <div className={cn("flex flex-wrap", className)}>
      {displayTags.map((tag) => (
        <Link
          key={tag.slug}
          href={`/tags/${tag.slug}`}
          className={tagVariants({ size })}
        >
          {tag.name}
        </Link>
      ))}
      
      {hasMore && (
        <span className={tagVariants({ size })}>
          +{tags.length - maxDisplay} more
        </span>
      )}
    </div>
  );
};
```

**State Management**:
- **None Required**: TagList is a stateless UI component
- **Props**: All content and behavior controlled via props

**Implementation Requirements**:
1. Create a component for displaying multiple tags
2. Support limiting the number of displayed tags
3. Support different sizes for various contexts

**Design System References**:
- Colors: Light (#F1F5F9) background, Medium (#64748B) text
- Typography: Inter Regular (400), 14px
- Components: Tags in design system

**Next.js Specific Changes**:
- Use Next.js Link for navigation
- Can be implemented as a server component

**Testing Strategy**:
- **Unit Tests**: Verify rendering of tags and "more" indicator
- **Test Example**:
```typescript
// __tests__/components/ui/TagList.test.tsx
import { render, screen } from '@testing-library/react';
import { TagList } from '@/components/ui/TagList';

const mockTags = [
  { name: 'React', slug: 'react' },
  { name: 'TypeScript', slug: 'typescript' },
  { name: 'Next.js', slug: 'nextjs' },
  { name: 'Tailwind', slug: 'tailwind' },
];

describe('TagList Component', () => {
  it('renders all tags when maxDisplay is not provided', () => {
    render(<TagList tags={mockTags} />);
    
    expect(screen.getByText('React')).toBeInTheDocument();
    expect(screen.getByText('TypeScript')).toBeInTheDocument();
    expect(screen.getByText('Next.js')).toBeInTheDocument();
    expect(screen.getByText('Tailwind')).toBeInTheDocument();
  });
  
  it('limits displayed tags when maxDisplay is provided', () => {
    render(<TagList tags={mockTags} maxDisplay={2} />);
    
    expect(screen.getByText('React')).toBeInTheDocument();
    expect(screen.getByText('TypeScript')).toBeInTheDocument();
    expect(screen.queryByText('Next.js')).not.toBeInTheDocument();
    expect(screen.queryByText('Tailwind')).not.toBeInTheDocument();
  });
  
  it('shows "more" indicator when tags are limited', () => {
    render(<TagList tags={mockTags} maxDisplay={2} />);
    
    expect(screen.getByText('+2 more')).toBeInTheDocument();
  });
  
  it('applies the correct size class', () => {
    render(<TagList tags={mockTags} size="sm" />);
    
    const tag = screen.getByText('React');
    expect(tag).toHaveClass('text-xs');
  });
  
  it('links to the correct tag pages', () => {
    render(<TagList tags={mockTags} />);
    
    const reactTag = screen.getByText('React');
    expect(reactTag).toHaveAttribute('href', '/tags/react');
    
    const tsTag = screen.getByText('TypeScript');
    expect(tsTag).toHaveAttribute('href', '/tags/typescript');
  });
});
```

### 6.3. ThemeToggle Component (New Component)

**Current Implementation**: Not present in current codebase

**Migration Path**: **Build New**

**TypeScript Interface**:
```typescript
interface ThemeToggleProps {
  theme: 'light' | 'dark';
  toggleTheme: () => void;
  className?: string;
}
```

**Code Example**:
```tsx
// components/ui/ThemeToggle.tsx
'use client';

import { motion } from 'framer-motion';
import { cn } from '@/lib/utils';

export const ThemeToggle = ({ 
  theme, 
  toggleTheme, 
  className 
}: ThemeToggleProps) => {
  return (
    <button
      onClick={toggleTheme}
      className={cn(
        "relative inline-flex h-6 w-11 items-center rounded-full",
        theme === 'dark' ? 'bg-primary-600' : 'bg-gray-200',
        className
      )}
      aria-label={`Switch to ${theme === 'dark' ? 'light' : 'dark'} mode`}
    >
      <span className="sr-only">
        Switch to {theme === 'dark' ? 'light' : 'dark'} mode
      </span>
      <motion.span
        layout
        transition={{ type: "spring", stiffness: 700, damping: 30 }}
        className={cn(
          "inline-block h-4 w-4 transform rounded-full bg-white shadow-lg ring-0",
          theme === 'dark' ? 'translate-x-6' : 'translate-x-1'
        )}
      >
        {theme === 'dark' ? (
          <svg 
            className="h-4 w-4 text-primary-600" 
            fill="currentColor" 
            viewBox="0 0 20 20"
          >
            <path d="M17.293 13.293A8 8 0 016.707 2.707a8.001 8.001 0 1010.586 10.586z" />
          </svg>
        ) : (
          <svg 
            className="h-4 w-4 text-yellow-500" 
            fill="currentColor" 
            viewBox="0 0 20 20"
          >
            <path 
              fillRule="evenodd" 
              d="M10 2a1 1 0 011 1v1a1 1 0 11-2 0V3a1 1 0 011-1zm4 8a4 4 0 11-8 0 4 4 0 018 0zm-.464 4.95l.707.707a1 1 0 001.414-1.414l-.707-.707a1 1 0 00-1.414 1.414zm2.12-10.607a1 1 0 010 1.414l-.706.707a1 1 0 11-1.414-1.414l.707-.707a1 1 0 011.414 0zM17 11a1 1 0 100-2h-1a1 1 0 100 2h1zm-7 4a1 1 0 011 1v1a1 1 0 11-2 0v-1a1 1 0 011-1zM5.05 6.464A1 1 0 106.465 5.05l-.708-.707a1 1 0 00-1.414 1.414l.707.707zm1.414 8.486l-.707.707a1 1 0 01-1.414-1.414l.707-.707a1 1 0 011.414 1.414zM4 11a1 1 0 100-2H3a1 1 0 000 2h1z" 
              clipRule="evenodd" 
            />
          </svg>
        )}
      </motion.span>
    </button>
  );
};
```

**State Management**:
- **Zustand Store**: For theme state
- **Example Zustand Store**:
```typescript
// store/useThemeStore.ts
import { create } from 'zustand';
import { persist } from 'zustand/middleware';

type Theme = 'light' | 'dark';

interface ThemeState {
  theme: Theme;
  toggleTheme: () => void;
  setTheme: (theme: Theme) => void;
}

export const useThemeStore = create<ThemeState>()(
  persist(
    (set) => ({
      theme: 'light',
      toggleTheme: () => set((state) => ({ 
        theme: state.theme === 'light' ? 'dark' : 'light' 
      })),
      setTheme: (theme) => set({ theme }),
    }),
    {
      name: 'theme-storage',
    }
  )
);
```

**Implementation Requirements**:
1. Create a toggle switch for theme switching
2. Support smooth animation between states
3. Include appropriate icons for light/dark modes

**Design System References**:
- Colors: Primary Blue (#2563EB) for dark mode toggle background
- Components: Toggle Switch in design system

**Next.js Specific Changes**:
- Implement as a client component for interactive functionality
- Integrate with Next.js theme provider if using

**Testing Strategy**:
- **Unit Tests**: Test toggle functionality and visual state
- **Test Example**:
```typescript
// __tests__/components/ui/ThemeToggle.test.tsx
import { render, screen, fireEvent } from '@testing-library/react';
import { ThemeToggle } from '@/components/ui/ThemeToggle';

describe('ThemeToggle Component', () => {
  it('renders with light theme initially', () => {
    const toggleTheme = jest.fn();
    render(<ThemeToggle theme="light" toggleTheme={toggleTheme} />);
    
    const toggle = screen.getByLabelText('Switch to dark mode');
    expect(toggle).toHaveClass('bg-gray-200');
    expect(toggle).not.toHaveClass('bg-primary-600');
  });
  
  it('renders with dark theme when specified', () => {
    const toggleTheme = jest.fn();
    render(<ThemeToggle theme="dark" toggleTheme={toggleTheme} />);
    
    const toggle = screen.getByLabelText('Switch to light mode');
    expect(toggle).toHaveClass('bg-primary-600');
    expect(toggle).not.toHaveClass('bg-gray-200');
  });
  
  it('calls toggleTheme when clicked', () => {
    const toggleTheme = jest.fn();
    render(<ThemeToggle theme="light" toggleTheme={toggleTheme} />);
    
    const toggle = screen.getByLabelText('Switch to dark mode');
    fireEvent.click(toggle);
    
    expect(toggleTheme).toHaveBeenCalledTimes(1);
  });
});
```

## 7. Conclusion and Next Steps

This enhanced migration guide provides a comprehensive roadmap for transitioning from the current React/Vite codebase to a Next.js/TypeScript implementation. By following the detailed component-by-component instructions, developers can ensure a smooth migration process while implementing the design system and new features.

### Key Takeaways

1. **Component Structure**: The migration involves both adapting existing components and building new ones, with a clear organization structure.
2. **TypeScript Integration**: All components require TypeScript interfaces for type safety and better developer experience.
3. **State Management**: A combination of local state, React Context, Zustand, and React Query/SWR is recommended based on component needs.
4. **Testing Strategy**: Each component has specific testing recommendations to ensure functionality and visual consistency.
5. **Next.js Adaptation**: Components need to be adapted for Next.js patterns, including server vs. client components and Next.js-specific features.

### Next Steps

1. **Set up the Next.js project structure** with TypeScript and Tailwind CSS.
2. **Implement the core UI components** first, as they are used throughout the application.
3. **Build the layout components** (Header, Footer) to establish the application structure.
4. **Implement page-specific components** based on priority (Home, Article, etc.).
5. **Integrate with backend APIs** using React Query or SWR for data fetching.
6. **Set up global state management** with Zustand for authentication, theme, etc.
7. **Implement comprehensive testing** as outlined in the component-specific strategies.

By following this guide, developers can ensure a consistent implementation that adheres to the design system and meets the requirements outlined in the PRD.
