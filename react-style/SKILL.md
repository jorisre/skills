---
name: react-style
description: Enforce consistent React import patterns — import React once per file, use React.XXX for all React APIs including hooks and types. Triggers when reviewing React components or refactoring imports.
---

# React Style Guide

## Purpose
Enforce a consistent React import pattern that maximizes clarity and avoids naming conflicts. All React APIs (components, hooks, types) are accessed via the `React` namespace.

## Rules

### 1. Import Statement
Always import React at the top of the file:
```typescript
import React from "react";
```

Or if using TypeScript namespace:
```typescript
import * as React from "react";
```

Use only one of these patterns per file — never mix, never destructure.

### 2. Usage — All React APIs via `React.XXX`

**Hooks:**
```typescript
const [count, setCount] = React.useState(0);
React.useEffect(() => {}, []);
const value = React.useContext(MyContext);
```

**Types:**
```typescript
type Props = React.PropsWithChildren<{ id: string }>;
const Component: React.FC<Props> = ({ children }) => {
  // ...
};
```

**Utilities:**
```typescript
const Lazy = React.lazy(() => import("./Component"));
const Memoized = React.memo(MyComponent);
const Forwarded = React.forwardRef((props, ref) => {
  // ...
});
```

### 3. Fragments
Shorthand fragment syntax `<>...</>` is allowed and preferred over `<React.Fragment>`.

### 4. Custom Components
Custom components are used without the `React.` prefix:
```typescript
return <MyComponent prop={value} />;
```

### 5. Files Without JSX
Files that do not contain JSX do not need to import React (e.g., utils, hooks, types).

### 6. External Packages
Dependencies like `react-router`, `react-query` follow their own import conventions — do not force `React.XXX` on them.

## Example

```typescript
import React from "react";
import { useQuery } from "react-query";

type ButtonProps = React.PropsWithChildren<{
  onClick: () => void;
}>;

export const Button: React.FC<ButtonProps> = ({ children, onClick }) => {
  const [isLoading, setIsLoading] = React.useState(false);

  React.useEffect(() => {
    // side effect
  }, []);

  return (
    <>
      <button onClick={onClick} disabled={isLoading}>
        {children}
      </button>
    </>
  );
};
```

## Anti-patterns (Don't do this)

```typescript
// ❌ Destructuring React imports
import { useState, useEffect, FC } from "react";

// ❌ Mixing import styles
import React, { useState } from "react";

// ❌ Missing imports in files with JSX
const Component = () => <div>...</div>; // React must be in scope

// ❌ Unnecessary imports in non-JSX files
// utils.ts
import React from "react"; // not needed if no JSX
```
