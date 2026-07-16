---
name: typescript-function-style
description: Enforce function declaration syntax for named functions and arrow functions for callbacks. Use function Name() {} for clarity, arrow () => {} for callbacks (map, filter, event handlers, promises, etc.).
---

# Function Style Guide

## Purpose
Enforce explicit, readable function patterns. Named functions use declaration syntax for clarity; callbacks use arrow functions for conciseness.

## General Rules

### 1. Named Functions — Use Function Declaration
For **all named functions** (whether exported, used elsewhere, or called directly), use the explicit declaration syntax. This applies to every named function, regardless of how it's used or whether it's reused:

```typescript
// ❌ Bad — arrow function for named function
const getUserName = (user: User): string => {
  return user.name;
};

// ❌ Bad — even if called in code, still wrong
const handleSelectLocale = (code: Locale) => {
  persistLocaleCookie(code);
  router.push(redirectedPathname(pathname ?? "/", code));
};

// ✅ Good — clear function declaration
function getUserName(user: User): string {
  return user.name;
}

// ✅ Good — named function, even if called elsewhere
function handleSelectLocale(code: Locale) {
  persistLocaleCookie(code);
  router.push(redirectedPathname(pathname ?? "/", code));
}
```

### 2. Callbacks — Use Arrow Functions
For any function passed as an argument (callbacks), use arrow functions:

```typescript
// ❌ Bad — named function as callback
array.map(function(item) {
  return item.id;
});

// ✅ Good — arrow function callback
array.map((item) => item.id);

// ✅ Good — multiline arrow function callback
array.forEach((item) => {
  processItem(item);
});
```

### 3. Event Handlers (React/DOM)
Event handlers passed to components or elements use arrow functions:

```typescript
// ❌ Bad
function handleClick() {
  doSomething();
}
<button onClick={handleClick}>Click</button>

// ✅ Good — define function separately for clarity
function handleClick() {
  doSomething();
}
export const Component = () => {
  return <button onClick={handleClick}>Click</button>;
};

// ✅ Good — inline arrow callback
<button onClick={() => doSomething()}>Click</button>
```

### 4. Promise Callbacks
Promise chains and `.then()/.catch()` use arrow functions:

```typescript
// ❌ Bad
fetch(url).then(function(response) {
  return response.json();
});

// ✅ Good
fetch(url)
  .then((response) => response.json())
  .catch((error) => console.error(error));
```

### 5. Async Functions
Async functions use the declaration syntax:

```typescript
// ❌ Bad
const fetchData = async () => {
  // ...
};

// ✅ Good
async function fetchData() {
  // ...
}

// ✅ Good for callbacks (still arrow)
promise.then(async (data) => {
  return await process(data);
});
```

### 6. setTimeout/setInterval
Use arrow functions for timer callbacks:

```typescript
// ❌ Bad
setTimeout(function() {
  doSomething();
}, 1000);

// ✅ Good
setTimeout(() => {
  doSomething();
}, 1000);
```

### 7. IIFE (Immediately Invoked Function Expression)
Use function declaration syntax for IIFE:

```typescript
// ❌ Bad
(() => {
  // ...
})();

// ✅ Good
(function() {
  // ...
})();
```

### 8. Type Annotations on Arrows
Arrow functions with type annotations are acceptable (typically for callbacks):

```typescript
// ✅ Good — type annotated arrow callback
const formatter: (value: number) => string = (value) => value.toFixed(2);

// ✅ Good — array callback with type
const ids: number[] = users.map((user: User): number => user.id);
```

### 9. Array Methods
All array methods (map, filter, reduce, forEach, some, etc.) use arrow functions:

```typescript
// ❌ Bad
items.filter(function(item) {
  return item.active;
});

items.reduce(function(acc, item) {
  return acc + item.value;
}, 0);

// ✅ Good
items.filter((item) => item.active);

items.reduce((acc, item) => acc + item.value, 0);
```

---

## Examples

### TypeScript

```typescript
// ✅ Named function (declaration)
function calculateTotal(items: Item[]): number {
  return items.reduce((sum, item) => sum + item.price, 0);
}

// ✅ Event handler callback
const items = data.filter((item) => item.isActive);

// ✅ Promise callback
async function loadUser(id: string) {
  return fetch(`/api/users/${id}`)
    .then((res) => res.json())
    .then((user) => formatUser(user))
    .catch((err) => console.error(err));
}

// ✅ Timer callback
setTimeout(() => {
  refreshData();
}, 5000);

// ✅ IIFE
(function() {
  const privateVar = "hidden";
  // ...
})();
```

### React

```typescript
interface ButtonProps {
  onClick: () => void;
}

export function Button({ onClick }: ButtonProps) {
  function handleClick() {
    onClick();
  }

  return (
    <button onClick={handleClick}>
      Click me
    </button>
  );
}

// ✅ Callbacks in hooks
export function Component() {
  React.useEffect(() => {
    const subscription = dataSource.subscribe((data) => {
      setData(data);
    });
    return () => subscription.unsubscribe();
  }, []);

  return (
    <div>
      {items.map((item) => (
        <Item key={item.id} data={item} />
      ))}
    </div>
  );
}
```

---

## Anti-patterns (Don't do this)

```typescript
// ❌ Arrow function for named function
const getUserName = (user: User) => user.name;

// ❌ Function declaration as callback
array.map(function(item) {
  return item.id;
});

// ❌ Named function for Promise callback
fetch(url).then(function(response) {
  return response.json();
});

// ❌ Arrow function for IIFE
(() => {
  // ...
})();

// ❌ Named function for array method
items.filter(function(item) {
  return item.active;
});
```

## Checklist

- [ ] Named functions use `function Name() {}` syntax
- [ ] All callbacks (Array methods, event handlers, promises) use arrow functions
- [ ] Async functions use declaration syntax (`async function Name() {}`)
- [ ] IIFE uses function declaration `(function() {})()`
- [ ] setTimeout/setInterval use arrow function callbacks
- [ ] Promise callbacks (.then/.catch) use arrow functions
- [ ] React event handlers and callbacks use arrow functions
