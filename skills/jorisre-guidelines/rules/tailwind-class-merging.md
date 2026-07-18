---
name: tailwind-class-merging
description: Use cn() utility ONLY for dynamic Tailwind classes. Static Tailwind classes don't need cn(). Use object syntax for conditions. Leverage class-variance-authority for component variants.
---

# Tailwind Class Merging

## Purpose
Enforce consistent class management patterns. Use the `cn()` utility function **only** when merging dynamic Tailwind classes or handling conditional Tailwind styles, avoiding unnecessary runtime overhead for static classes.

## 🔴 CRITICAL RULE — Only Use cn() for Dynamic Tailwind Classes

`cn()` should ONLY be used when:
1. **You have Tailwind classes** in your className
2. **AND those classes are dynamic** (added/removed/merged at runtime)

If your classes are static (hardcoded), don't wrap them in `cn()`. If your classes are non-Tailwind custom classes, don't wrap them in `cn()` either.

---

## General Rules

### 1. Static Classes — No cn() Needed
For static (non-dynamic) classes, use them directly. No `cn()` wrapper needed:

```typescript
// ✅ Good — static Tailwind classes only
<div className="flex items-center gap-2 px-4 py-2">
  {children}
</div>

// ✅ Good — static custom classes only
<div className="my-custom-class another-class">
  {children}
</div>

// ❌ Bad — unnecessary cn() wrapper for static classes
<div className={cn("flex items-center gap-2 px-4 py-2")}>
  {children}
</div>
```

### 2. Dynamic Tailwind Classes — Use cn()
Use `cn()` **ONLY** when merging dynamic Tailwind classes with other classes:

```typescript
// ✅ Good — dynamic Tailwind classes from props
<div className={cn("base-tailwind-class", className)}>
  {children}
</div>

// ✅ Good — dynamic Tailwind classes from variables
<button className={cn("px-4 py-2", variantClasses[variant])}>
  Click
</button>

// ❌ Bad — don't use cn() if all classes are static
<button className={cn("px-4 py-2 bg-blue-600")}>
  Click
</button>

// ❌ Bad — don't use cn() for non-Tailwind classes
<div className={cn(customClass)}>
  {children}
</div>
```

### 3. Conditional Tailwind Classes — Use Object Syntax
When you need dynamic conditional Tailwind classes, use object syntax in `cn()` — more readable than ternaries:

```typescript
// ✅ Good — object syntax for dynamic Tailwind conditions
<button
  className={cn(
    "px-4 py-2 bg-blue-600",
    { "opacity-50": disabled, "cursor-not-allowed": disabled }
  )}
>
  Click
</button>

// ✅ Good — static base, dynamic conditions
<button
  className={cn(
    "base-button",
    disabled && { "opacity-50": true, "cursor-not-allowed": true }
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

// ❌ Bad — wrapping static classes in cn()
<button className={cn("base-button px-4 py-2")}>
  Click
</button>
```

### 4. Multiple Dynamic Tailwind Conditions
Combine multiple dynamic Tailwind conditions in a single object:

```typescript
// ✅ Good — multiple dynamic Tailwind conditions
<div
  className={cn(
    "base-tailwind-class",
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

// ❌ Bad — using cn() for static classes
<div className={cn("text-black bg-white p-4")}>
  {content}
</div>
```

### 5. Variants — Use class-variance-authority (CVA)
For component variants with dynamic Tailwind classes, use `class-variance-authority` (CVA) instead of manual conditional logic:

```typescript
// ✅ Good — CVA for dynamic variant Tailwind classes
import { cva } from "class-variance-authority";
import { cn } from "@/lib/cn";

const buttonVariants = cva("px-4 py-2 rounded transition", {
  variants: {
    variant: {
      primary: "bg-blue-600 text-white hover:bg-blue-700",
      secondary: "bg-gray-200 text-gray-900 hover:bg-gray-300",
      danger: "bg-red-600 text-white hover:bg-red-700",
    },
    size: {
      sm: "text-sm",
      md: "text-base",
      lg: "text-lg",
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
      // ✅ Use cn() here to merge CVA output with custom className prop
      className={cn(buttonVariants({ variant, size }), className)}
      {...props}
    />
  );
}
```

### 6. Merging Dynamic className Props with Tailwind
When accepting a `className` prop and merging it with base Tailwind classes:

