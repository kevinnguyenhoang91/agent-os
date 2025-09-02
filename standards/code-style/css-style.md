# CSS Style Guide

Based on Google CSS Style Guide with Tailwind CSS project adaptations.

## Project Framework

We always use the latest version of **TailwindCSS** for all CSS styling. This guide covers both Tailwind usage patterns and custom CSS when needed.

## General CSS Guidelines

### Validity and Browser Support
- Use valid CSS code where possible
- Test across supported browsers
- Use vendor prefixes when necessary for compatibility
- Prefer modern CSS features with appropriate fallbacks

### Code Quality
- Use meaningful names for classes and IDs
- Avoid type selectors with class and ID selectors
- Use shorthand properties where appropriate
- Group related properties together

```css
/* Good - valid and organized CSS */
.navigation {
  display: flex;
  align-items: center;
  margin: 0;
  padding: 1rem 2rem;
  background-color: #ffffff;
  border-bottom: 1px solid #e5e7eb;
}

/* Bad - invalid and disorganized */
.navigation {
  display: flex;
  margin: 0px;
  background-color: white;
  align-items: center;
  padding-top: 1rem;
  padding-bottom: 1rem;
  padding-left: 2rem;
  padding-right: 2rem;
  border-bottom-width: 1px;
  border-bottom-style: solid;
  border-bottom-color: #e5e7eb;
}
```

## CSS Formatting Rules

### Indentation and Spacing
- Use **2 spaces** for indentation (never tabs)
- Include a space after each comma in separated values
- Include a space after the colon in declarations
- Use lowercase for all CSS

```css
/* Good - proper spacing and formatting */
.button-primary {
  display: inline-flex;
  align-items: center;
  padding: 0.75rem 1.5rem;
  background-color: #3b82f6;
  color: #ffffff;
  border: none;
  border-radius: 0.375rem;
  font-weight: 500;
  transition: background-color 0.2s ease-in-out;
}

.button-primary:hover {
  background-color: #2563eb;
}

/* Bad - inconsistent spacing */
.button-primary{
  display:inline-flex;
  padding:0.75rem 1.5rem;
  background-color:#3b82f6;
  Color:#ffffff;
}
```

### Property Declaration Order
- Group related properties together
- Order: positioning, box model, typography, visual, misc
- Use consistent ordering within teams

```css
/* Good - logical property grouping */
.card {
  /* Positioning */
  position: relative;
  z-index: 1;
  
  /* Box model */
  display: flex;
  flex-direction: column;
  width: 100%;
  max-width: 24rem;
  margin: 0 auto;
  padding: 1.5rem;
  
  /* Typography */
  font-family: 'Inter', sans-serif;
  font-size: 1rem;
  line-height: 1.5;
  
  /* Visual */
  background-color: #ffffff;
  border: 1px solid #e5e7eb;
  border-radius: 0.5rem;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
  
  /* Misc */
  transition: box-shadow 0.2s ease-in-out;
}
```

### Selectors and Naming
- Use lowercase and separate words with hyphens
- Avoid unnecessary ancestor selectors
- Use class selectors over ID selectors for styling
- Prefer semantic names over presentational names

```css
/* Good - semantic and specific selectors */
.user-profile-card {
  /* styles */
}

.navigation-link {
  /* styles */
}

.error-message {
  /* styles */
}

.button-primary,
.button-secondary {
  /* shared button styles */
}

/* Bad - non-semantic and overly specific */
#sidebar div.widget ul li a {
  /* styles */
}

.redButton {
  /* styles */
}

.navigation a.link {
  /* unnecessary specificity */
}
```

## Tailwind CSS Guidelines

### Multi-line CSS Classes in Markup

We use a unique multi-line formatting style when writing Tailwind CSS classes in HTML markup and ERB tags:

