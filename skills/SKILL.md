---
name: dev-skills
description: Personal Claude skills library for consistent JavaScript/TypeScript and React code style. Enforces explicit patterns for readability and maintainability across projects.
---

# Development Skills Library

Complete set of coding standards and guidelines for JavaScript, TypeScript, React, and Tailwind CSS development.

## TypeScript Rules

### 1. typescript-style
**Kebab-case files/dirs, framework conventions**

Enforce consistent file and directory naming conventions. Files and folders use kebab-case. Respect framework-specific conventions (Next.js, TanStack Start, Remix, React Router).

📖 Full rules: [`rules/typescript-style.md`](rules/typescript-style.md)

---

### 2. typescript-ternary
**Explicit ternaries, no short-circuit `&&`**

Enforce explicit ternary patterns. Never use `&&` operator for conditional logic—always use `condition ? value : null`. Makes intent clear and prevents bugs.

📖 Full rules: [`rules/typescript-ternary.md`](rules/typescript-ternary.md)

---

### 3. typescript-function-style
**Function declarations vs arrows**

Named functions use declaration syntax `function Name() {}`. Callbacks (array methods, promises, event handlers) use arrow functions `() => {}`. The distinction is type-based, not location-based.

📖 Full rules: [`rules/typescript-function-style.md`](rules/typescript-function-style.md)

---

### 4. typescript-interface-preference
**Prefer `interface` over `type`**

Use `interface` for object shapes. Reserve `type` for unions, tuples, primitives, and utility types. Applies project-wide to all type/interface declarations.

📖 Full rules: [`rules/typescript-interface-preference.md`](rules/typescript-interface-preference.md)

---

## React Rules

### 5. react-style
**React imports via `React.XXX`**

Import React and use `React.ReactNode`, `React.ComponentProps`, etc. This pattern is explicit and avoids confusion with destructured imports.

📖 Full rules: [`rules/react-style.md`](rules/react-style.md)

---

### 6. react-component-wrapper
**Props extending, `cn()` for className**

Extend React component props with `React.ComponentProps<"element">`. Use `cn()` utility for dynamic class merging. Never concatenate classes with template literals.

📖 Full rules: [`rules/react-component-wrapper.md`](rules/react-component-wrapper.md)

---

### 7. react-no-render-functions
**Extract components, no render functions**

Never pass render functions to components. Extract them as proper components instead. Keeps component tree stable and improves performance.

📖 Full rules: [`rules/react-no-render-functions.md`](rules/react-no-render-functions.md)

---

## Styling Rules

### 8. tailwind-class-merging
**Dynamic classes with `cn()`, CVA for variants**

Use `cn()` utility only for dynamic class manipulation. Static classes don't need `cn()`. Use `class-variance-authority` (CVA) for component variants.

📖 Full rules: [`rules/tailwind-class-merging.md`](rules/tailwind-class-merging.md)

---

## General Rules

### 9. code-simplicity
**Readability first, simple and clear code**

Prioritize human readability. Simple and clear code beats clever optimizations. Follow established patterns. Think about the next developer reading your code.

📖 Full rules: [`rules/code-simplicity.md`](rules/code-simplicity.md)

---

## Installation & Usage

This library is designed to be used with Claude Code as a source of truth for code style decisions.

### For Local Development
```bash
git clone https://github.com/jorisre/dev-skills.git
cd dev-skills
```

### For Integration with Claude Projects
Reference the skills library in your project to enforce these rules during development.

---

## Structure

```
skills/
├── SKILL.md (this file — index of all rules)
└── rules/
    ├── typescript-style.md
    ├── typescript-ternary.md
    ├── typescript-function-style.md
    ├── typescript-interface-preference.md
    ├── react-style.md
    ├── react-component-wrapper.md
    ├── react-no-render-functions.md
    ├── tailwind-class-merging.md
    └── code-simplicity.md
```

---

## Quick Reference by Category

**File & Naming Conventions**
- typescript-style

**Conditional Logic**
- typescript-ternary

**Function Patterns**
- typescript-function-style

**Type Declarations**
- typescript-interface-preference

**React Component Patterns**
- react-style
- react-component-wrapper
- react-no-render-functions

**CSS & Styling**
- tailwind-class-merging

**Code Quality**
- code-simplicity

---

## License

MIT
