# TechNexus Blog UI Mockups

This document provides visual representations of key pages and components for the TechNexus Blog platform, designed to align with the project's requirements and design system.

## 1. Homepage

The homepage offers a dynamic and engaging layout, inspired by modern tech blogs and magazines:

- **Header:**

  - **Description**: Clean, modern header with clear navigation.

  - **Elements**:

    - Site Logo (TechNexus) - clickable, links to homepage. **Positioned top-left.**
    - Main Navigation Links: AI/ML, Cybersecurity, Web Development, etc. (as defined in PRD).
      - **Desktop**: These links will feature dropdown menus on hover/click, revealing sub-categories and potentially small article previews (image + title) for quick insight into the category's content.
      - Each main category link will expand (e.g., accordion style) to show sub-categories if applicable, presented as a nested list.
    - Prominent Search Bar/Icon (may be integrated into the navigation bar or as a standalone element).
    - User Authentication Area:
      - "Login/Sign Up" buttons if the user is not authenticated.
      - **User account icon** if authenticated (triggers dropdown with links to Profile, Settings, Saved Articles, Logout).
    - Dark Mode Toggle Switch.
    - **Newsletter Subscription Button**: Clearly visible, perhaps an icon with text (e.g., "Subscribe"). On click, opens a modal or a dedicated section/flyout for newsletter sign-up.

  - **Visuals**:

    - Refer to `header_desktop.png` (conceptual).
    - Refer to `header_mobile.png` (conceptual, showing hamburger menu).

- **Body:**

  - Hero Section: Large featured article or a slider/carousel of top stories with high-quality imagery and clear headlines.
  - Featured Articles Grid/List: Showcase several important articles below the hero section.
  - Category Showcases: Dedicated sections for key content categories (e.g., "Latest in Technology," "Cybersecurity Highlights") using various layouts like grids, lists with thumbnails, or carousels.
  - Popular Posts/Trending Section: Display articles based on popularity (e.g., views, comments).
  - "Top Picks" Section: A visually distinct section positioned above the footer, showcasing curated content (e.g., using a carousel or unique grid layout).
  - Newsletter signup with category selection: A dedicated section on the homepage (as originally planned) and also accessible via the header button/menu link. The modal/view triggered from the header/menu should offer similar functionality (email input, optional category preferences for the newsletter).

  - **Visuals**:

    - Refer to `homepage_layout.png` (conceptual).

- **Footer:**
  - Multi-column layout with content blocks (e.g., About Us, Quick Links, optional Recent/Popular Posts, optional Tags/Categories Cloud).
  - Site links (About Us, Contact Us, Privacy Policy, Terms of Service).
  - Social media icons linking to TechNexus Blog's profiles on: GitHub, Instagram, Facebook, X, LinkedIn, & Email.
  - Copyright information.
  - Optional "Back to Top" button.

## 2. Category Page

The category page provides a focused view of content within a specific topic:

- **Header:** (Same as Homepage)

  - Clean, modern design with clear navigation.
  - Site Logo, Navigation Links, Search, User Authentication, Dark Mode Toggle, Newsletter Subscribe.

- **Body:**

  - Distinctive category header with icon and description.
  - Filter bar for refining content by expertise level, format, and date.
  - Featured category content in a prominent position.
  - Subcategory navigation tabs for drilling down into specific topics.
  - Article grid with clear visual indicators for content format and expertise level.
  - Related categories section to encourage exploration.
  - Pagination controls for navigating through all category content.

- **Footer:** (Same as Homepage)
  - Multi-column layout, site links, social media, copyright, optional back-to-top button.

## 3. Article Page

The article page prioritizes readability and engagement:

- **Header:** (Same as Homepage)

  - Consistent navigation and branding elements.

- **Body:**

  - Breadcrumb navigation for context.
  - Clean article header with title, author info, and metadata.
  - Table of contents sidebar for navigating longer articles.
  - Expertise Level Indicator: Clearly displayed.
  - Social Sharing Buttons: Easily accessible.
  - Beautifully formatted article content with proper typography.
  - Syntax-highlighted code blocks with copy functionality.
  - Interactive elements clearly delineated from static content.
  - Author bio section to establish credibility.
  - Related learning path progress indicator for logged-in users.
  - Related articles recommendations.
  - Threaded comment section for community discussions.

- **Footer:** (Same as Homepage)
  - Multi-column layout, site links, social media, copyright, optional back-to-top button.

## 4. User Profile

The user profile allows users to manage their information and preferences:

- **Header:** (Same as Homepage, but likely with the user's avatar in the authentication area when logged in)

  - Consistent navigation and branding.

- **Body:**

  - Profile header with user avatar and join date.
  - Navigation tabs for different profile sections:
    - Saved articles grid with quick-access options.
    - Reading history timeline organized by date.
    - Learning path progress with visual indicators.
    - Comprehensive preference settings panel:
      - Dark mode toggle.
      - Notification settings.
      - Favorite categories.
      - Preferred content formats.
      - Preferred expertise levels.

- **Footer:** (Same as Homepage)
  - Standard footer layout.

## 5. Search Results

The search page facilitates content discovery:

- **Header:** (Similar to Homepage, with focus on the search bar)

  - Site Logo, prominent Search Bar/Icon, Navigation Links, User Authentication, etc.

- **Body:**

  - Enhanced search bar with suggestions as users type.
  - Result type tabs for filtering by content type.
  - Left sidebar with advanced filtering options.
  - Featured/most relevant result highlighted at the top.
  - Clean result listings with content previews and metadata.
  - Related searches suggestions.
  - Pagination for navigating through all results.

- **Footer:** (Same as Homepage)
  - Standard footer layout.

## 6. UI Components

The platform utilizes a consistent set of UI components:

- Button styles (primary, secondary, tertiary, icon)
- Form elements (text inputs, dropdowns, checkboxes, radio buttons)
- Card variations for different content types
- Navigation elements (tabs, breadcrumbs, pagination)
- Tags and badges for categories, expertise levels, and formats
- Feedback elements (alerts, toasts, progress indicators)
- Interactive elements (code playground, toggle switches)

## 7. Mobile Responsive Design

The platform is fully responsive and optimized for smaller screens:

- Collapsible hamburger menu for navigation.
- Single-column layout for content.
- Touch-optimized interactive elements.
- Bottom navigation bar for key actions.
- Optimized reading experience with proper font sizing and spacing.
- Full-featured search with filters accessible via expandable panel.

## 8. Dark Mode

The platform supports a dark mode for user preference:

- Color palette adaptation for dark backgrounds.
- Maintained accessibility and readability.
- Reduced eye strain for nighttime reading.
- Consistent UI component styling across modes.
- Mode toggle accessible from user menu.

- Consideration for including small visual cues or other minor enhancements can be discussed during the detailed design phase.
