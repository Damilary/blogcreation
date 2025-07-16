# TechNexus Blog Design System

This document outlines the design system for the TechNexus Blog platform, including color palette, typography, UI components, and their usage guidelines.

## 1. Color Palette

### Primary Colors

- **Primary Blue**: #2563EB

  - Used for primary buttons, links, and key interactive elements
  - Hover state: #1E40AF
  - Active state: #1E3A8A

- **Secondary Teal**: #0D9488

  - Used for secondary UI elements, success states, and complementary accents
  - Hover state: #0F766E
  - Active state: #115E59

- **Accent Orange**: #F97316
  - Used for call-to-action elements, highlights, and attention-grabbing components
  - Hover state: #EA580C
  - Active state: #C2410C

### Neutral Colors

- **Dark**: #1E293B

  - Used for primary text, headings, and high-contrast UI elements
  - Light variant: #334155 (for secondary text)

- **Medium**: #64748B

  - Used for tertiary text, borders, and less prominent UI elements
  - Light variant: #94A3B8

- **Light**: #F1F5F9

  - Used for backgrounds, cards, and subtle UI elements
  - Dark variant: #E2E8F0
  - Extra light variant: #F8FAFC

- **White**: #FFFFFF
  - Used for backgrounds, text on dark elements, and high-contrast components

### Semantic Colors

- **Success**: #10B981

  - Used for success messages, completion indicators, and positive actions

- **Warning**: #F59E0B

  - Used for warnings, alerts, and cautionary messages

- **Error**: #EF4444

  - Used for error states, destructive actions, and critical alerts

- **Info**: #3B82F6
  - Used for informational messages and neutral notifications

### Color Usage Guidelines

1. **Accessibility**

   - All color combinations must meet WCAG 2.2 AA contrast ratio requirements (4.5:1 for normal text, 3:1 for large text)
   - Never rely solely on color to convey information (use additional indicators)

2. **Consistency**

   - Use primary blue for main interactive elements
   - Use semantic colors only for their intended meanings
   - Maintain color hierarchy consistently across the platform

3. **Dark Mode**
   - Light backgrounds switch to #0F172A
   - Dark text switches to #F1F5F9
   - Colors maintain similar relationships but with adjusted brightness and saturation

## 2. Typography

### Font Families

- **Primary Font**: Inter (Sans-serif)

  - Used for all UI elements, headings, and body text
  - Weights: 400 (regular), 500 (medium), 600 (semibold), 700 (bold)

- **Monospace Font**: JetBrains Mono
  - Used exclusively for code blocks and technical specifications
  - Weights: 400 (regular), 700 (bold)

### Type Scale

Based on a 1.250 (major third) typographic scale:

- **xs**: 12px (0.75rem)
- **sm**: 14px (0.875rem)
- **base**: 16px (1rem)
- **lg**: 18px (1.125rem)
- **xl**: 20px (1.25rem)
- **2xl**: 24px (1.5rem)
- **3xl**: 30px (1.875rem)
- **4xl**: 36px (2.25rem)
- **5xl**: 48px (3rem)
- **6xl**: 60px (3.75rem)

### Heading Styles

- **H1 (Page Title)**

  - Font: Inter Bold (700)
  - Size: 36px (2.25rem)
  - Line Height: 1.2
  - Margin: 0 0 24px 0

- **H2 (Section Title)**

  - Font: Inter Semibold (600)
  - Size: 30px (1.875rem)
  - Line Height: 1.3
  - Margin: 48px 0 16px 0

- **H3 (Subsection Title)**

  - Font: Inter Semibold (600)
  - Size: 24px (1.5rem)
  - Line Height: 1.4
  - Margin: 32px 0 16px 0

- **H4 (Component Title)**

  - Font: Inter Medium (500)
  - Size: 20px (1.25rem)
  - Line Height: 1.4
  - Margin: 24px 0 12px 0

- **H5 (Small Section Title)**

  - Font: Inter Medium (500)
  - Size: 18px (1.125rem)
  - Line Height: 1.5
  - Margin: 20px 0 12px 0

- **H6 (Subtitle)**
  - Font: Inter Medium (500)
  - Size: 16px (1rem)
  - Line Height: 1.5
  - Margin: 16px 0 8px 0

### Body Text

- **Body (Default)**

  - Font: Inter Regular (400)
  - Size: 16px (1rem)
  - Line Height: 1.6
  - Margin: 0 0 16px 0

