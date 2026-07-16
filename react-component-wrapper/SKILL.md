---
name: react-component-wrapper
description: Enforce consistent component wrapper pattern — extend underlying element/component props, spread first, override specific props (className via cn, event handlers, disabled). No forwardRef needed in modern React.
---

# React Component Wrapper Pattern

## Purpose
Enforce a consistent pattern for wrapper components that compose DOM elements or other React components. This pattern ensures flexibility, maintainability, and proper prop merging.

## Pattern Overview

For any React component that wraps a single DOM element or another component:

1. **Extend props**: Interface extends `React.ComponentProps<"element">` or `React.ComponentProps<typeof Component>`
2. **Spread first**: Pass all props via spread operator
3. **Override specific props**: Merge className with `cn()`, override event handlers or attributes
4. **No forwardRef needed**: Modern React handles refs automatically

---

## General Rules

### 1. Props Interface
Extend the underlying element's or component's props:

```typescript
// ✅ Good — DOM element wrapper
interface ButtonProps extends React.ComponentProps<"button"> {
  loading?: boolean;
}

// ✅ Good — Component wrapper
interface CardProps extends React.ComponentProps<typeof BaseCard> {
  variant?: "primary" | "secondary";
}

// ❌ Bad — Not extending underlying props
interface ButtonProps {
  loading?: boolean;
  onClick?: () => void;
  className?: string;
}
```

### 2. Spread Pattern
Spread all inherited props first, then override specific ones:

```typescript
// ✅ Good
function Button({ loading, className, disabled, ...props }: ButtonProps) {
  return (
    <button
      {...props}
      className={cn(className, "px-4 py-2")}
      disabled={disabled || loading}
    />
  );
}

// ❌ Bad — spread after overrides (props can override your customizations)
function Button({ loading, className, disabled, ...props }: ButtonProps) {
  return (
    <button
      className={cn(className, "px-4 py-2")}
      disabled={disabled || loading}
      {...props}
    />
  );
}
```

### 3. ClassName Merging
Always use `cn()` utility to merge classNames:

```typescript
// ✅ Good — merge with cn
<button className={cn(className, "custom-class")} />

// ❌ Bad — string concatenation
<button className={`${className} custom-class`} />

// ❌ Bad — ignoring inherited className
<button className="custom-class" />
```

### 4. No forwardRef Needed
Modern React (19+) automatically forwards refs to DOM elements without explicit `forwardRef`:

```typescript
// ✅ Good — no forwardRef needed
function Input({ className, ...props }: InputProps) {
  return (
    <input
      {...props}
      className={cn(className, "border rounded")}
    />
  );
}

// Usage — ref works automatically
const inputRef = React.useRef<HTMLInputElement>(null);
<Input ref={inputRef} />

// ❌ Bad — unnecessary forwardRef (outdated pattern)
const Input = React.forwardRef<HTMLInputElement, InputProps>(
  ({ className, ...props }, ref) => (
    <input ref={ref} {...props} className={cn(className, "border rounded")} />
  )
);
```

### 5. Custom Props (Optional)
Add custom props as needed; no need to destructure if not used:

```typescript
// ✅ Good — custom prop only if needed
interface ButtonProps extends React.ComponentProps<"button"> {
  size?: "sm" | "md" | "lg";
  variant?: "primary" | "secondary";
}

function Button({ variant, size, className, ...props }: ButtonProps) {
  return (
    <button
      {...props}
      className={cn(className, variantStyles[variant], sizeStyles[size])}
    />
  );
}

// ✅ Good — custom props not used (they stay in spread)
interface ButtonProps extends React.ComponentProps<"button"> {
  analytics?: { event: string };
}

function Button({ className, ...props }: ButtonProps) {
  // analytics prop is in props if needed, but not explicitly used
  return (
    <button
      {...props}
      className={cn(className, "px-4 py-2")}
    />
  );
}
```

### 6. Event Handler Overrides
Override event handlers as needed:

