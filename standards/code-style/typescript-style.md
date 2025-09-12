# TypeScript Style Guide

Based on Google TypeScript Style Guide with Backstage project adaptations.

## General Formatting

### Indentation and Spacing
- Use **2 spaces** for indentation (never tabs)
- Maximum line length: **80 characters**
- Use trailing commas in multiline arrays, objects, and type definitions
- Add spaces around operators and after commas

### Semicolons
- Always use semicolons to terminate statements
- Required for all statements, including interface and type declarations

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

## Type Annotations

### Explicit vs Inferred Types
- Prefer type inference when the type is obvious
- Use explicit types for function parameters and return values
- Use explicit types for exported APIs

```typescript
// Good - inference is clear
const userName = "john_doe";
const users = ["alice", "bob", "charlie"];

// Good - explicit types for APIs
function getUserById(id: string): Promise<User | null> {
  return userService.findById(id);
}

// Good - explicit types for complex objects
const config: DatabaseConfig = {
  host: "localhost",
  port: 5432,
  database: "backstage",
};

// Bad - unnecessary explicit types
const userName: string = "john_doe";
const count: number = users.length;
```

### Type vs Interface
- Use `interface` for object shapes that might be extended
- Use `type` for unions, intersections, and computed types
- Prefer `interface` for public APIs

```typescript
// Good - interface for extensible shapes
interface User {
  id: string;
  name: string;
  email: string;
}

interface AdminUser extends User {
  permissions: string[];
}

// Good - type for unions and intersections
type Status = "pending" | "approved" | "rejected";
type UserWithStatus = User & {status: Status};

// Good - type for computed types
type UserKeys = keyof User;
type PartialUser = Partial<User>;
```

## Naming Conventions

### Types and Interfaces
- Use **PascalCase** for interfaces, types, classes, and enums
- Avoid prefixing interfaces with `I`
- Use descriptive names that indicate purpose

```typescript
// Good
interface UserProfile {
  id: string;
  displayName: string;
}

type ApiResponse<T> = {
  data: T;
  status: number;
  message?: string;
};

enum UserRole {
  ADMIN = "admin",
  USER = "user",
  GUEST = "guest",
}

// Bad
interface IUser {
  id: string;
}

type userProfile = {
  id: string;
};
```

### Generic Type Parameters
- Use single uppercase letters starting with `T`
- Use descriptive names for complex generics

```typescript
// Good - simple generics
interface Repository<T> {
  findById(id: string): Promise<T | null>;
  save(entity: T): Promise<T>;
}

// Good - descriptive names for complex scenarios
interface ApiClient<TRequest, TResponse> {
  send(request: TRequest): Promise<TResponse>;
}

// Good - constraint naming
interface Identifiable {
  id: string;
}

function updateEntity<TEntity extends Identifiable>(
  entity: TEntity,
  updates: Partial<TEntity>,
): TEntity {
  return {...entity, ...updates};
}
```

## Function and Method Declarations

### Function Signatures
- Always provide explicit parameter and return types for exported functions
- Use readonly for parameters that shouldn't be modified
- Prefer function declarations over arrow functions for top-level functions

```typescript
// Good - explicit types
export function createUser(
  userData: Readonly<CreateUserRequest>,
): Promise<User> {
  return userService.create(userData);
}

// Good - arrow function for callbacks
const processUsers = (users: readonly User[]): ProcessedUser[] => {
  return users.map(user => ({
    ...user,
    processed: true,
    processedAt: new Date(),
  }));
};

// Good - method signatures
class UserService {
  async findById(id: string): Promise<User | null> {
    return this.repository.findById(id);
  }

  async create(userData: CreateUserRequest): Promise<User> {
    const user = new User(userData);
    return this.repository.save(user);
  }
}
```

### Optional Parameters and Default Values
- Use optional parameters (`?`) for truly optional values
- Provide default values when appropriate
- Place optional parameters after required ones

```typescript
// Good
interface CreateUserOptions {
  sendWelcomeEmail?: boolean;
  role?: UserRole;
}

function createUser(
  userData: UserData,
  options: CreateUserOptions = {},
): Promise<User> {
  const {sendWelcomeEmail = true, role = UserRole.USER} = options;
  // Implementation
}

// Good - with destructuring defaults
function buildApiUrl(
  endpoint: string,
  {baseUrl = "/api", version = "v1"}: ApiOptions = {},
): string {
  return `${baseUrl}/${version}/${endpoint}`;
}
```

## Class Definitions

### Access Modifiers
- Use `private` for internal implementation details
- Use `protected` for inheritance scenarios
- Use `public` explicitly when clarity is important
- Use `readonly` for immutable properties

