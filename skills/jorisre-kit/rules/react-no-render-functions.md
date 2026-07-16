---
name: react-no-render-functions
description: Avoid render functions and JSX stored in variables. Extract proper React components instead. Components are easier to test, reuse, optimize, and handle re-renders correctly with memoization.
---

# React — No Render Functions

## Purpose
Enforce proper React component extraction. Render functions and JSX-in-variables prevent memoization, complicate testing, and limit reusability. Extract components instead to leverage React's optimization capabilities and improve code clarity.

---

## General Rules

### 1. No Render Functions
Never use function variables that return JSX:

```typescript
// ❌ Bad — render function
const renderUserCard = (user: User) => (
  <div className="card">
    <h2>{user.name}</h2>
    <p>{user.email}</p>
  </div>
);

// ✅ Good — proper component
interface UserCardProps {
  user: User;
}

function UserCard({ user }: UserCardProps) {
  return (
    <div className="card">
      <h2>{user.name}</h2>
      <p>{user.email}</p>
    </div>
  );
}
```

### 2. No JSX Variables
Never store JSX in const variables:

```typescript
// ❌ Bad — JSX in a variable
const button = (
  <button onClick={handleClick} className="primary">
    Click me
  </button>
);

// ❌ Bad — JSX template
const errorMessage = <div className="error">Something went wrong</div>;

// ✅ Good — proper component (even if simple)
function PrimaryButton() {
  return (
    <button onClick={handleClick} className="primary">
      Click me
    </button>
  );
}

function ErrorMessage() {
  return <div className="error">Something went wrong</div>;
}
```

### 3. Extract Even Simple Components
If it's JSX, extract it as a component, even if simple:

```typescript
// ❌ Bad — JSX in variable (even if small)
const emptyState = <div className="text-gray-500">No items</div>;

// ✅ Good — component (reusable, testable, memoizable)
function EmptyState() {
  return <div className="text-gray-500">No items</div>;
}
```