```typescript
// ✅ Good — override onClick if needed
interface ButtonProps extends React.ComponentProps<"button"> {
  onClickCustom?: () => void;
}

function Button({ className, onClickCustom, ...props }: ButtonProps) {
  return (
    <button
      {...props}
      className={cn(className, "px-4 py-2")}
      onClick={(e) => {
        props.onClick?.(e);
        onClickCustom?.();
      }}
    />
  );
}

// ✅ Good — let onClick pass through via spread
function Input({ className, ...props }: InputProps) {
  return (
    <input
      {...props}
      className={cn(className, "border rounded")}
    />
  );
}
```

---

## Examples

### Wrapper for DOM Element

```typescript
interface ButtonProps extends React.ComponentProps<"button"> {
  loading?: boolean;
  icon?: React.ReactNode;
}

export function Button({ 
  loading, 
  icon, 
  className, 
  disabled, 
  children,
  ...props 
}: ButtonProps) {
  return (
    <button
      {...props}
      className={cn(
        className,
        "inline-flex items-center gap-2 px-4 py-2 rounded",
        "bg-blue-500 text-white hover:bg-blue-600",
        "disabled:opacity-50 disabled:cursor-not-allowed"
      )}
      disabled={disabled || loading}
    >
      {loading && <Spinner />}
      {icon && <span>{icon}</span>}
      {children}
    </button>
  );
}
```

### Wrapper for Custom Component

```typescript
interface CardProps extends React.ComponentProps<typeof BaseCard> {
  highlighted?: boolean;
}

export function Card({ 
  highlighted, 
  className, 
  ...props 
}: CardProps) {
  return (
    <BaseCard
      {...props}
      className={cn(
        className,
        highlighted && "ring-2 ring-blue-500"
      )}
    />
  );
}
```

### Wrapper in React

```typescript
interface InputProps extends React.ComponentProps<"input"> {
  error?: string;
  label?: string;
}

export function Input({ 
  error, 
  label, 
  className, 
  id,
  ...props 
}: InputProps) {
  return (
    <div>
      {label && <label htmlFor={id}>{label}</label>}
      <input
        {...props}
        id={id}
        className={cn(
          className,
          "border rounded px-3 py-2",
          error && "border-red-500 bg-red-50"
        )}
      />
      {error && <span className="text-red-500 text-sm">{error}</span>}
    </div>
  );
}
```

---

## Anti-patterns (Don't do this)

```typescript
// ❌ Not extending underlying props
interface ButtonProps {
  onClick?: () => void;
  disabled?: boolean;
}

// ❌ Spreading after overrides (props can override)
<button className={cn(...)} {...props} disabled={disabled} />

// ❌ Manual className concatenation instead of cn()
<button className={`${className} custom-class`} />

// ❌ Ignoring inherited className
<button className="custom-class" />

// ❌ Unnecessary forwardRef (React 19+)
const Input = React.forwardRef<HTMLInputElement, InputProps>((...) => ...)

// ❌ Not spreading props (limits flexibility)
function Button({ loading, className }: ButtonProps) {
  return <button className={cn(className, "...")} />; // onClick, etc. lost
}

// ❌ Destructuring and not using custom props
interface ButtonProps extends React.ComponentProps<"button"> {
  analytics?: { event: string };
}
function Button({ analytics, ...props }: ButtonProps) { // unused analytics
  return <button {...props} />;
}
```

## Checklist

- [ ] Props interface extends `React.ComponentProps<"element">` or `React.ComponentProps<typeof Component>`
- [ ] All props spread first via `{...props}`
- [ ] Specific props (className, disabled, etc.) overridden after spread
- [ ] className merged with `cn()` utility
- [ ] No unnecessary `forwardRef` used (modern React handles refs)
- [ ] Event handlers can be overridden if needed
- [ ] Custom props are documented if added
- [ ] Component is flexible — consumers can pass any valid prop