- **Body Small**

  - Font: Inter Regular (400)
  - Size: 14px (0.875rem)
  - Line Height: 1.6
  - Margin: 0 0 12px 0

- **Caption**
  - Font: Inter Regular (400)
  - Size: 12px (0.75rem)
  - Line Height: 1.5
  - Margin: 0 0 8px 0

### Code Text

- **Code Block**

  - Font: JetBrains Mono Regular (400)
  - Size: 14px (0.875rem)
  - Line Height: 1.5
  - Padding: 16px
  - Background: #F8FAFC (Light mode) / #1E293B (Dark mode)
  - Border: 1px solid #E2E8F0 (Light mode) / #334155 (Dark mode)
  - Border-Radius: 6px

- **Inline Code**
  - Font: JetBrains Mono Regular (400)
  - Size: 14px (0.875rem)
  - Padding: 2px 4px
  - Background: #F1F5F9 (Light mode) / #334155 (Dark mode)
  - Border-Radius: 4px

### Typography Guidelines

1. **Hierarchy**

   - Maintain consistent heading hierarchy (H1 > H2 > H3, etc.)
   - Use size, weight, and color to establish visual hierarchy
   - Limit heading levels to 3 per page when possible

2. **Readability**

   - Keep paragraph width between 50-75 characters for optimal readability
   - Use proper line height (1.5-1.6 for body text) to enhance readability
   - Ensure sufficient contrast between text and background

3. **Responsive Behavior**
   - Implement fluid typography that scales appropriately for different screen sizes
   - Reduce heading sizes proportionally on smaller screens
   - Maintain minimum body text size of 16px on all devices

## 3. UI Components

### Buttons

#### Primary Button

