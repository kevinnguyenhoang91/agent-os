# JavaScript Style Guide

Based on Google JavaScript Style Guide with project-specific adaptations.

## General Formatting

### Indentation and Spacing
- Use **2 spaces** for indentation (never tabs)
- Maximum line length: **80 characters**
- Use trailing commas in multiline arrays and objects
- Add spaces around operators and after commas

### Semicolons
- Always use semicolons to terminate statements
- Do not rely on automatic semicolon insertion (ASI)

## Naming Conventions

### Variables and Functions
- Use **camelCase** for variables, functions, and methods
- Use **PascalCase** for constructors and classes
- Use **SCREAMING_SNAKE_CASE** for constants

```javascript
// Good
const userName = "john_doe";
const MAX_RETRY_COUNT = 3;
class UserManager {}
function getUserById(id) {}

// Bad
const user_name = "john_doe";
const maxRetryCount = 3;
class userManager {}
function get_user_by_id(id) {}
```

### Files and Directories
- Use **kebab-case** for file names: `user-service.js`
- Use **camelCase** for directory names when appropriate

## String Literals

### Quote Style
- Use **double quotes** for strings and object keys
- Use template literals for string interpolation
- Use single quotes only when avoiding escaping double quotes

```javascript
// Good
const message = "Hello world";
const greeting = `Hello ${name}!`;
const html = '<div class="container">Content</div>';

// Bad
const message = 'Hello world';
const greeting = "Hello " + name + "!";
```

## Object and Array Formatting

### Objects
```javascript
// Good - short objects on one line
const user = {id: 1, name: "John"};

// Good - multiline for complex objects
const config = {
  apiUrl: "https://api.example.com",
  timeout: 5000,
  retries: 3,
};

// Bad
const config = {apiUrl: "https://api.example.com", timeout: 5000, retries: 3};
```

### Arrays
```javascript
// Good
const numbers = [1, 2, 3, 4];
const users = [
  {id: 1, name: "Alice"},
  {id: 2, name: "Bob"},
  {id: 3, name: "Charlie"},
];

// Bad
const users = [{id: 1, name: "Alice"}, {id: 2, name: "Bob"}];
```

## Function Declarations

### Function Syntax
- Prefer `function` declarations for top-level functions
- Use arrow functions for callbacks and functional programming
- Use `const` for function expressions

```javascript
// Good - function declaration
function calculateTotal(items) {
  return items.reduce((sum, item) => sum + item.price, 0);
}

// Good - arrow function for callbacks
const processedItems = items.map(item => ({
  ...item,
  processed: true,
}));

// Good - const function expression
const validateEmail = (email) => {
  return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);
};
```

### Parameters
- Use destructuring for object parameters
- Provide default values when appropriate
- Use rest parameters instead of `arguments`

```javascript
// Good
function createUser({name, email, role = "user"}) {
  return {name, email, role, createdAt: new Date()};
}

function sum(...numbers) {
  return numbers.reduce((a, b) => a + b, 0);
}

// Bad
function createUser(userObj) {
  const name = userObj.name;
  const email = userObj.email;
  const role = userObj.role || "user";
}
```

## Variable Declarations

### const, let, var
- Use `const` by default
- Use `let` when reassignment is needed
- Never use `var`

```javascript
// Good
const apiUrl = "https://api.example.com";
let currentUser = null;

for (let i = 0; i < items.length; i++) {
  // Process items
}

// Bad
var apiUrl = "https://api.example.com";
var currentUser = null;
```

### Destructuring
- Use destructuring for arrays and objects when it improves readability

```javascript
// Good
const {name, email} = user;
const [first, second] = coordinates;

// Good - with defaults
const {timeout = 5000, retries = 3} = config;

// Good - nested destructuring
const {
  user: {name, email},
  settings: {theme},
} = response.data;
```

## Control Structures

### Conditionals
- Always use braces for blocks, even single statements
- Use explicit comparisons when appropriate

```javascript
// Good
if (user.isActive) {
  processUser(user);
}

if (count === 0) {
  showEmptyState();
}

// Bad
if (user.isActive)
  processUser(user);

if (!count) {
  showEmptyState();
}
```

