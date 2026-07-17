---
name: typescript-style
description: Enforce kebab-case file naming for utils, lib, i18n, components, hooks directories while respecting framework-specific magic files and conventions. Triggers when creating/renaming files or reviewing file structure.
---

# TypeScript Style Guide

## Purpose
Enforce consistent file naming conventions across TypeScript/JavaScript projects. All non-framework-specific files use kebab-case (dash-case), while respecting framework magic files and routing conventions.

## General Rules

### 1. File Naming — Kebab-Case
All source files must use kebab-case (lowercase with dashes):
- ✅ `my-helper.ts`
- ✅ `my-component.tsx`
- ✅ `use-fetch-data.ts`
- ❌ `myHelper.ts`
- ❌ `MyComponent.tsx`

### 2. Directories — Kebab-Case
All directories follow kebab-case except where framework conventions override:
- ✅ `utils/`, `lib/`, `i18n/`, `components/`, `hooks/`
- ✅ `components/my-button/`
- ✅ `hooks/use-scroll-lock/`
- ❌ `components/MyButton/`
- ❌ `hooks/useScrollLock/`

### 3. Type Files
Type definition files use kebab-case:
- ✅ `my-component.types.ts`
- ✅ `api-client.d.ts`
- ❌ `MyComponent.types.ts`

### 4. Index Files
Standard `index.ts` exports remain as-is (already lowercase):
```typescript
// components/my-button/index.ts
export * from "./my-button";
```

### 5. Configuration Files
Framework/tool configuration files follow their standard conventions:
- ✅ `jest.config.ts`, `vite.config.ts`, `tsconfig.json`
- ✅ `package.json`, `.eslintrc.json`

---

## Framework-Specific Conventions

### Next.js (App Router)

**Magic files — Keep exact names:**
- `page.tsx`, `layout.tsx`, `error.tsx`, `loading.tsx`
- `not-found.tsx`, `middleware.ts`, `route.ts` (API route)
- `opengraph-image.tsx`, `icon.tsx`

**Route directories:**
```
app/
├── dashboard/
│   ├── page.tsx (not dashboard.tsx)
│   ├── layout.tsx
│   └── components/
│       ├── stats-panel/
│       │   └── stats-panel.tsx (kebab-case)
│       └── index.ts
├── api/
│   └── users/
│       ├── route.ts (magic file)
│       └── helpers/
│           └── validate-user.ts (kebab-case)
```

**Components directory** (if separate):
```
components/
├── my-button/
│   ├── my-button.tsx
│   ├── my-button.types.ts
│   └── index.ts
└── layout/
    └── header/
        └── header.tsx
```

### TanStack Start

**Magic files — Keep exact names:**
- `+page.tsx`, `+layout.tsx`, `+error.tsx`, `+server.ts`

**Route directories:**
```
routes/
├── __root.tsx (framework convention)
├── index.tsx (root route)
├── dashboard/
│   ├── +page.tsx
│   └── components/
│       └── dashboard-card/
│           └── dashboard-card.tsx
└── settings/
    └── privacy.tsx
```

### Remix

**Route file patterns:**
```
routes/
├── _index.tsx (root)
├── dashboard._index.tsx
├── dashboard.settings.tsx
├── components/
│   └── my-component/
│       └── my-component.tsx
└── utils/
    └── api-client.ts
```

Optional route segmentation directory:
```
routes/
├── __auth/
│   ├── login.tsx
│   └── register.tsx
├── __app/
│   └── dashboard.tsx
└── components/
    └── my-component/
        └── my-component.tsx
```

### React Router (No File-Based Routing)

All files follow kebab-case; organize as needed:
```
src/
├── routes/
│   └── dashboard/
│       └── dashboard.tsx (kebab-case)
├── components/
│   └── my-button/
│       ├── my-button.tsx
│       └── my-button.types.ts
├── hooks/
│   └── use-fetch-data.ts
└── utils/
    └── api-client.ts
```

---

## Example Project Structure

```
src/
├── components/
│   ├── my-button/
│   │   ├── my-button.tsx
│   │   ├── my-button.types.ts
│   │   └── index.ts
│   ├── layout/
│   │   └── header/
│   │       └── header.tsx
├── hooks/
│   ├── use-fetch-data.ts
│   ├── use-scroll-lock.ts
│   └── index.ts
├── utils/
│   ├── api-client.ts
│   ├── format-date.ts
│   └── index.ts
├── lib/
│   ├── db-connection.ts
│   └── cache.ts
├── i18n/
│   └── translations.ts
└── types/
    └── common.types.ts
```

## Anti-patterns (Don't do this)

```typescript
// ❌ PascalCase files
components/MyButton.tsx
hooks/UseFetchData.ts

// ❌ camelCase files
components/myButton.tsx
hooks/useFetchData.ts

// ❌ Inconsistent naming across directories
components/my-button/
hooks/UseFetchData.ts
utils/apiClient.ts

// ❌ Ignoring framework magic files (Next.js)
app/dashboard/dashboard-page.tsx (should be page.tsx)
app/dashboard/my-layout.tsx (should be layout.tsx)
```

## Checklist

- [ ] All custom files in `components/`, `hooks/`, `utils/`, `lib/`, `i18n/` use kebab-case
- [ ] Subdirectories use kebab-case
- [ ] Framework magic files (Next.js `page.tsx`, TanStack Start `+page.tsx`, etc.) kept as-is
- [ ] Type files (`.types.ts`, `.d.ts`) use kebab-case
- [ ] `index.ts` remains unchanged
- [ ] Configuration files follow their standard conventions