```typescript
// Good
class UserService {
  private readonly repository: UserRepository;
  private readonly logger: Logger;

  constructor(
    repository: UserRepository,
    logger: Logger,
  ) {
    this.repository = repository;
    this.logger = logger;
  }

  public async findById(id: string): Promise<User | null> {
    try {
      return await this.repository.findById(id);
    } catch (error) {
      this.logger.error("Failed to find user", {id, error});
      throw error;
    }
  }

  protected validateUserData(userData: UserData): void {
    if (!userData.email) {
      throw new Error("Email is required");
    }
  }
}
```

### Abstract Classes and Inheritance
- Use abstract classes for shared implementation with required overrides
- Prefer composition over inheritance when possible

```typescript
// Good - abstract base class
abstract class BaseEntity {
  public readonly id: string;
  public readonly createdAt: Date;
  public readonly updatedAt: Date;

  constructor(id: string) {
    this.id = id;
    this.createdAt = new Date();
    this.updatedAt = new Date();
  }

  abstract validate(): boolean;

  protected touch(): void {
    (this as any).updatedAt = new Date();
  }
}

class User extends BaseEntity {
  constructor(
    id: string,
    public readonly name: string,
    public readonly email: string,
  ) {
    super(id);
  }

  validate(): boolean {
    return this.name.length > 0 && this.email.includes("@");
  }
}
```

## Type Definitions

### Union Types
- Use union types for controlled sets of values
- Prefer string literal unions over enums when values are simple

```typescript
// Good - string literal unions
type HttpMethod = "GET" | "POST" | "PUT" | "DELETE";
type Environment = "development" | "staging" | "production";

// Good - discriminated unions
type ApiResult<T> =
  | {success: true; data: T}
  | {success: false; error: string};

// Good - complex unions with type guards
type DatabaseEntity = User | Team | Project;

function isUser(entity: DatabaseEntity): entity is User {
  return "email" in entity;
}
```

### Utility Types
- Use built-in utility types when appropriate
- Create custom utility types for common patterns

```typescript
// Good - built-in utility types
type CreateUserRequest = Omit<User, "id" | "createdAt" | "updatedAt">;
type UpdateUserRequest = Partial<Pick<User, "name" | "email">>;
type UserKeys = keyof User;

// Good - custom utility types
type NonNullable<T> = T extends null | undefined ? never : T;
type RequiredFields<T, K extends keyof T> = T & Required<Pick<T, K>>;

// Usage
type UserWithRequiredEmail = RequiredFields<Partial<User>, "email">;
```

### Conditional Types
- Use conditional types for advanced type transformations
- Keep complex conditional types well-documented

```typescript
// Good - conditional type with documentation
/**
 * Extracts the return type of a promise-returning function
 */
type PromiseReturnType<T> = T extends (...args: any[]) => Promise<infer R>
  ? R
  : never;

// Good - mapped types
type ApiEndpoints<T> = {
  [K in keyof T]: T[K] extends (...args: any[]) => any
    ? T[K]
    : never;
};
```

## Error Handling

### Error Types
- Define specific error types for different error categories
- Use discriminated unions for error handling

```typescript
// Good - specific error types
class ValidationError extends Error {
  constructor(
    message: string,
    public readonly field: string,
  ) {
    super(message);
    this.name = "ValidationError";
  }
}

class NotFoundError extends Error {
  constructor(resource: string, id: string) {
    super(`${resource} with id ${id} not found`);
    this.name = "NotFoundError";
  }
}

// Good - result type pattern
type Result<T, E = Error> =
  | {success: true; data: T}
  | {success: false; error: E};

async function safeUserLookup(id: string): Promise<Result<User, NotFoundError>> {
  try {
    const user = await userService.findById(id);
    return user
      ? {success: true, data: user}
      : {success: false, error: new NotFoundError("User", id)};
  } catch (error) {
    return {success: false, error: error as NotFoundError};
  }
}
```

## Async/Await and Promises

### Promise Handling
- Prefer `async/await` over Promise chains
- Always handle or propagate errors
- Use proper typing for Promise return values

```typescript
// Good - async/await with proper typing
async function fetchUserProfile(userId: string): Promise<UserProfile> {
  try {
    const user = await userService.findById(userId);
    if (!user) {
      throw new NotFoundError("User", userId);
    }

    const profile = await profileService.getProfile(user.id);
    return profile;
  } catch (error) {
    logger.error("Failed to fetch user profile", {userId, error});
    throw error;
  }
}

// Good - parallel async operations
async function getUserWithPermissions(
  userId: string,
): Promise<UserWithPermissions> {
  const [user, permissions] = await Promise.all([
    userService.findById(userId),
    permissionService.getUserPermissions(userId),
  ]);

  if (!user) {
    throw new NotFoundError("User", userId);
  }

  return {
    ...user,
    permissions,
  };
}
```

## Modules and Imports

### Import/Export Patterns
- Use named exports by default
- Use default exports sparingly, only for primary module exports
- Group imports logically and sort alphabetically within groups

