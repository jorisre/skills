---
name: tailwind-class-merging
description: Use cn() utility for dynamic class manipulation. Static classes do not need cn(). Use object syntax for conditions. Leverage class-variance-authority for component variants.
---

# Tailwind Class Merging

## Purpose
Enforce consistent class management patterns. Use the `cn()` utility function for dynamic class manipulation and composition, avoiding unnecessary runtime overhead for static classes.

## General Rules

### 1. Static Classes — No cn() Needed
For static (non-dynamic) classes, use them directly. No runtime wrapper needed:

```typescript
// ✅ Good — static classes only
<div className="flex items-center gap-2 px-4 py-2">
  {children}
</div>

// ❌ Bad — unnecessary cn() wrapper
<div className={cn("flex items-center gap-2 px-4 py-2")}>
  {children}
</div>
```

### 2. Dynamic Classes — Use cn()
For any dynamic class manipulation (merging, conditional classes), use the `cn()` utility:

```typescript
// ✅ Good — dynamic classes
<div className={cn(className, "base-class")}>
  {children}
</div>

// ✅ Good — dynamic classes from variables
<button className={cn(buttonBaseClasses, variantClasses[variant])}>
  Click
</button>
```

### 3. Conditional Classes — Use Object Syntax
Use object syntax in `cn()` for conditional classes — more readable than ternaries:

```typescript
// ✅ Good — object syntax for conditions
<button
  className={cn(
    "base-button",
    { "opacity-50": disabled, "cursor-not-allowed": disabled }
  )}
>
  Click
</button>

// ❌ Bad — ternary in cn()
<button className={cn("base-button", disabled ? "opacity-50" : "")}>
  Click
</button>

// ❌ Bad — && operator (implicit falsy)
<button className={cn("base-button", disabled && "opacity-50")}>
  Click
</button>
```

### 4. Multiple Conditions
Combine multiple conditional classes in a single object:

```typescript
// ✅ Good — multiple conditions
<div
  className={cn(
    "base-class",
    {
      "text-red-500": error,
      "border-red-500": error,
      "bg-red-50": error,
      "cursor-not-allowed": disabled,
      "opacity-50": disabled,
    }
  )}
>
  {content}
</div>
```

### 5. Variants — Use class-variance-authority
For component variants, use `class-variance-authority` (CVA) instead of manual cn() logic:

```typescript
// ✅ Good — CVA for variants
import { cva } from "class-variance-authority";

const buttonVariants = cva("base-button", {
  variants: {
    variant: {
      primary: "bg-blue-600 text-white hover:bg-blue-700",
      secondary: "bg-gray-200 text-gray-900 hover:bg-gray-300",
      danger: "bg-red-600 text-white hover:bg-red-700",
    },
    size: {
      sm: "px-2 py-1 text-sm",
      md: "px-4 py-2 text-base",
      lg: "px-6 py-3 text-lg",
    },
  },
  defaultVariants: {
    variant: "primary",
    size: "md",
  },
});

interface ButtonProps extends React.ComponentProps<"button"> {
  variant?: "primary" | "secondary" | "danger";
  size?: "sm" | "md" | "lg";
}

function Button({ variant, size, className, ...props }: ButtonProps) {
  return (
    <button
      className={cn(buttonVariants({ variant, size }), className)}
      {...props}
    />
  );
}
```

### 6. Custom Classes with Tailwind
When combining custom/dynamic classes with Tailwind:

```typescript
// ✅ Good — merge custom and Tailwind
<div className={cn(customClassName, "tailwind-base-class")}>
  {children}
</div>

// ✅ Good — custom classes rarely used, but valid
<div className={cn(className, rareCustomClass, "tailwind-class")}>
  {children}
</div>
```

### 7. cn() Utility Location
The `cn()` utility is typically a custom function in the project. Find it in `lib/` or `utils/`:

```typescript
// Example location: lib/cn.ts or utils/class-names.ts
export function cn(...classes: (string | undefined | false | Record<string, boolean>)[]): string {
  // Implementation using clsx or classnames
}

// Usage
import { cn } from "@/lib/cn";
```

---

## Examples

### Button Component

```typescript
import { cn } from "@/lib/cn";

interface ButtonProps extends React.ComponentProps<"button"> {
  loading?: boolean;
  disabled?: boolean;
}

export function Button({ 
  loading, 
  disabled, 
  className, 
  ...props 
}: ButtonProps) {
  return (
    <button
      className={cn(
        "inline-flex items-center gap-2 px-4 py-2 rounded",
        "bg-blue-600 text-white hover:bg-blue-700",
        {
          "opacity-50": loading || disabled,
          "cursor-not-allowed": loading || disabled,
        }
      )}
      disabled={loading || disabled}
      {...props}
    />
  );
}
```

### Input Component with Validation

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
      {label && (
        <label htmlFor={id} className="block text-sm font-medium mb-1">
          {label}
        </label>
      )}
      <input
        id={id}
        className={cn(
          "w-full px-3 py-2 border rounded",
          "focus:outline-none focus:ring-2 focus:ring-blue-500",
          {
            "border-red-500 focus:ring-red-500": error,
            "border-gray-300": !error,
          },
          className
        )}
        {...props}
      />
      {error && <span className="text-red-500 text-sm mt-1">{error}</span>}
    </div>
  );
}
```

### Card with Variants (CVA)

```typescript
import { cva } from "class-variance-authority";
import { cn } from "@/lib/cn";

const cardVariants = cva(
  "rounded-lg p-4",
  {
    variants: {
      variant: {
        default: "bg-white border border-gray-200",
        elevated: "bg-white shadow-lg",
        subtle: "bg-gray-50",
      },
    },
    defaultVariants: {
      variant: "default",
    },
  }
);

interface CardProps extends React.ComponentProps<"div"> {
  variant?: "default" | "elevated" | "subtle";
}

export function Card({ variant, className, ...props }: CardProps) {
  return (
    <div
      className={cn(cardVariants({ variant }), className)}
      {...props}
    />
  );
}
```

---

## Anti-patterns (Don't do this)

```typescript
// ❌ Unnecessary cn() for static classes
<div className={cn("flex items-center")}>
  {children}
</div>

// ❌ Ternary instead of object syntax
<button className={cn(disabled ? "opacity-50" : "opacity-100")}>
  Click
</button>

// ❌ && operator in cn()
<button className={cn("button", disabled && "opacity-50")}>
  Click
</button>

// ❌ Manual variant switching instead of CVA
<button className={cn(
  "button",
  variant === "primary" && "bg-blue-600",
  variant === "secondary" && "bg-gray-200",
  variant === "danger" && "bg-red-600",
)}>
  Click
</button>

// ❌ String concatenation instead of cn()
<div className={`${className} extra-class`}>
  {children}
</div>

// ❌ Ignoring inherited className
<div className="base-class">
  {children}
</div>
```

## Checklist

- [ ] Static classes do NOT use `cn()`
- [ ] Dynamic classes use `cn()`
- [ ] Conditional classes use object syntax in `cn()`
- [ ] No `&&` operator in `cn()` calls
- [ ] No ternaries in `cn()` (use object syntax instead)
- [ ] Component variants use `class-variance-authority`
- [ ] Custom `cn()` utility is imported from correct location
- [ ] Inherited `className` prop is always merged with `cn()`
