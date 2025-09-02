# Markdown Style Guide

Based on Google's Markdown Style Guide with project-specific adaptations.

## Document Structure

### Headings
- No empty lines after headings
- Use ATX-style headings (`#`) instead of Setext-style (`===` or `---`)
- Start with H1 (`#`) and increment hierarchically
- Use sentence case for headings
- Don't skip heading levels (H1 → H2 → H3, not H1 → H3)

### Line Length
- Limit lines to 80 characters where possible
- Break long lines at natural boundaries (spaces, punctuation)
- Don't break URLs or code spans

### Spacing
- Use 2 spaces for indentation in lists
- Add blank line before and after:
  - Code blocks
  - Lists
  - Tables
  - Block quotes
- No trailing whitespace on lines
- End files with a single newline

## Text Formatting

### Emphasis
- Use `**bold**` for strong emphasis (not `__bold__`)
- Use `*italic*` for emphasis (not `_italic_`)
- Use `***bold italic***` for combined emphasis
- Use `inline code` for code references, filenames, and commands

### Lists
- Use `-` for unordered lists (consistent bullet style)
- Use `1.` for ordered lists (let markdown handle numbering)
- Indent nested lists with 2 spaces
- Add blank line before first item if list follows paragraph

```markdown
Here's a list:

- First item
- Second item
  - Nested item
  - Another nested item
- Third item
```

### Links
- Use reference-style links for readability in long documents
- Use inline links for short, simple URLs
- Use descriptive link text (not "click here" or raw URLs)
- Place reference definitions at bottom of document

```markdown
Visit the [Backstage documentation](https://backstage.io/docs) for details.

Or use reference style: [Backstage docs][backstage-docs]

[backstage-docs]: https://backstage.io/docs
```

## Code and Technical Content

### Code Blocks
- Use fenced code blocks with language specification
- Indent code blocks consistently with surrounding content
- Use backticks for inline code spans

```markdown
Use `yarn dev` to start development:

```bash
yarn install
yarn dev
```
```

### Tables
- Align table columns using pipes
- Use header row with dashes
- Left-align text columns, right-align number columns

```markdown
| Feature | Status | Priority |
|---------|--------|----------|
| Authentication | Complete | High |
| Templates | In Progress | High |
| Documentation | Planned | Medium |
```

### File Paths and Commands
- Use `inline code` for file paths, commands, and technical terms
- Use forward slashes for paths (cross-platform compatibility)
- Include full command syntax in code blocks

## Documentation Best Practices

### Structure
- Start with brief description or overview
- Use consistent heading hierarchy
- Include table of contents for long documents
- End with "Next steps" or "Related links" section

### Content Guidelines
- Write in second person ("you") for instructions
- Use active voice where possible
- Be concise but comprehensive
- Include examples for complex concepts
- Use consistent terminology throughout

### Cross-References
- Link to related documentation
- Use relative paths for internal links
- Verify all links work before publishing

## Project-Specific Conventions

### Backstage Documentation
- Use `backstage.io` domain for official documentation links
- Reference plugin names with backticks: `@backstage/plugin-catalog`
- Include version numbers where relevant
- Use YAML examples for configuration

### API Documentation
- Document parameters with types and descriptions
- Include example requests and responses
- Use OpenAPI/Swagger format references where applicable

### Troubleshooting Sections
- Use consistent "Problem/Solution" format
- Include error messages in code blocks
- Provide step-by-step resolution instructions

## Examples

### Good Heading Structure
```markdown
# Main Title

## Overview

Brief description of the document.

## Getting Started

### Prerequisites

- Node.js 20 or higher
- Yarn 4.4.1+

### Installation

1. Clone the repository
2. Install dependencies
3. Start development server
```

### Good List Formatting
```markdown
Available commands:

- `yarn dev` - Start development server
- `yarn build` - Build for production
- `yarn test` - Run test suite
  - `yarn test:unit` - Unit tests only
  - `yarn test:e2e` - End-to-end tests only
```

### Good Table Example
```markdown
| Environment | URL | Status |
|-------------|-----|--------|
| Development | http://localhost:3001 | Active |
| Staging | https://staging.ghn.dev | Active |
| Production | https://portal.ghn.vn | Active |
```