- **Responsive Organization**: Classes for each responsive size are written on their own dedicated line
- **Size Progression**: The top-most line should be the smallest size (no responsive prefix), with each line below being the next responsive size up
- **Vertical Alignment**: Each line of CSS classes should be aligned vertically
- **State Classes**: Focus and hover classes should be on their own additional dedicated lines
- **Custom Breakpoint**: We implement one additional responsive breakpoint called 'xs' which represents 400px
- **Custom Classes**: If there are any custom CSS classes being used, those should be included at the start of the first line

```html
<!-- Good - multi-line Tailwind CSS classes -->
<div class="custom-cta bg-gray-50 dark:bg-gray-900 p-4 rounded cursor-pointer w-full
            hover:bg-gray-100 dark:hover:bg-gray-800
            focus:outline-none focus:ring-2 focus:ring-blue-500
            xs:p-6
            sm:p-8 sm:font-medium
            md:p-10 md:text-lg
            lg:p-12 lg:text-xl lg:font-semibold lg:w-3/5
            xl:p-14 xl:text-2xl
            2xl:p-16 2xl:text-3xl 2xl:font-bold 2xl:w-3/4">
  I'm a call-to-action!
</div>

<!-- Good - simpler component with fewer breakpoints -->
<button class="btn-base bg-blue-600 text-white px-4 py-2 rounded
               hover:bg-blue-700
               focus:outline-none focus:ring-2 focus:ring-blue-500
               sm:px-6 sm:py-3
               lg:px-8 lg:py-4 lg:text-lg">
  Submit
</button>

<!-- Good - navigation example -->
<nav class="navigation-primary flex flex-col p-4 bg-white border-b
            hover:shadow-md
            sm:flex-row sm:items-center sm:justify-between
            lg:px-8">
  <div class="nav-brand">Logo</div>
  <ul class="nav-links flex flex-col space-y-2
             sm:flex-row sm:space-y-0 sm:space-x-6">
    <li><a href="/" class="nav-link">Home</a></li>
    <li><a href="/about" class="nav-link">About</a></li>
  </ul>
</nav>
```

### Tailwind Class Organization
- **Base Classes First**: Start with custom classes and base utility classes
- **Layout**: Display, positioning, spacing classes
- **Appearance**: Colors, borders, shadows
- **Typography**: Font, text styling
- **Interactive States**: Hover, focus, active states on separate lines
- **Responsive**: Organize by breakpoint size (xs, sm, md, lg, xl, 2xl)

```html
<!-- Good - organized class structure -->
<article class="blog-post relative flex flex-col p-6 bg-white rounded-lg shadow-sm
                hover:shadow-md
                focus-within:ring-2 focus-within:ring-blue-500
                sm:p-8
                md:flex-row md:items-start md:space-x-6
                lg:p-10">
  <img class="article-image w-full h-48 object-cover rounded
              sm:w-32 sm:h-32 sm:flex-shrink-0
              md:w-40 md:h-40" 
       src="image.jpg" 
       alt="Article thumbnail">
  
  <div class="article-content flex-1 mt-4
              sm:mt-0">
    <h2 class="article-title text-xl font-semibold text-gray-900
               sm:text-2xl
               lg:text-3xl">
      Article Title
    </h2>
    <p class="article-excerpt mt-2 text-gray-600 line-clamp-3
              sm:text-lg">
      Article excerpt content...
    </p>
  </div>
</article>
```

## Custom CSS (When Needed)

### When to Write Custom CSS
- Complex animations not covered by Tailwind
- Unique design patterns specific to the application
- Performance-critical styles
- Browser-specific workarounds

### Custom CSS Structure
- Use CSS custom properties for theming
- Organize CSS in logical modules
- Follow BEM methodology for component-specific styles
- Integrate with Tailwind's design system

