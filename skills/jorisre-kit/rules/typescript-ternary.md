---
name: typescript-ternary
description: Enforce ternary operator pattern (condition ? value : null) and ban short-circuit && operator for clarity. Use optional chaining when possible. Applies to assignments, returns, JSX, and side effects.
---

# Ternary & Conditional Style Guide

## Purpose
Enforce explicit, readable conditional patterns throughout the codebase. Eliminate implicit short-circuit patterns (`&&`, `||`) in favor of explicit ternaries or if statements.

## General Rules

### 1. Ban Short-Circuit `&&` for Values
Never use `&&` to conditionally assign or return values. Use ternary operator instead:

```typescript
// ❌ Bad
const result = condition && value;
return condition && data;

// ✅ Good
const result = condition ? value : null;
return condition ? data : null;

// ✅ Even better if undefined is acceptable
const result = condition ? value : undefined;
```

### 2. Prefer Optional Chaining Over Ternary
When accessing properties, use optional chaining (`?.`) as first choice:

```typescript
// ❌ Bad
const name = user != null ? user.name : null;

// ✅ Good (optimal)
const name = user?.name;

// ✅ Acceptable if you need explicit null/undefined fallback
const name = user?.name ?? defaultName;
```

### 3. Explicit Ternary Syntax
When using ternaries, always be explicit about both branches:

```typescript
// ❌ Bad (implicit undefined)
const result = condition ? value;

// ✅ Good
const result = condition ? value : null;
const result = condition ? value : undefined;
```

### 4. Side Effects — Use If Statement
Never use `&&` for side effects. Use explicit if statement:

```typescript
// ❌ Bad
condition && sideEffect();
isValid && validateData();

// ✅ Good
if (condition) {
  sideEffect();
}

if (isValid) {
  validateData();
}
```

### 5. Ternary in JSX
In JSX, render conditionally with explicit ternary, not `&&`:

```typescript
// ❌ Bad
<div>
  {condition && <Component />}
</div>

// ✅ Good
<div>
  {condition ? <Component /> : null}
</div>

// ✅ Or with Fragment if nothing renders
<div>
  {condition ? <Component /> : <></>}
</div>
```

### 6. Boolean Simplification
Reduce redundant ternaries:

```typescript
// ❌ Bad
const isValid = condition ? true : false;

// ✅ Good
const isValid = condition;
const isValid = !!value; // explicit coercion if needed
```

### 7. Nested Ternaries
Nested ternaries are acceptable if readable. Consider breaking into multiple statements if it becomes complex:

```typescript
// ✅ Acceptable
const status = age < 18 ? "minor" : age < 65 ? "adult" : "senior";

// ✅ Also acceptable (more readable)
let status;
if (age < 18) {
  status = "minor";
} else if (age < 65) {
  status = "adult";
} else {
  status = "senior";
}
```

---

## Examples

### TypeScript

```typescript
// Value assignment
const displayName = user ? user.name : "Guest";
const displayName = user?.name ?? "Guest"; // even better with optional chaining

// Return statement
function getAge(): number {
  return age > 0 ? age : 0;
}

// Null/undefined checks
const email = customer?.email ?? "no-email@example.com";
const phone = contact != null ? contact.phone : null;

// Side effect
function processData(data: Data) {
  if (data.isValid) {
    validateAndSave(data);
  }
}

// Complex conditional
const role = isAdmin ? "admin" : isEditor ? "editor" : "viewer";
```

### React/JSX

```typescript
interface CardProps {
  title: string;
  data?: DataItem;
  isLoading?: boolean;
}

export const Card: React.FC<CardProps> = ({ title, data, isLoading }) => {
  return (
    <div className="card">
      <h2>{title}</h2>
      
      {/* ✅ Good: explicit ternary */}
      {isLoading ? <Spinner /> : null}
      
      {/* ✅ Good: optional chaining + nullish coalescing */}
      <p>{data?.description ?? "No description"}</p>
      
      {/* ✅ Good: side effect in proper context */}
      {React.useEffect(() => {
        if (data) {
          fetchRelatedItems(data.id);
        }
      }, [data])}
      
      {/* ✅ Good: render content or empty fragment */}
      {data ? (
        <div>
          <h3>{data.title}</h3>
          <p>{data.content}</p>
        </div>
      ) : (
        <></>
      )}
    </div>
  );
};
```

---

## Anti-patterns (Don't do this)

```typescript
// ❌ Short-circuit && for values
const result = condition && value;
const output = isValid && getData();
return user && user.name;

// ❌ Short-circuit && for side effects
condition && console.log("error");
isReady && fetchData();

// ❌ Short-circuit && in JSX
{isVisible && <Modal />}
{hasError && <ErrorBanner message={error} />}

// ❌ Implicit undefined in ternary
const value = condition ? data;

// ❌ Unnecessary ternary
const flag = isValid ? true : false;
```

## Checklist

- [ ] No `&&` used for value assignment or returns
- [ ] No `&&` used for side effects (use `if` instead)
- [ ] No `&&` in JSX conditionals (use ternary with explicit branches)
- [ ] Optional chaining (`?.`) used when accessing nested properties
- [ ] All ternaries have explicit branches (`condition ? value : null/undefined`)
- [ ] Boolean ternaries simplified to their conditions
- [ ] Nested ternaries readable or broken into multiple statements
