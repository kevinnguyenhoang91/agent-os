# Code Style Guide

## Context

Global code style rules for Agent OS projects.

<conditional-block context-check="general-formatting">
IF this General Formatting section already read in current context:
  SKIP: Re-reading this section
  NOTE: "Using General Formatting rules already in context"
ELSE:
  READ: The following formatting rules

## General Formatting

### Indentation
- Use 2 spaces for indentation (never tabs)
- Maintain consistent indentation throughout files
- Align nested structures for readability

### Naming Conventions
- **Methods and Variables**: Use snake_case (e.g., `user_profile`, `calculate_total`)
- **Classes and Modules**: Use PascalCase (e.g., `UserProfile`, `PaymentProcessor`)
- **Constants**: Use UPPER_SNAKE_CASE (e.g., `MAX_RETRY_COUNT`)

### String Formatting
- Use single quotes for strings: `'Hello World'`
- Use double quotes only when interpolation is needed
- Use template literals for multi-line strings or complex interpolation

### Code Comments
- Add brief comments above non-obvious business logic
- Document complex algorithms or calculations
- Explain the "why" behind implementation choices
- Never remove existing comments unless removing the associated code
- Update comments when modifying code to maintain accuracy
- Keep comments concise and relevant
</conditional-block>

<conditional-block task-condition="html-css-tailwind" context-check="html-css-style">
IF current task involves writing or updating HTML, CSS, or TailwindCSS:
  IF html-style.md AND css-style.md already in context:
    SKIP: Re-reading these files
    NOTE: "Using HTML/CSS style guides already in context"
  ELSE:
    <context_fetcher_strategy>
      IF context-fetcher agent exists:
        USE: @agent:context-fetcher
        REQUEST: "Get HTML formatting rules from code-style/html-style.md"
        REQUEST: "Get CSS and TailwindCSS rules from code-style/css-style.md"
        PROCESS: Returned style rules
      ELSE:
        READ the following style guides (only if not already in context):
        - @.agent-os/standards/code-style/html-style.md (if not in context)
        - @.agent-os/standards/code-style/css-style.md (if not in context)
    </context_fetcher_strategy>
ELSE:
  SKIP: HTML/CSS style guides not relevant to current task
</conditional-block>

<conditional-block task-condition="javascript" context-check="javascript-style">
IF current task involves writing or updating JavaScript:
  IF javascript-style.md already in context:
    SKIP: Re-reading this file
    NOTE: "Using JavaScript style guide already in context"
  ELSE:
    <context_fetcher_strategy>
      IF context-fetcher agent exists:
        USE: @agent:context-fetcher
        REQUEST: "Get JavaScript style rules from code-style/javascript-style.md"
        PROCESS: Returned style rules
      ELSE:
        READ: @.agent-os/standards/code-style/javascript-style.md
    </context_fetcher_strategy>
ELSE:
  SKIP: JavaScript style guide not relevant to current task
</conditional-block>

<conditional-block task-condition="typescript" context-check="typescript-style">
IF current task involves writing or updating TypeScript:
  IF typescript-style.md already in context:
    SKIP: Re-reading this file
    NOTE: "Using TypeScript style guide already in context"
  ELSE:
    <context_fetcher_strategy>
      IF context-fetcher agent exists:
        USE: @agent:context-fetcher
        REQUEST: "Get TypeScript style rules from code-style/javascript-style.md"
        PROCESS: Returned style rules
      ELSE:
        READ: @.agent-os/standards/code-style/typescript-style.md
    </context_fetcher_strategy>
ELSE:
  SKIP: TypeScript style guide not relevant to current task
</conditional-block>

<conditional-block task-condition="markdown" context-check="markdown-style">
IF current task involves writing or updating MarkDown:
  IF markdown-style.md already in context:
    SKIP: Re-reading this file
    NOTE: "Using MarkDown style guide already in context"
  ELSE:
    <context_fetcher_strategy>
      IF context-fetcher agent exists:
        USE: @agent:context-fetcher
        REQUEST: "Get MarkDown style rules from code-style/markdown-style.md"
        PROCESS: Returned style rules
      ELSE:
        READ: @.agent-os/standards/code-style/markdown-style.md
    </context_fetcher_strategy>
ELSE:
  SKIP: MarkDown style guide not relevant to current task
</conditional-block>

<conditional-block task-condition="sql" context-check="sql-style">
IF current task involves writing or updating SQL queries, database schemas, or migrations:
  IF sql-style.md already in context:
    SKIP: Re-reading this file
    NOTE: "Using SQL style guide already in context"
  ELSE:
    <context_fetcher_strategy>
      IF context-fetcher agent exists:
        USE: @agent:context-fetcher
        REQUEST: "Get SQL style rules from code-style/sql-style.md"
        PROCESS: Returned style rules
      ELSE:
        READ: @.agent-os/standards/code-style/sql-style.md
    </context_fetcher_strategy>
ELSE:
  SKIP: SQL style guide not relevant to current task
</conditional-block>

<conditional-block task-condition="shell" context-check="shell-style">
IF current task involves writing or updating shell scripts, build scripts, or DevOps automation:
  IF shell-style.md already in context:
    SKIP: Re-reading this file
    NOTE: "Using Shell Script style guide already in context"
  ELSE:
    <context_fetcher_strategy>
      IF context-fetcher agent exists:
        USE: @agent:context-fetcher
        REQUEST: "Get Shell Script style rules from code-style/shell-style.md"
        PROCESS: Returned style rules
      ELSE:
        READ: @.agent-os/standards/code-style/shell-style.md
    </context_fetcher_strategy>
ELSE:
  SKIP: Shell Script style guide not relevant to current task
</conditional-block>
