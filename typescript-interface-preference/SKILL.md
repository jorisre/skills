---
name: typescript-interface-preference
description: Prefer interface over type for object shape declarations. Use type only for unions, tuples, utility types, and primitives. Applies project-wide to all type/interface declarations.
---

# TypeScript Interface Preference

## Purpose
Enforce consistent type declaration patterns across the entire codebase. Interfaces are preferred for object structures, while types are reserved for constructs that require them (unions, tuples, primitives, utilities).

## General Rules

### 1. Objects — Use Interface
For any object shape, always use `interface` instead of `type`:

```typescript
// ❌ Bad
type User = {
  id: string;
  name: string;
  email: string;
};

// ✅ Good
interface User {
  id: string;
  name: string;
  email: string;
}
```

### 2. Props Objects — Use Interface
All component props and function parameter objects use `interface`:

```typescript
// ❌ Bad
type ButtonProps = {
  loading?: boolean;
  disabled?: boolean;
  onClick?: () => void;
};

// ✅ Good
interface ButtonProps {
  loading?: boolean;
  disabled?: boolean;
  onClick?: () => void;
}

// ✅ Good with extend
interface ButtonProps extends React.ComponentProps<"button"> {
  loading?: boolean;
}
```

### 3. Extend Objects — Use Interface
When extending other objects, use `interface` with `extends`:

```typescript
// ❌ Bad
type ExtendedUser = User & {
  role: "admin" | "user";
  createdAt: Date;
};

// ✅ Good
interface ExtendedUser extends User {
  role: "admin" | "user";
  createdAt: Date;
}

// ✅ Good — extend multiple
interface Admin extends User, Timestamped {
  permissions: string[];
}
```

### 4. Keep Type — Unions
Unions must use `type`:

```typescript
// ✅ Good
type Status = "pending" | "active" | "inactive";
type Result = Success | Error;
type Maybe<T> = T | null | undefined;

// ❌ Bad — can't use interface for unions
interface Status = "pending" | "active"; // syntax error
```

### 5. Keep Type — Tuples
Tuples must use `type`:

```typescript
// ✅ Good
type Coordinates = [x: number, y: number];
type Response = [status: number, data: unknown];

// ❌ Bad — can't use interface for tuples
interface Coordinates = [number, number]; // not valid
```

### 6. Keep Type — Primitives & Aliases
Primitive aliases and type aliases use `type`:

```typescript
// ✅ Good
type ID = string;
type Timestamp = number;
type Email = string & { readonly __brand: "Email" };

// ❌ Bad
interface ID extends string {} // awkward, use type instead
```

### 7. Keep Type — Utility Types
Utility types and generic type utilities use `type`:

```typescript
// ✅ Good
type Readonly<T> = { readonly [K in keyof T]: T[K] };
type Partial<T> = { [K in keyof T]?: T[K] };
type Pick<T, K extends keyof T> = { [P in K]: T[P] };

// ❌ Bad
interface Readonly<T> { readonly [K in keyof T]: T[K] } // awkward syntax
```

### 8. Interface Merging is OK
Multiple interface declarations with the same name merge:

```typescript
// ✅ Good — interfaces merge
interface User {
  id: string;
  name: string;
}

interface User {
  email: string;
}

// Result: User has id, name, and email
const user: User = { id: "1", name: "John", email: "john@example.com" };
```

### 9. Callable Signatures
For function signatures, prefer `interface`:

```typescript
// ✅ Good
interface Middleware {
  (req: Request, res: Response): void;
}

interface EventEmitter {
  on(event: string, listener: (...args: any[]) => void): void;
  emit(event: string, ...args: any[]): void;
}

// Also acceptable as type
type Middleware = (req: Request, res: Response) => void;
```

### 10. Import Types
When importing type-only declarations, use the `type` import keyword:

```typescript
// ✅ Good — type imports
import { type User } from "./types";
import { type ButtonProps } from "./components/button";

// ✅ Also good — multiple type imports
import { type User, type ApiResponse } from "./api/types";

// ✅ Mixed imports (values and types)
import { Logger, type LogLevel } from "./logger";

// ❌ Avoid — creates runtime dependency even when User is only used in types
import { User } from "./types"; // includes module in bundle even if only used for types
```

---

## Examples

### React Component Props

```typescript
// ✅ Good
interface ButtonProps extends React.ComponentProps<"button"> {
  variant?: "primary" | "secondary";
  size?: "sm" | "md" | "lg";
  loading?: boolean;
}

export function Button(props: ButtonProps) {
  // ...
}

// ✅ Good
interface ModalProps {
  isOpen: boolean;
  onClose: () => void;
  title: string;
  children: React.ReactNode;
}

export function Modal(props: ModalProps) {
  // ...
}
```

### API Models

```typescript
// ✅ Good
interface User {
  id: string;
  email: string;
  createdAt: Date;
}

interface Admin extends User {
  permissions: string[];
  role: "admin";
}

interface ApiResponse<T> {
  data: T;
  status: number;
  message?: string;
}
```

### Utility Types (Keep as type)

```typescript
// ✅ Good
type ApiError = {
  code: string;
  message: string;
  details?: unknown;
};

type Status = "pending" | "success" | "error";

type Coordinates = [x: number, y: number];

type Nullable<T> = T | null;

type DeepPartial<T> = T extends object
  ? { [P in keyof T]?: DeepPartial<T[P]> }
  : T;
```

### Function Parameters

```typescript
// ✅ Good
interface SearchOptions {
  query: string;
  limit?: number;
  offset?: number;
  filters?: Record<string, string>;
}

function search(options: SearchOptions): Promise<Result[]> {
  // ...
}

// ✅ Good
interface Logger {
  log(level: string, message: string): void;
  error(message: string, error?: Error): void;
}

function createLogger(name: string): Logger {
  // ...
}
```

---

## Anti-patterns (Don't do this)

```typescript
// ❌ Type for object shape
type User = {
  id: string;
  name: string;
};

// ❌ Type with intersection instead of interface extend
type ExtendedUser = User & {
  role: "admin";
};

// ❌ Interface for unions (syntax error)
interface Status = "pending" | "done";

// ❌ Interface for primitives
interface ID extends string {}

// ❌ Interface for tuples
interface Tuple = [number, number];

// ❌ Type for simple object props
type ButtonProps = {
  onClick: () => void;
  disabled: boolean;
};
```

## Checklist

- [ ] All object shapes use `interface` instead of `type`
- [ ] React component Props use `interface`
- [ ] Extending other objects uses `interface` with `extends`
- [ ] Unions remain as `type` (can't use interface)
- [ ] Tuples remain as `type` (can't use interface)
- [ ] Primitive aliases use `type`
- [ ] Utility types use `type`
- [ ] Function signatures use `interface` (or `type` if preferred for shorthand)
- [ ] No unnecessary `type` declarations for objects
