# HTML Style Guide

Based on Google HTML/CSS Style Guide with project-specific adaptations.

## General Guidelines

### Document Type
- Use HTML5 doctype for all HTML documents
- Always include the `lang` attribute on the `html` element
- Use UTF-8 encoding without byte order mark (BOM)

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>Page Title</title>
</head>
<body>
  <!-- Content -->
</body>
</html>
```

### Validity and Semantics
- Use valid HTML code where possible
- Use semantic HTML elements for their intended purpose
- Provide alternative contents for multimedia when needed

```html
<!-- Good - semantic HTML -->
<article>
  <header>
    <h1>Article Title</h1>
    <time datetime="2024-01-15">January 15, 2024</time>
  </header>
  <main>
    <p>Article content...</p>
  </main>
  <footer>
    <p>Author: John Doe</p>
  </footer>
</article>

<!-- Good - accessibility -->
<img src="chart.png" alt="Sales increased 20% in Q4">
<video controls>
  <source src="video.mp4" type="video/mp4">
  <track kind="subtitles" src="subtitles.vtt" srclang="en" label="English">
  Your browser does not support the video tag.
</video>
```

## Formatting Rules

### Structure and Indentation
- Use **2 spaces** for indentation (never tabs)
- Place nested elements on new lines with proper indentation
- Use lowercase for all HTML element names and attributes
- Remove trailing whitespace

```html
<!-- Good - proper indentation -->
<section class="content">
  <header>
    <h1>Section Title</h1>
  </header>
  <div class="content-body">
    <p>Paragraph content with proper indentation.</p>
    <ul>
      <li>First item</li>
      <li>Second item</li>
    </ul>
  </div>
</section>

<!-- Bad - inconsistent indentation -->
<section class="content">
<header>
    <h1>Section Title</h1>
  </header>
    <div class="content-body">
<p>Paragraph content.</p>
    </div>
</section>
```

### Line Breaks and Wrapping
- Break long lines for readability (recommended 80 characters)
- Content between tags should be on its own line when multi-line
- Use consistent line breaks for similar elements

```html
<!-- Good - readable line breaks -->
<div class="navigation-container">
  <nav class="primary-navigation"
       role="navigation"
       aria-label="Primary">
    <ul class="nav-list">
      <li><a href="/" class="nav-link">Home</a></li>
      <li><a href="/products" class="nav-link">Products</a></li>
      <li><a href="/contact" class="nav-link">Contact</a></li>
    </ul>
  </nav>
</div>

<!-- Good - multi-line content -->
<p class="description">
  This is a longer paragraph that spans multiple lines and contains
  important information about the product or service being described.
</p>
```

## Attribute Guidelines

### Attribute Formatting (Project-Specific)
- Place each HTML attribute on its own line for complex elements
- Align attributes vertically for readability
- Keep the closing `>` on the same line as the last attribute
- For simple elements, attributes may stay on the same line

```html
<!-- Good - multi-line attributes for complex elements -->
<input type="email"
       id="user-email"
       name="email"
       class="form-input validation-required"
       placeholder="Enter your email"
       required
       aria-describedby="email-help">

<!-- Good - single line for simple elements -->
<img src="logo.png" alt="Company Logo" class="logo">

<!-- Good - mixed approach based on complexity -->
<div class="flex flex-col space-y-2
            md:flex-row md:space-y-0 md:space-x-4">
  <button type="submit" class="btn-primary">Submit</button>
</div>
```

### Attribute Order
- Use consistent attribute ordering for maintainability
- Recommended order: `id`, `class`, `type`, `name`, `src`/`href`, `title`, `alt`, data attributes, aria attributes

```html
<!-- Good - consistent attribute order -->
<input id="username"
       class="form-input required"
       type="text"
       name="username"
       placeholder="Username"
       data-validation="required"
       aria-required="true">

<a id="main-cta"
   class="btn btn-primary"
   href="/signup"
   title="Sign up for an account"
   data-analytics="cta-click"
   aria-label="Sign up for a new account">
  Sign Up
</a>
```

### Quotes and Values
- Always use double quotes for attribute values
- Quote all attribute values, even when not strictly required
- Use meaningful attribute values

```html
<!-- Good - double quotes and meaningful values -->
<div class="user-profile" id="profile-123">
  <img src="/avatars/user-123.jpg" alt="User avatar">
  <span class="username">john_doe</span>
</div>

<!-- Bad - inconsistent quotes -->
<div class='user-profile' id=profile-123>
  <img src=/avatars/user-123.jpg alt="User avatar">