### 4. Array Maps — Inline is OK
Maps with JSX are acceptable (it's the pattern):

```typescript
// ✅ Good — inline map is standard React pattern
return (
  <ul>
    {items.map((item) => (
      <li key={item.id}>
        <Item item={item} />
      </li>
    ))}
  </ul>
);

// ✅ Also fine — extract if logic becomes complex
return (
  <ul>
    {items.map((item) => (
      <ItemListRow key={item.id} item={item} />
    ))}
  </ul>
);
```

### 5. Why Extract: Memoization & Re-renders
Extracted components can be memoized and don't re-create on parent render:

```typescript
// ❌ Bad — render function re-creates on every parent render
function Dashboard({ user }: DashboardProps) {
  const renderUserCard = () => (
    <UserCard user={user} />
  );

  return (
    <div>
      {renderUserCard()}
      {/* Parent re-renders → renderUserCard re-creates → UserCard always re-renders */}
    </div>
  );
}

// ✅ Good — component instance is stable
function Dashboard({ user }: DashboardProps) {
  return (
    <div>
      <UserCard user={user} />
      {/* Parent re-renders → UserCard reference stable → can be memoized */}
    </div>
  );
}

// ✅ Even better — memoized for extra performance if expensive
const UserCard = React.memo(function UserCard({ user }: UserCardProps) {
  return (
    <div className="card">
      <h2>{user.name}</h2>
      <p>{user.email}</p>
    </div>
  );
});
```

### 6. Conditional Rendering — Extract Components
Extract components for conditional branches:

```typescript
// ❌ Bad — render function for conditions
const renderContent = () => {
  if (isLoading) return <Spinner />;
  if (error) return <ErrorMessage error={error} />;
  return <Content data={data} />;
};

// ✅ Good — proper component structure
interface PageProps {
  isLoading: boolean;
  error?: Error;
  data?: Data;
}

function Page({ isLoading, error, data }: PageProps) {
  if (isLoading) return <Spinner />;
  if (error) return <ErrorMessage error={error} />;
  if (!data) return <EmptyState />;
  return <Content data={data} />;
}
```

### 7. Props Passing — Extract with Props
When you need to customize, use component props:

```typescript
// ❌ Bad — render function with arguments
const renderCard = (title: string, content: React.ReactNode) => (
  <div className="card">
    <h2>{title}</h2>
    <div>{content}</div>
  </div>
);

// ✅ Good — component with props
interface CardProps {
  title: string;
  children: React.ReactNode;
}

function Card({ title, children }: CardProps) {
  return (
    <div className="card">
      <h2>{title}</h2>
      <div>{children}</div>
    </div>
  );
}
```

### 8. Higher-Order Component Pattern (Advanced)
If you truly need dynamic rendering logic, use HOCs (but prefer composition):

```typescript
// ⚠️ Acceptable if unavoidable
function withLoading<T extends object>(
  Component: React.ComponentType<T>
) {
  return function WithLoadingComponent({
    isLoading,
    ...props
  }: T & { isLoading: boolean }) {
    if (isLoading) return <Spinner />;
    return <Component {...(props as T)} />;
  };
}

// Usage
const LoadingCard = withLoading(Card);
```

---

## Examples

### ❌ Bad Patterns → ✅ Good Patterns

```typescript
// ❌ Bad
function UserList({ users }: UserListProps) {
  const renderUserItem = (user: User) => (
    <div key={user.id}>
      <h3>{user.name}</h3>
      <p>{user.email}</p>
    </div>
  );

  return (
    <div>
      <h2>Users</h2>
      {users.map(renderUserItem)}
    </div>
  );
}

// ✅ Good
interface UserItemProps {
  user: User;
}

function UserItem({ user }: UserItemProps) {
  return (
    <div>
      <h3>{user.name}</h3>
      <p>{user.email}</p>
    </div>
  );
}

function UserList({ users }: UserListProps) {
  return (
    <div>
      <h2>Users</h2>
      {users.map((user) => (
        <UserItem key={user.id} user={user} />
      ))}
    </div>
  );
}
```

### Conditional Rendering

```typescript
// ❌ Bad
function DataDisplay({ isLoading, error, data }: DataDisplayProps) {
  const renderContent = () => {
    if (isLoading) return <Spinner />;
    if (error) return <ErrorAlert error={error} />;
    if (!data) return <EmptyState />;
    return <DataTable data={data} />;
  };

  return <div>{renderContent()}</div>;
}

// ✅ Good
function DataDisplay({ isLoading, error, data }: DataDisplayProps) {
  if (isLoading) return <Spinner />;
  if (error) return <ErrorAlert error={error} />;
  if (!data) return <EmptyState />;
  return <DataTable data={data} />;
}
```

### Complex Content — Extract

```typescript
// ❌ Bad — JSX in variable
function Dashboard() {
  const headerContent = (
    <div className="header">
      <h1>Dashboard</h1>
      <p>Welcome back</p>
    </div>
  );

  return <div>{headerContent}</div>;
}

// ✅ Good — extracted component
function DashboardHeader() {
  return (
    <div className="header">
      <h1>Dashboard</h1>
      <p>Welcome back</p>
    </div>
  );
}

function Dashboard() {
  return (
    <div>
      <DashboardHeader />
    </div>
  );
}
```

### With Memoization

```typescript
// ✅ Good — component can be memoized
interface ExpensiveCardProps {
  data: LargeData;
  onAction: () => void;
}

const ExpensiveCard = React.memo(function ExpensiveCard({
  data,
  onAction,
}: ExpensiveCardProps) {
  // Expensive rendering logic
  return (
    <div className="card">
      {/* Complex rendering */}
    </div>
  );
});

// Parent doesn't re-create the component on every render
function Page({ data }: PageProps) {
  return <ExpensiveCard data={data} onAction={handleAction} />;
}
```

---

## Anti-patterns (Don't do this)

```typescript
// ❌ Render function
const renderHeader = () => <h1>Title</h1>;

// ❌ JSX in variable
const footer = <footer>Footer text</footer>;

// ❌ Render function with arguments
const renderButton = (label: string) => <button>{label}</button>;

// ❌ Complex conditional rendering in a function
const getContent = () => {
  if (a) return <A />;
  if (b) return <B />;
  return <C />;
};

// ❌ Over-using render functions instead of components
const App = () => {
  const renderHeader = () => <Header />;
  const renderContent = () => <Content />;
  const renderFooter = () => <Footer />;
  return (
    <div>
      {renderHeader()}
      {renderContent()}
      {renderFooter()}
    </div>
  );
};
```

## Checklist

- [ ] No render functions (`const renderX = () => <...>`)
- [ ] No JSX stored in variables (`const jsxVar = <...>`)
- [ ] All JSX content is in proper React components
- [ ] Components can be memoized if needed (`React.memo`)
- [ ] Array maps use inline JSX (this is standard)
- [ ] Conditional rendering uses clear if/return structure
- [ ] Props are used for customization, not function arguments
- [ ] Components are testable (not render functions)
- [ ] Components are reusable