```typescript
// ✅ Good — merge prop className with Tailwind (dynamic merge)
<div className={cn("px-4 py-2 border", className)}>
  {children}
</div>

// ✅ Good — merging dynamic variant Tailwind classes
<button className={cn("px-4 py-2", variantTailwindClasses[variant], className)}>
  Click
</button>

// ❌ Bad — using cn() when no dynamic Tailwind classes
<div className={cn("custom-class")}>
  {children}
</div>

// ❌ Bad — unnecessary cn() for static classes
<div className={cn("px-4 py-2 border rounded")}>
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

### Button Component with Dynamic State

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
      // ✅ Use cn() because we have dynamic Tailwind conditions (loading, disabled)
      className={cn(
        "inline-flex items-center gap-2 px-4 py-2 rounded bg-blue-600 text-white hover:bg-blue-700",
        {
          "opacity-50": loading || disabled,
          "cursor-not-allowed": loading || disabled,
        },
        className  // ✅ Merge className prop
      )}
      disabled={loading || disabled}
      {...props}
    />
  );
}
```

### Input Component with Dynamic Error State

```typescript
import { cn } from "@/lib/cn";

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
        // ✅ Use cn() because we have dynamic Tailwind conditions (error state)
        className={cn(
          "w-full px-3 py-2 border rounded focus:outline-none focus:ring-2",
          {
            "border-red-500 focus:ring-red-500": error,
            "border-gray-300 focus:ring-blue-500": !error,
          },
          className  // ✅ Merge className prop
        )}
        {...props}
      />
      {error && <span className="text-red-500 text-sm mt-1">{error}</span>}
    </div>
  );
}
```

### Card with Dynamic Variants (CVA)

```typescript
import { cva } from "class-variance-authority";
import { cn } from "@/lib/cn";

const cardVariants = cva(
  "rounded-lg p-4",  // Static base Tailwind classes
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
      // ✅ Use cn() to merge CVA output (dynamic Tailwind) with className prop
      className={cn(cardVariants({ variant }), className)}
      {...props}
    />
  );
}
```

---

## Anti-patterns (Don't do this)

```typescript
// ❌ CRITICAL: Unnecessary cn() for STATIC Tailwind classes
<div className={cn("flex items-center gap-2 px-4 py-2")}>
  {children}
</div>
// ✅ Correct:
<div className="flex items-center gap-2 px-4 py-2">
  {children}
</div>

// ❌ CRITICAL: Using cn() for non-Tailwind classes
<div className={cn("custom-class another-custom")}>
  {children}
</div>
// ✅ Correct:
<div className="custom-class another-custom">
  {children}
</div>

// ❌ Ternary instead of object syntax (when cn() is used)
<button className={cn(
  "px-4 py-2",
  disabled ? "opacity-50" : "opacity-100"
)}>
  Click
</button>
// ✅ Correct (if you need dynamic Tailwind):
<button className={cn(
  "px-4 py-2",
  { "opacity-50": disabled, "opacity-100": !disabled }
)}>
  Click
</button>

// ❌ && operator instead of object syntax
<button className={cn("button", disabled && "opacity-50")}>
  Click
</button>
// ✅ Correct:
<button className={cn("button", { "opacity-50": disabled })}>
  Click
</button>

// ❌ Manual variant switching instead of CVA (when you have many variants)
<button className={cn(
  "button",
  variant === "primary" && "bg-blue-600",
  variant === "secondary" && "bg-gray-200",
  variant === "danger" && "bg-red-600",
)}>
  Click
</button>
// ✅ Correct (use CVA for variants):
const buttonVariants = cva("button", {
  variants: {
    variant: {
      primary: "bg-blue-600",
      secondary: "bg-gray-200",
      danger: "bg-red-600",
    },
  },
});
<button className={buttonVariants({ variant })}>
  Click
</button>

// ❌ String concatenation instead of cn()
<div className={`${className} extra-tailwind-class`}>
  {children}
</div>
// ✅ Correct (when merging dynamic className with Tailwind):
<div className={cn("base-tailwind-class", className)}>
  {children}
</div>
```

## Checklist

- [ ] **Static Tailwind classes do NOT use `cn()`** — use className directly
- [ ] **Non-Tailwind classes do NOT use `cn()`** — use className directly  
- [ ] **Only use `cn()` for dynamic Tailwind classes** — classes added/removed/merged at runtime
- [ ] **Conditional Tailwind classes use object syntax** in `cn()` (not ternaries or `&&`)
- [ ] **No `&&` operator in `cn()` calls** — use object syntax `{ "class": condition }`
- [ ] **No ternaries in `cn()` calls** — use object syntax `{ "class1": condition, "class2": !condition }`
- [ ] **Component variants use `class-variance-authority` (CVA)** — not manual `cn()` switching
- [ ] **Custom `cn()` utility is imported** from correct location
- [ ] **Merge `className` prop with base Tailwind** only when the base uses dynamic Tailwind classes