### Loops
- Prefer `for...of` for iterating arrays
- Use `for...in` with `hasOwnProperty` check for objects
- Consider functional methods (`map`, `filter`, `reduce`) when appropriate

```javascript
// Good
for (const item of items) {
  processItem(item);
}

for (const key in object) {
  if (object.hasOwnProperty(key)) {
    processProperty(key, object[key]);
  }
}

// Better for transformations
const activeUsers = users.filter(user => user.isActive);
const userNames = users.map(user => user.name);
```

## Error Handling

### Try-Catch
- Use specific error types when possible
- Always handle or re-throw errors

```javascript
// Good
try {
  const data = await fetchUserData(userId);
  return processData(data);
} catch (error) {
  if (error instanceof NetworkError) {
    logger.warn("Network error fetching user data", {userId, error});
    return getDefaultUserData();
  }
  throw error;
}
```

## Comments and Documentation

### JSDoc Comments
- Use JSDoc for public APIs and complex functions
- Document parameters, return values, and exceptions

```javascript
/**
 * Calculates the total price including tax and discounts.
 * @param {Object} options - Calculation options
 * @param {number} options.basePrice - Base price before modifications
 * @param {number} [options.taxRate=0.1] - Tax rate (default 10%)
 * @param {number} [options.discount=0] - Discount amount
 * @returns {number} Final price after tax and discounts
 * @throws {Error} When basePrice is negative
 */
function calculateTotal({basePrice, taxRate = 0.1, discount = 0}) {
  if (basePrice < 0) {
    throw new Error("Base price cannot be negative");
  }
  return (basePrice - discount) * (1 + taxRate);
}
```

### Inline Comments
- Use `//` for single-line comments
- Add comments to explain "why", not "what"
- Keep comments concise and up-to-date

```javascript
// Good - explains reasoning
// Retry failed requests up to 3 times to handle transient network issues
const maxRetries = 3;

// Bad - states the obvious
// Set maxRetries to 3
const maxRetries = 3;
```

## Modern JavaScript Features

### ES6+ Features
- Use template literals for string interpolation
- Use destructuring assignments
- Use arrow functions appropriately
- Use `async/await` instead of Promise chains

```javascript
// Good - modern syntax
const buildApiUrl = (endpoint, params = {}) => {
  const queryString = new URLSearchParams(params).toString();
  return `${API_BASE_URL}/${endpoint}${queryString ? `?${queryString}` : ""}`;
};

async function fetchUserProfile(userId) {
  try {
    const response = await fetch(buildApiUrl("users", {id: userId}));
    const {data} = await response.json();
    return data;
  } catch (error) {
    logger.error("Failed to fetch user profile", {userId, error});
    throw error;
  }
}
```

### Modules
- Use ES6 module syntax
- Prefer named exports over default exports
- Group imports logically

```javascript
// Good
import {UserService, EmailService} from "./services";
import {validateEmail, formatDate} from "./utils";
import {config} from "./config";

export {UserManager};
export {EmailNotifier};

// Avoid default exports unless there's a single primary export
export default class ApiClient {
  // Implementation
}
```

## Performance Considerations

### Object and Array Operations
- Use `Object.assign()` or spread operator for object copying
- Use appropriate array methods for better readability

```javascript
// Good - object copying
const updatedUser = {...user, lastLogin: new Date()};

// Good - array operations
const activeUsers = users.filter(user => user.status === "active");
const userEmails = users.map(user => user.email);

// Good - find operations
const targetUser = users.find(user => user.id === targetId);
```

### Memory Management
- Avoid memory leaks with event listeners
- Clean up resources properly

```javascript
// Good - cleanup pattern
class EventManager {
  constructor() {
    this.listeners = new Map();
    this.cleanup = this.cleanup.bind(this);
    window.addEventListener("beforeunload", this.cleanup);
  }

  cleanup() {
    this.listeners.forEach((listener, element) => {
      element.removeEventListener("click", listener);
    });
    this.listeners.clear();
  }
}
```

## Project-Specific Guidelines

### Backstage Integration
- Follow Backstage's TypeScript patterns when applicable
- Use consistent error handling patterns
- Maintain compatibility with existing codebase conventions
- Use double quotes consistently with existing project style