</div>
```

## Semantic HTML

### Document Structure
- Use proper heading hierarchy (h1 → h2 → h3)
- Use structural elements appropriately
- Include landmark roles for accessibility

```html
<!-- Good - semantic document structure -->
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Backstage - Developer Portal</title>
</head>
<body>
  <header role="banner">
    <nav role="navigation" aria-label="Main navigation">
      <!-- Navigation content -->
    </nav>
  </header>
  
  <main role="main">
    <section>
      <h1>Welcome to Backstage</h1>
      <article>
        <h2>Getting Started</h2>
        <p>Introduction content...</p>
      </article>
    </section>
  </main>
  
  <aside role="complementary">
    <h2>Related Links</h2>
    <!-- Sidebar content -->
  </aside>
  
  <footer role="contentinfo">
    <p>&copy; 2024 Backstage. All rights reserved.</p>
  </footer>
</body>
</html>
```

### Form Elements
- Use proper form structure with labels and fieldsets
- Include appropriate input types and validation attributes
- Group related form elements logically

```html
<!-- Good - semantic form structure -->
<form class="user-registration" novalidate>
  <fieldset>
    <legend>Personal Information</legend>
    
    <div class="form-group">
      <label for="first-name" class="form-label">
        First Name <span class="required">*</span>
      </label>
      <input type="text"
             id="first-name"
             name="firstName"
             class="form-input"
             required
             aria-describedby="first-name-error">
      <div id="first-name-error" class="error-message" role="alert"></div>
    </div>
    
    <div class="form-group">
      <label for="email" class="form-label">
        Email Address <span class="required">*</span>
      </label>
      <input type="email"
             id="email"
             name="email"
             class="form-input"
             required
             aria-describedby="email-help email-error">
      <div id="email-help" class="form-help">
        We'll never share your email with anyone else.
      </div>
      <div id="email-error" class="error-message" role="alert"></div>
    </div>
  </fieldset>
  
  <div class="form-actions">
    <button type="submit" class="btn btn-primary">
      Create Account
    </button>
    <button type="button" class="btn btn-secondary">
      Cancel
    </button>
  </div>
</form>
```

## Accessibility

### ARIA and Screen Readers
- Use ARIA attributes to enhance accessibility
- Provide meaningful labels and descriptions
- Ensure proper focus management

```html
<!-- Good - accessible navigation -->
<nav role="navigation" aria-label="Breadcrumb">
  <ol class="breadcrumb">
    <li><a href="/" aria-current="false">Home</a></li>
    <li><a href="/services" aria-current="false">Services</a></li>
    <li><span aria-current="page">Web Development</span></li>
  </ol>
</nav>

<!-- Good - accessible modal -->
<div class="modal"
     role="dialog"
     aria-labelledby="modal-title"
     aria-describedby="modal-description"
     aria-modal="true">
  <div class="modal-content">
    <header class="modal-header">
      <h2 id="modal-title">Confirm Action</h2>
      <button type="button"
              class="modal-close"
              aria-label="Close dialog">
        <span aria-hidden="true">&times;</span>
      </button>
    </header>
    <div id="modal-description">
      Are you sure you want to delete this item?
    </div>
    <footer class="modal-actions">
      <button type="button" class="btn btn-danger">Delete</button>
      <button type="button" class="btn btn-secondary">Cancel</button>
    </footer>
  </div>
</div>
```

### Images and Media
- Always provide meaningful alt text for images
- Use empty alt attributes for decorative images
- Provide captions and transcripts for media

```html
<!-- Good - meaningful alt text -->
<img src="sales-chart-q4.png"
     alt="Sales chart showing 25% increase in Q4 2024"
     class="chart-image">

<!-- Good - decorative image -->
<img src="decorative-border.png" alt="" role="presentation">

<!-- Good - complex image with description -->
<figure>
  <img src="complex-diagram.png"
       alt="System architecture diagram"
       aria-describedby="diagram-description">
  <figcaption id="diagram-description">
    The diagram shows data flow from user requests through the API gateway,
    to microservices, and finally to the database layer.
  </figcaption>
</figure>
```

## Performance Optimization

### Resource Loading
- Use appropriate loading strategies for images and scripts
- Minimize HTTP requests through bundling
- Optimize critical rendering path

```html
<!-- Good - optimized resource loading -->
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  
  <!-- Preload critical resources -->
  <link rel="preload" href="/fonts/inter.woff2" as="font" type="font/woff2" crossorigin>
  <link rel="preload" href="/css/critical.css" as="style">
  
  <!-- Critical CSS inline -->
  <style>
    /* Critical above-the-fold styles */
  </style>
  
  <!-- Non-critical CSS -->
  <link rel="stylesheet" href="/css/main.css" media="print" onload="this.media='all'">