- Background: Primary Blue (#2563EB)
- Text: White (#FFFFFF)
- Padding: 10px 16px
- Border: None
- Border-Radius: 6px
- Font: Inter Medium (500), 16px
- States:
  - Hover: Darker blue (#1E40AF)
  - Active: Even darker blue (#1E3A8A)
  - Disabled: Gray (#94A3B8) with reduced opacity

#### Secondary Button

- Background: White (#FFFFFF)
- Text: Primary Blue (#2563EB)
- Padding: 10px 16px
- Border: 1px solid Primary Blue (#2563EB)
- Border-Radius: 6px
- Font: Inter Medium (500), 16px
- States:
  - Hover: Light blue background (#EFF6FF)
  - Active: Slightly darker blue background (#DBEAFE)
  - Disabled: Gray text and border (#94A3B8) with reduced opacity

#### Tertiary Button

- Background: Transparent
- Text: Primary Blue (#2563EB)
- Padding: 10px 16px
- Border: None
- Border-Radius: 6px
- Font: Inter Medium (500), 16px
- States:
  - Hover: Light blue background (#EFF6FF)
  - Active: Slightly darker blue background (#DBEAFE)
  - Disabled: Gray text (#94A3B8) with reduced opacity

#### Icon Button

- Background: Transparent
- Icon: Primary Blue (#2563EB) or Dark (#1E293B) based on context
- Padding: 8px
- Border: None
- Border-Radius: 6px
- States:
  - Hover: Light background (#F1F5F9)
  - Active: Slightly darker background (#E2E8F0)
  - Disabled: Gray icon (#94A3B8) with reduced opacity

### Cards

#### Standard Card

- Background: White (#FFFFFF)
- Border: 1px solid Light (#E2E8F0)
- Border-Radius: 8px
- Padding: 24px
- Shadow: 0 1px 3px rgba(0, 0, 0, 0.1), 0 1px 2px rgba(0, 0, 0, 0.06)
- States:
  - Hover (if interactive): Shadow increases to 0 4px 6px rgba(0, 0, 0, 0.1), 0 2px 4px rgba(0, 0, 0, 0.06)

#### Article Card

- Background: White (#FFFFFF)
- Border: 1px solid Light (#E2E8F0)
- Border-Radius: 8px
- Padding: 0
- Shadow: 0 1px 3px rgba(0, 0, 0, 0.1), 0 1px 2px rgba(0, 0, 0, 0.06)
- Structure:
  - Image container: 16:9 ratio, border-radius 8px 8px 0 0
  - Content padding: 16px
  - Title: H4 style, margin-bottom 8px
  - Excerpt: Body Small, margin-bottom 12px
  - Metadata: Caption style (author, date, reading time)
  - Category/Tag: Small pill (see below)
- States:
  - Hover: Shadow increases, slight scale transform (1.02)

_Note: Consider additional card variants or component styles to support the visually distinct "Top Picks" section and varied homepage category showcases (e.g., carousel items)._

#### Featured Card

- Similar to Standard Card but with:
  - Border-left: 4px solid Primary Blue (#2563EB) or Accent Orange (#F97316)
  - Background: Subtle gradient or highlight
  - Slightly larger title text

### Form Elements

#### Text Input

- Background: White (#FFFFFF)
- Text: Dark (#1E293B)
- Placeholder: Medium (#64748B)
- Border: 1px solid Medium (#94A3B8)
- Border-Radius: 6px
- Padding: 10px 12px
- Font: Inter Regular (400), 16px
- States:
  - Focus: Border color changes to Primary Blue (#2563EB), subtle shadow
  - Error: Border color changes to Error (#EF4444)
  - Disabled: Background Light (#F1F5F9), reduced opacity

#### Checkbox

- Size: 18px × 18px
- Border: 1px solid Medium (#94A3B8)
- Border-Radius: 4px
- Background: White (#FFFFFF)
- Check Mark: White (#FFFFFF)
- States:
  - Checked: Background changes to Primary Blue (#2563EB)
  - Focus: Subtle shadow/ring
  - Disabled: Background Light (#F1F5F9), reduced opacity

#### Radio Button

- Size: 18px × 18px
- Border: 1px solid Medium (#94A3B8)
- Border-Radius: 50%
- Background: White (#FFFFFF)
- Selected Dot: Primary Blue (#2563EB)
- States:
  - Selected: Inner circle with Primary Blue (#2563EB)
  - Focus: Subtle shadow/ring
  - Disabled: Background Light (#F1F5F9), reduced opacity

#### Select/Dropdown

- Similar to Text Input
- Includes dropdown icon (chevron)
- Dropdown menu follows Card styling

### Navigation Elements

#### Main Navigation

- Background: White (#FFFFFF)
- Text: Dark (#1E293B)
- Active Item: Primary Blue (#2563EB) text, subtle underline or indicator
- Height: 64px
- Shadow: Subtle shadow to separate from content
- Mobile: Transforms into hamburger menu

#### Breadcrumb

- Text: Medium (#64748B)
- Separator: Light icon (chevron or slash)
- Current Page: Dark (#1E293B)
- Font: Inter Regular (400), 14px

#### Pagination

- Container: Flexible, centered
- Page Number: 36px × 36px, circular
- Current Page: Primary Blue (#2563EB) background, White (#FFFFFF) text
- Other Pages: White (#FFFFFF) background, Dark (#1E293B) text
- Previous/Next: With icons, similar to Secondary Button

#### Tab Navigation

- Container: Bottom border 1px solid Light (#E2E8F0)
- Tab: Padding 12px 16px, Medium (#64748B) text
- Active Tab: Dark (#1E293B) text, 2px solid Primary Blue (#2563EB) bottom border
- Font: Inter Medium (500), 16px

### Tags and Badges

#### Category Tag

- Background: Light (#F1F5F9)
- Text: Dark (#1E293B)
- Border: None
- Border-Radius: 16px
- Padding: 4px 10px
- Font: Inter Medium (500), 14px

#### Expertise Level Badge

- Beginner:
  - Background: #ECFDF5
  - Text: #047857
- Intermediate:
  - Background: #EFF6FF
  - Text: #2563EB
- Advanced:
  - Background: #FEF2F2
  - Text: #B91C1C
- Border-Radius: 4px
- Padding: 2px 8px
- Font: Inter Medium (500), 12px

#### Content Format Badge

- Different color schemes for different formats (Tutorial, Article, etc.)
- Border-Radius: 4px
- Padding: 2px 8px
- Font: Inter Medium (500), 12px

### Feedback Elements

#### Alert/Notification

- Success:
  - Background: #ECFDF5
  - Border-left: 4px solid #10B981
- Warning:
  - Background: #FFFBEB
  - Border-left: 4px solid #F59E0B
- Error:
  - Background: #FEF2F2
  - Border-left: 4px solid #EF4444
- Info:
  - Background: #EFF6FF
  - Border-left: 4px solid #3B82F6
- Border-Radius: 6px
- Padding: 12px 16px
- Icon: Corresponding to alert type
- Close Button: Optional X icon

#### Toast Notification

- Similar color schemes to Alert
- Added shadow
- Appears temporarily
- Position: Top-right of screen
- Transition: Slide-in and fade

#### Progress Bar

- Height: 8px
- Background: Light (#E2E8F0)
- Fill: Primary Blue (#2563EB) or context-specific color
- Border-Radius: 4px
- Label: Optional percentage or text above

### Interactive Elements

#### Code Playground

- Editor:
  - Background: Dark (#1E293B)
  - Text: Light (#F1F5F9)
  - Line Numbers: Medium (#64748B)
  - Syntax Highlighting: Language appropriate
- Controls:
  - Similar to Button components
  - Run button: Success color
  - Reset button: Secondary styling

#### Toggle Switch

- Height: 24px
- Width: 44px
- Border-Radius: 12px
- Off State:
  - Background: Medium (#94A3B8)
  - Knob: White (#FFFFFF)
- On State:
  - Background: Primary Blue (#2563EB)
  - Knob: White (#FFFFFF)
- Transition: Smooth slide and background change

#### Search Bar

- Background: White (#FFFFFF)
- Border: 1px solid Light (#E2E8F0)
- Border-Radius: 8px
- Padding: 10px 12px
- Icon: Search icon in Medium (#64748B)
- Expanded state: Grows in width, with clear button

## 4. Iconography

### Icon Set

TechNexus Blog uses a consistent icon set with the following characteristics:

- Style: Line icons with 1.5px stroke width
- Size: 24px × 24px (default)
- Color: Inherits from text color or specified color
- Available sizes: 16px, 20px, 24px, 32px

### Key Icons

- **Navigation**: Home, menu, search, arrow-left, arrow-right, external-link
- **Actions**: Plus, minus, edit, delete, download, upload, share
- **Status**: Check, x-mark, warning, information, question
- **Content**: Document, image, video, code, link, bookmark
- **Social Media Icons**: Twitter/X, LinkedIn, GitHub, Facebook, Instagram, Email
- **User**: User, users, settings, logout, notification

### Icon Usage Guidelines

1. **Consistency**

   - Use the same icon style throughout the platform
   - Keep icons aligned to the pixel grid for sharpness

2. **Accessibility**

   - Always provide text alternatives for icons
   - Use aria-label for standalone icon buttons

3. **Sizing**
   - Use appropriate size based on context (navigation vs inline)
   - Maintain consistent padding around icons

## 5. Spacing System

### Base Spacing Unit

- Base: 4px
- Scale: 0, 0.5, 1, 1.5, 2, 2.5, 3, 3.5, 4, 5, 6, 7, 8, 9, 10, 12, 14, 16, 20, 24, 28, 32, 36, 40, 44, 48, 52, 56, 60, 64, 72, 80, 96

Translated to rems:

- 0: 0
- 0.5: 0.125rem (2px)
- 1: 0.25rem (4px)
- 2: 0.5rem (8px)
- 3: 0.75rem (12px)
- 4: 1rem (16px)
- 5: 1.25rem (20px)
- 6: 1.5rem (24px)
- 8: 2rem (32px)
- 10: 2.5rem (40px)
- 12: 3rem (48px)
- 16: 4rem (64px)
- 20: 5rem (80px)
- 24: 6rem (96px)

### Layout Spacing

- **Page Padding**:

  - Desktop: 64px (sides), 48px (top/bottom)
  - Tablet: 32px (sides), 32px (top/bottom)
  - Mobile: 16px (sides), 24px (top/bottom)

- **Section Spacing**:

  - Desktop: 80px between major sections
  - Tablet: 64px between major sections
  - Mobile: 48px between major sections

- **Component Spacing**:

  - Related components: 16px-24px
  - Unrelated components: 32px-48px

- **Element Spacing**:
  - Label to input: 8px
  - Input to help text: 4px
  - Button to button: 16px
  - Icon to text: 8px

### Grid System

- **Columns**:

  - Desktop: 12 columns
  - Tablet: 8 columns
  - Mobile: 4 columns

- **Gutters**:

  - Desktop: 24px
  - Tablet: 16px
  - Mobile: 16px

- **Margins**:
  - Desktop: 64px
  - Tablet: 32px
  - Mobile: 16px

_Note: The grid system and spacing utilities should support the multi-column layout required for the footer._

## 6. Responsive Breakpoints

- **Mobile**: < 640px
- **Tablet**: 640px - 1023px
- **Desktop**: 1024px - 1279px
- **Large Desktop**: 1280px - 1535px
- **Extra Large Desktop**: ≥ 1536px

CSS Media Query breakpoints:

```css
/* Mobile first approach */
/* Base styles for mobile */

/* Small (sm) */
@media (min-width: 640px) {
  /* Tablet styles */
}

/* Medium (md) */
@media (min-width: 768px) {
  /* Larger tablet styles */
}

/* Large (lg) */
@media (min-width: 1024px) {
  /* Desktop styles */
}

/* Extra Large (xl) */
@media (min-width: 1280px) {
  /* Large desktop styles */
}

/* 2XL */
@media (min-width: 1536px) {
  /* Extra large desktop styles */
}
```

## 7. Animation & Transitions

### Transition Durations

- **Fast**: 150ms (micro-interactions, buttons)
- **Standard**: 300ms (most UI elements)
- **Slow**: 500ms (larger transitions, modals)

### Easing Functions

- **Standard**: cubic-bezier(0.4, 0, 0.2, 1) - Smooth, natural feeling
- **Ease-in**: cubic-bezier(0.4, 0, 1, 1) - Start slow, end fast (for exits)
- **Ease-out**: cubic-bezier(0, 0, 0.2, 1) - Start fast, end slow (for entrances)

### Common Animations

- **Fade**: Opacity transition from 0 to 1
- **Scale**: Transform scale from 0.95 to 1
- **Slide**: Transform translateY or translateX
- **Skeleton Loading**: Subtle pulse animation for loading states

### Animation Guidelines

1. **Purpose**

   - Use animation purposefully to enhance UX, not distract
   - Animate elements in a logical flow (follow user attention)

2. **Performance**

   - Prefer transforms and opacity for smooth animations
   - Be mindful of performance, especially on mobile devices

3. **Accessibility**
   - Respect user preferences (prefers-reduced-motion)
   - Keep animations subtle and non-disruptive

## 8. Implementation Guidelines

### CSS Framework

Tailwind CSS will be used as the primary CSS framework with a custom configuration that aligns with this design system.

```js
// tailwind.config.js example (simplified)
module.exports = {
  theme: {
    colors: {
      primary: {
        DEFAULT: "#2563EB",
        dark: "#1E40AF",
        darker: "#1E3A8A",
        // etc.
      },
      secondary: {
        DEFAULT: "#0D9488",
        // etc.
      },
      accent: {
        DEFAULT: "#F97316",
        // etc.
      },
      dark: {
        DEFAULT: "#1E293B",
        // etc.
      },
      // Other colors...
    },
    fontFamily: {
      sans: ["Inter", "sans-serif"],
      mono: ["JetBrains Mono", "monospace"],
    },
    // Other theme settings...
  },
  plugins: [
    // Required plugins
  ],
};
```

### Component Library

React components should be built as a library of reusable parts:

1. **Atomic Design Methodology**

   - Atoms: Basic UI elements (buttons, inputs, icons)
   - Molecules: Simple combinations of atoms (form fields, search bars)
   - Organisms: Complex UI sections (article cards, comment sections)
   - Templates: Page layouts without specific content
   - Pages: Complete screens with real content

2. **Component Documentation**
   - Each component should have clear documentation
   - Include usage examples, props API, and variants
   - Use Storybook for visual component library

### Accessibility Implementation

1. **Semantic HTML**

   - Use proper HTML5 elements (nav, article, section, etc.)
   - Implement proper heading hierarchy

2. **ARIA Attributes**

   - Add aria-labels where appropriate
   - Use aria-expanded, aria-controls for interactive elements
   - Implement aria-live regions for dynamic content

3. **Keyboard Navigation**

   - Ensure all interactive elements are keyboard accessible
   - Implement logical tab order
   - Provide visible focus indicators

4. **Screen Reader Support**
   - Test with popular screen readers (NVDA, VoiceOver)
   - Provide text alternatives for non-text content
   - Use appropriate ARIA roles and properties

### Performance Considerations

1. **Optimized Assets**

   - Compress images and use proper formats (WebP with fallbacks)
   - Implement responsive images with srcset
   - Use font-display: swap for text rendering

2. **Code Splitting**

   - Split code by routes and components
   - Implement lazy loading for non-critical components
   - Use dynamic imports for large libraries

3. **Perceived Performance**
   - Implement skeleton screens for loading states
   - Prioritize above-the-fold content
   - Use progressive enhancement techniques