```typescript
// Good - named imports grouped logically
import {Logger} from "@backstage/backend-common";
import {Config} from "@backstage/config";

import {DatabaseService} from "../database";
import {EmailService} from "../email";

import {User, UserRole} from "./types";
import {UserRepository} from "./user-repository";

// Good - named exports
export {UserService};
export {UserRepository};
export type {User, UserRole, CreateUserRequest};

// Good - default export for primary class
export default class UserManager {
  // Implementation
}
```

### Re-exports
- Use index files for clean module interfaces
- Re-export types and implementations separately when needed

```typescript
// Good - index.ts file
export {UserService} from "./user-service";
export {UserRepository} from "./user-repository";
export type {
  User,
  UserRole,
  CreateUserRequest,
  UpdateUserRequest,
} from "./types";

// Good - separate type exports
export type * from "./types";
export * from "./services";
```

## Documentation

### TSDoc Comments
- Use TSDoc for all public APIs
- Document complex types and generic constraints
- Include examples for non-obvious usage

```typescript
/**
 * Service for managing user accounts and authentication
 *
 * @example
 * ```typescript
 * const userService = new UserService(repository, logger);
 * const user = await userService.createUser({
 *   name: "John Doe",
 *   email: "john@example.com"
 * });
 * ```
 */
export class UserService {
  /**
   * Creates a new user account
   *
   * @param userData - The user data for account creation
   * @param options - Additional options for user creation
   * @returns Promise resolving to the created user
   * @throws {ValidationError} When user data is invalid
   * @throws {ConflictError} When user already exists
   */
  async createUser(
    userData: CreateUserRequest,
    options: CreateUserOptions = {},
  ): Promise<User> {
    // Implementation
  }
}

/**
 * Represents a user in the system
 *
 * @public
 */
interface User {
  /** Unique identifier for the user */
  readonly id: string;
  /** Display name of the user */
  name: string;
  /** Email address (must be unique) */
  email: string;
  /** User's role in the system */
  role: UserRole;
  /** Timestamp when the user was created */
  readonly createdAt: Date;
}
```

## Backstage-Specific Guidelines

### Backend Plugin Patterns
- Follow Backstage's new backend system patterns
- Use proper dependency injection with `coreServices`
- Implement proper service interfaces

```typescript
// Good - Backstage backend plugin structure
import {
  coreServices,
  createBackendPlugin,
} from "@backstage/backend-plugin-api";
import {loggerToWinstonLogger} from "@backstage/backend-common";

interface UserPluginOptions {
  maxRetries?: number;
}

export const userPlugin = createBackendPlugin({
  pluginId: "user",
  register(env, options?: UserPluginOptions) {
    env.registerInit({
      deps: {
        logger: coreServices.logger,
        database: coreServices.database,
        httpRouter: coreServices.httpRouter,
      },
      async init({logger, database, httpRouter}) {
        const winstonLogger = loggerToWinstonLogger(logger);
        const userService = new UserService(database, winstonLogger);
        
        httpRouter.use("/users", await createUserRouter(userService));
      },
    });
  },
});
```

### Frontend Component Patterns
- Use proper React TypeScript patterns
- Implement proper prop interfaces
- Use Backstage's design system types

```typescript
// Good - React component with proper typing
interface UserProfileProps {
  userId: string;
  onUserUpdate?: (user: User) => void;
  className?: string;
}

export const UserProfile: React.FC<UserProfileProps> = ({
  userId,
  onUserUpdate,
  className,
}) => {
  const [user, setUser] = useState<User | null>(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<Error | null>(null);

  useEffect(() => {
    const fetchUser = async () => {
      try {
        setLoading(true);
        const userData = await userApi.getUser(userId);
        setUser(userData);
      } catch (err) {
        setError(err as Error);
      } finally {
        setLoading(false);
      }
    };

    fetchUser();
  }, [userId]);

  if (loading) return <Progress />;
  if (error) return <ErrorPanel error={error} />;
  if (!user) return <EmptyState />;

  return (
    <div className={className}>
      {/* Component content */}
    </div>
  );
};
```

## Performance and Best Practices

### Type Performance
- Avoid overly complex conditional types
- Use readonly for immutable data structures
- Prefer specific types over `any` or `unknown`

```typescript
// Good - performance-conscious types
interface ReadonlyUserList {
  readonly users: readonly User[];
  readonly total: number;
  readonly page: number;
}

// Good - specific union types
type UserAction = 
  | {type: "CREATE"; payload: CreateUserRequest}
  | {type: "UPDATE"; payload: {id: string; updates: Partial<User>}}
  | {type: "DELETE"; payload: {id: string}};

// Avoid - overly complex types that slow compilation
type OverlyComplex<T> = T extends Record<string, any>
  ? T extends {[K in keyof T]: T[K] extends Function ? never : T[K]}
    ? // ... many more levels
    : never
  : never;
```

This TypeScript style guide provides comprehensive guidelines that align with Google's TypeScript standards while being specifically adapted for the Backstage project's needs and conventions.