</head>

<body>
  <!-- Lazy load images -->
  <img src="placeholder.jpg"
       data-src="high-res-image.jpg"
       alt="High resolution image"
       loading="lazy"
       class="lazy-image">
  
  <!-- Defer non-critical JavaScript -->
  <script src="/js/main.js" defer></script>
</body>
```

### Image Optimization
- Use responsive images with srcset
- Provide multiple formats for better compression
- Use appropriate image formats (WebP, AVIF when supported)

```html
<!-- Good - responsive images -->
<picture>
  <source media="(min-width: 800px)"
          srcset="hero-large.webp 1200w,
                  hero-medium.webp 800w"
          type="image/webp">
  <source media="(min-width: 800px)"
          srcset="hero-large.jpg 1200w,
                  hero-medium.jpg 800w"
          type="image/jpeg">
  <img src="hero-medium.jpg"
       alt="Hero image description"
       class="hero-image"
       loading="lazy">
</picture>
```

## Project-Specific Guidelines

### React/JSX Integration
- When using HTML templates in React projects, follow JSX conventions
- Use className instead of class in JSX contexts
- Maintain consistency between HTML templates and React components

```html
<!-- Good - HTML template for email -->
<div class="email-template">
  <header class="email-header">
    <img src="{{logoUrl}}" alt="{{companyName}} Logo" class="logo">
  </header>
  <main class="email-content">
    <h1>Welcome to {{appName}}</h1>
    <p>Thank you for joining our platform.</p>
  </main>
</div>

<!-- Corresponding JSX structure -->
<!--
<div className="email-template">
  <header className="email-header">
    <img src={logoUrl} alt={`${companyName} Logo`} className="logo" />
  </header>
  <main className="email-content">
    <h1>Welcome to {appName}</h1>
    <p>Thank you for joining our platform.</p>
  </main>
</div>
-->
```

### Backstage-Specific Elements
- Use Backstage design system classes when available
- Follow component library patterns for consistency
- Include appropriate data attributes for analytics

```html
<!-- Good - Backstage component structure -->
<div class="backstage-page">
  <header class="page-header">
    <div class="page-title-container">
      <h1 class="page-title">Service Catalog</h1>
      <div class="page-actions">
        <button class="btn-primary"
                data-testid="create-service-btn"
                data-analytics="service-create-click">
          Create Service
        </button>
      </div>
    </div>
  </header>
  
  <main class="page-content">
    <div class="content-container">
      <!-- Page content -->
    </div>
  </main>
</div>
```

## Example HTML Structure (Project-Specific)

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Backstage - Developer Portal</title>
  <link rel="stylesheet" href="/css/main.css">
</head>
<body>
  <div class="app-container">
    <header class="app-header">
      <div class="container">
        <nav class="flex flex-col space-y-2
                     md:flex-row md:space-y-0 md:space-x-4"
             role="navigation"
             aria-label="Primary navigation">
          <div class="nav-brand">
            <img src="/logo.svg" alt="Backstage Logo" class="logo">
          </div>
          <ul class="nav-links">
            <li>
              <a href="/"
                 class="nav-link"
                 aria-current="page">
                Home
              </a>
            </li>
            <li>
              <a href="/catalog"
                 class="nav-link">
                Service Catalog
              </a>
            </li>
            <li>
              <a href="/docs"
                 class="nav-link">
                Documentation
              </a>
            </li>
          </ul>
        </nav>
      </div>
    </header>
    
    <main class="app-main" role="main">
      <div class="container">
        <section class="hero-section">
          <header>
            <h1 class="text-primary dark:text-primary-300">
              Welcome to Backstage
            </h1>
            <p class="hero-description">
              Your unified platform for developer productivity and service management.
            </p>
          </header>
          <div class="hero-actions">
            <button type="button"
                    class="btn btn-primary"
                    data-testid="get-started-btn">
              Get Started
            </button>
            <a href="/docs"
               class="btn btn-secondary">
              Learn More
            </a>
          </div>
        </section>
      </div>
    </main>
    
    <footer class="app-footer" role="contentinfo">
      <div class="container">
        <p>&copy; 2024 Backstage. All rights reserved.</p>
      </div>
    </footer>
  </div>
</body>
</html>
```

This HTML style guide follows Google's HTML/CSS style guidelines while preserving the project's specific formatting preferences for attributes and maintaining consistency with the Backstage design system.