```css
/* Good - custom CSS with CSS custom properties */
:root {
  --color-primary: #3b82f6;
  --color-primary-hover: #2563eb;
  --color-primary-focus: #1d4ed8;
  --shadow-elevation-low: 0 1px 3px rgba(0, 0, 0, 0.1);
  --shadow-elevation-medium: 0 4px 6px rgba(0, 0, 0, 0.1);
  --animation-duration-fast: 0.15s;
  --animation-duration-normal: 0.3s;
}

/* Custom component styles */
.backstage-sidebar {
  --sidebar-width: 16rem;
  --sidebar-width-collapsed: 4rem;
  
  position: fixed;
  top: 0;
  left: 0;
  height: 100vh;
  width: var(--sidebar-width);
  background-color: theme('colors.gray.50');
  border-right: 1px solid theme('colors.gray.200');
  transition: width var(--animation-duration-normal) ease-in-out;
}

.backstage-sidebar--collapsed {
  width: var(--sidebar-width-collapsed);
}

.backstage-sidebar__item {
  display: flex;
  align-items: center;
  padding: theme('spacing.3') theme('spacing.4');
  color: theme('colors.gray.700');
  text-decoration: none;
  transition: background-color var(--animation-duration-fast) ease-in-out;
}

.backstage-sidebar__item:hover {
  background-color: theme('colors.gray.100');
}

.backstage-sidebar__item--active {
  background-color: theme('colors.blue.50');
  color: theme('colors.blue.700');
  border-right: 3px solid theme('colors.blue.500');
}
```

### CSS-in-JS Integration
- When using CSS-in-JS, maintain consistency with Tailwind patterns
- Use Tailwind's theme function to access design tokens
- Keep styled components simple and composable

```javascript
// Good - CSS-in-JS with Tailwind integration
import styled from 'styled-components';
import { theme } from 'tailwindcss/defaultTheme';

const StyledButton = styled.button.attrs({
  className: 'inline-flex items-center justify-center rounded-md transition-colors'
})`
  padding: ${theme.spacing[2]} ${theme.spacing[4]};
  font-weight: ${theme.fontWeight.medium};
  
  &:focus {
    outline: none;
    box-shadow: 0 0 0 2px ${theme.colors.blue[500]};
  }
  
  ${props => props.variant === 'primary' && `
    background-color: ${theme.colors.blue[600]};
    color: ${theme.colors.white};
    
    &:hover {
      background-color: ${theme.colors.blue[700]};
    }
  `}
  
  ${props => props.variant === 'secondary' && `
    background-color: ${theme.colors.gray[200]};
    color: ${theme.colors.gray[900]};
    
    &:hover {
      background-color: ${theme.colors.gray[300]};
    }
  `}
`;
```

## Responsive Design

### Breakpoint Strategy
- Use Tailwind's default breakpoints with custom 'xs' addition
- Design mobile-first (base styles, then add larger breakpoints)
- Test at all breakpoint boundaries

```css
/* Tailwind breakpoints (with custom xs) */
/* xs: 400px */
/* sm: 640px */
/* md: 768px */
/* lg: 1024px */
/* xl: 1280px */
/* 2xl: 1536px */
```

### Container and Layout Patterns
- Use consistent container patterns across the application
- Implement flexible grid systems
- Consider content hierarchy at different screen sizes

```html
<!-- Good - responsive layout patterns -->
<div class="container-responsive mx-auto px-4 py-8
            sm:px-6
            lg:px-8 lg:py-12">
  
  <div class="grid grid-cols-1 gap-6
              sm:grid-cols-2
              lg:grid-cols-3
              xl:grid-cols-4">
    
    <div class="card-item p-4 bg-white rounded-lg shadow
                hover:shadow-md
                sm:p-6">
      <!-- Card content -->
    </div>
    
  </div>
</div>

<!-- Good - responsive navigation -->
<header class="header-main sticky top-0 z-50 bg-white shadow-sm
               lg:shadow-none">
  <div class="container mx-auto px-4
              sm:px-6
              lg:px-8">
    <nav class="nav-primary flex items-center justify-between py-4
                lg:py-6">
      <!-- Navigation content -->
    </nav>
  </div>
</header>
```

## Performance Optimization

### CSS Performance
- Minimize unused CSS through Tailwind's purge feature
- Use efficient selectors (avoid deep nesting)
- Optimize critical CSS for above-the-fold content
- Leverage CSS containment when appropriate

```javascript
// Good - Tailwind purge configuration
module.exports = {
  content: [
    './src/**/*.{js,jsx,ts,tsx}',
    './public/index.html'
  ],
  theme: {
    extend: {
      screens: {
        'xs': '400px',
      },
    },
  },
  variants: {
    extend: {
      opacity: ['disabled'],
      cursor: ['disabled'],
    },
  },
  plugins: [],
  corePlugins: {
    preflight: true,
  },
}
```

### Animation Performance
- Use CSS transforms and opacity for animations
- Prefer CSS animations over JavaScript when possible
- Use `will-change` property sparingly and remove after animation

```css
/* Good - performant animations */
.slide-in-animation {
  transform: translateX(-100%);
  opacity: 0;
  transition: transform 0.3s ease-out, opacity 0.3s ease-out;
}

.slide-in-animation--active {
  transform: translateX(0);
  opacity: 1;
}

.loading-spinner {
  will-change: transform;
  animation: spin 1s linear infinite;
}

@keyframes spin {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(360deg);
  }
}

/* Remove will-change when animation completes */
.loading-spinner--finished {
  will-change: auto;
}
```

## Dark Mode and Theming

### Dark Mode Implementation
- Use Tailwind's built-in dark mode support
- Provide fallbacks for unsupported browsers
- Test contrast ratios for accessibility

```html
<!-- Good - dark mode classes -->
<div class="card bg-white dark:bg-gray-800 text-gray-900 dark:text-white
            border border-gray-200 dark:border-gray-700">
  
  <h2 class="title text-2xl font-bold text-gray-900 dark:text-white">
    Card Title
  </h2>
  
  <p class="description text-gray-600 dark:text-gray-300">
    Card description with proper contrast in both modes.
  </p>
  
  <button class="btn bg-blue-600 hover:bg-blue-700 dark:bg-blue-500 dark:hover:bg-blue-600
                 text-white">
    Action Button
  </button>
</div>
```

### CSS Custom Properties for Theming
- Use CSS custom properties for dynamic theming
- Provide semantic color names
- Support system preference detection

```css
/* Good - semantic theming with custom properties */
:root {
  --color-background: #ffffff;
  --color-foreground: #1f2937;
  --color-muted: #6b7280;
  --color-border: #e5e7eb;
  --color-primary: #3b82f6;
  --color-primary-foreground: #ffffff;
}

[data-theme="dark"] {
  --color-background: #1f2937;
  --color-foreground: #f9fafb;
  --color-muted: #9ca3af;
  --color-border: #374151;
  --color-primary: #60a5fa;
  --color-primary-foreground: #1f2937;
}

.themed-component {
  background-color: var(--color-background);
  color: var(--color-foreground);
  border: 1px solid var(--color-border);
}
```

## Accessibility in CSS

### Focus States
- Provide clear focus indicators
- Ensure focus states meet contrast requirements
- Use appropriate focus styles for different input methods

```css
/* Good - accessible focus states */
.interactive-element {
  transition: all 0.15s ease-in-out;
}

.interactive-element:focus {
  outline: none;
  box-shadow: 0 0 0 2px var(--color-primary);
}

.interactive-element:focus-visible {
  box-shadow: 0 0 0 2px var(--color-primary),
              0 0 0 4px rgba(59, 130, 246, 0.3);
}

/* Different focus styles for mouse vs keyboard */
.js-focus-visible .interactive-element:focus:not(.focus-visible) {
  box-shadow: none;
}
```

### Motion and Animations
- Respect user preferences for reduced motion
- Provide alternatives for motion-sensitive users

```css
/* Good - respecting motion preferences */
.animated-element {
  transition: transform 0.3s ease-out;
}

@media (prefers-reduced-motion: reduce) {
  .animated-element {
    transition: none;
  }
  
  .auto-playing-animation {
    animation-play-state: paused;
  }
}
```

This comprehensive CSS style guide follows Google's CSS standards while maintaining the project's specific Tailwind CSS formatting conventions and responsive design patterns.
