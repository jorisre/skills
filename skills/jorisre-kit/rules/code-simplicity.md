---
name: code-simplicity
description: Write simple, human-readable code first. Follow standard programming conventions. Use early returns to reduce nesting. Document why, not what. Extract logic that is complex, reused, or has clear business intent. Optimize only when needed.
---

# Code Simplicity

## Purpose
Ensure all code produced is simple, readable, and maintainable by humans. An agent can implement complex logic quickly, but the resulting code must be easy to understand at a glance.

---

## General Principles

### 1. Readability is the Default
Code is read far more often than it is written. Optimize for the reader.

```typescript
// ❌ Too clever — saves 2 lines but confuses
const result = data?.filter(x => x.active)?.[0]?.name ?? "unknown";

// ✅ Clear — easy to understand step by step
const activeItem = data?.find(item => item.active);
const name = activeItem?.name ?? "unknown";
```

### 2. Follow Standard Conventions
Don't invent patterns. Follow what any developer would learn:
- Single Responsibility Principle (one function, one reason to change)
- DRY (Don't Repeat Yourself) — extract after the 3rd occurrence or if logic is clear
- Guard clauses / Early returns to reduce nesting
- Named functions over anonymous — clarity over conciseness

```typescript
// ❌ Nested if-else
function process(user: User) {
  if (user) {
    if (user.active) {
      if (user.verified) {
        return doSomething(user);
      }
    }
  }
  return null;
}

// ✅ Early returns
function process(user: User) {
  if (!user) return null;
  if (!user.active) return null;
  if (!user.verified) return null;
  return doSomething(user);
}
```

### 3. Clear, Explicit Names
Name variables and functions as clearly as possible. Assume the reader has no context:

```typescript
// ❌ Unclear
const d = new Date();
const u = users.filter(x => x.age > 18);
const calc = (a, b) => a + b * 0.1;

// ✅ Clear
const currentDate = new Date();
const adultsOver18 = users.filter(user => user.age > 18);
const calculatePriceWithTax = (basePrice: number, taxRate: number) =>
  basePrice + basePrice * taxRate;
```

### 4. Simple Data Structures
Use straightforward types and avoid over-engineering:

```typescript
// ❌ Over-engineered
type UserState = {
  data: User | null;
  isLoading: boolean;
  error: Error | null;
  metadata: { timestamp: number; retries: number };
};

// ✅ Simple and sufficient (if this covers your needs)
interface UserState {
  user: User | null;
  isLoading: boolean;
  error: Error | null;
}
```

### 5. Documentation: Why, Not What
Comments should explain WHY, not WHAT. The code itself explains what it does.

```typescript
// ❌ Obvious — don't write this
// Get all active users
const activeUsers = users.filter(u => u.active);

// ✅ Good — explain the intent
// Filter active users to avoid sending notifications to deactivated accounts
const activeUsers = users.filter(u => u.active);

// ✅ Good — edge case explanation
// We retry up to 3 times because the API sometimes returns transient errors
const maxRetries = 3;
```

### 6. Avoid Over-Generalization
Build for what you need now, not for hypothetical futures:

```typescript
// ❌ Over-engineered for "future flexibility"
function formatValue(value: unknown, type: string, options?: Record<string, any>) {
  // 50 lines of type checking and configuration
}

// ✅ Build what you need
function formatDate(date: Date): string {
  return date.toLocaleDateString("en-US");
}

function formatCurrency(amount: number): string {
  return new Intl.NumberFormat("en-US", {
    style: "currency",
    currency: "USD",
  }).format(amount);
}
```

### 7. Extract When It Makes Sense
Extract logic if:
- The function becomes large (>30 lines) or complex
- The logic is reused in multiple places
- The logic has clear business intent/meaning
- Otherwise, inline code is often clearer

```typescript
// ❌ Premature extraction — trivial logic
function validateEmail(email: string): boolean {
  return isValidEmailFormat(email); // extracted unnecessarily
}
function isValidEmailFormat(email: string): boolean {
  return email.includes("@");
}

// ✅ Keep inline if it's just 1 line
function validateUser(user: User): boolean {
  return user.email.includes("@") && user.name.length > 0;
}

// ✅ Extract when logic is complex OR business-intent is clear
function calculateSubscriptionDiscount(user: User): number {
  // Complex logic based on account age, plan type, etc.
  // Clear intent: "calculate subscription discount"
  // Worth extracting and naming
}
```

### 8. Error Handling — Be Explicit
Handle errors clearly. Don't hide them or over-catch:

```typescript
// ❌ Silent failure
try {
  processData(data);
} catch {
  // Oops, something failed but we don't care?
}

// ✅ Explicit handling
async function fetchUser(id: string) {
  try {
    return await api.getUser(id);
  } catch (error) {
    if (error instanceof NotFoundError) {
      return null; // User doesn't exist, that's ok
    }
    throw error; // Unknown error, propagate
  }
}
```

### 9. Performance — Think About It, But Don't Over-Optimize
Consider performance, but simplicity comes first:

```typescript
// ✅ Simple and sufficient for most cases
const items = largeArray.filter(item => item.active);

// ⚠️ Only optimize if profiling shows this is actually slow
const itemsSet = new Set(largeArray.filter(item => item.active).map(item => item.id));
```

### 10. Testing — Code That's Hard to Test is Often Too Complex
If a function is hard to test, it's probably doing too much:

```typescript
// ❌ Hard to test — mixed concerns
function saveUserAndSendEmail(user: User) {
  const saved = db.save(user);
  email.send(user.email, "Welcome!");
  return saved;
}

// ✅ Easy to test — separate concerns
function saveUser(user: User): User {
  return db.save(user);
}

function sendWelcomeEmail(user: User): void {
  email.send(user.email, "Welcome!");
}

// Orchestration (testable with mocks)
async function createUser(user: User) {
  const saved = await saveUser(user);
  await sendWelcomeEmail(saved);
  return saved;
}
```

---

## Examples

### React Component — Simple & Clear

```typescript
interface UserProfileProps {
  userId: string;
}

export function UserProfile({ userId }: UserProfileProps) {
  const [user, setUser] = React.useState<User | null>(null);
  const [isLoading, setIsLoading] = React.useState(false);
  const [error, setError] = React.useState<Error | null>(null);

  React.useEffect(() => {
    async function loadUser() {
      setIsLoading(true);
      setError(null);
      try {
        const data = await fetchUser(userId);
        setUser(data);
      } catch (err) {
        setError(err instanceof Error ? err : new Error("Failed to load user"));
      } finally {
        setIsLoading(false);
      }
    }

    loadUser();
  }, [userId]);

  // Early return for loading state
  if (isLoading) return <Spinner />;

  // Early return for error state
  if (error) return <ErrorMessage error={error} />;

  // Early return for no data
  if (!user) return <EmptyState />;

  // Main content — clear and simple
  return (
    <div className="profile">
      <h1>{user.name}</h1>
      <p>{user.email}</p>
      <button onClick={() => handleEdit(user)}>Edit</button>
    </div>
  );
}
```

### Function — Clear Intent & Simple Logic

```typescript
// Clear intent in the function name
function calculateProductPrice(
  basePrice: number,
  quantity: number,
  discountPercent: number
): number {
  // Early return for invalid inputs
  if (basePrice <= 0 || quantity <= 0) {
    throw new Error("Price and quantity must be positive");
  }

  // Clear calculation steps
  const subtotal = basePrice * quantity;
  const discountAmount = subtotal * (discountPercent / 100);
  const total = subtotal - discountAmount;

  return total;
}

// Usage is obvious
const price = calculateProductPrice(100, 2, 10); // 180
```

### Extract Business Logic

```typescript
// Business logic extracted and named clearly
function isEligibleForTrial(user: User): boolean {
  const hasNoActiveSubscription = !user.subscriptionEndDate || user.subscriptionEndDate < new Date();
  const hasNeverTriedBefore = !user.trialUsedAt;
  return hasNoActiveSubscription && hasNeverTriedBefore;
}

// Usage in component
{isEligibleForTrial(user) && <OfferTrialButton user={user} />}
```

---

## Anti-patterns (Don't do this)

```typescript
// ❌ Too clever / too concise
const result = data?.reduce((acc, item) => 
  item.active ? [...acc, item.name] : acc, 
[]);

// ✅ Clear
const activeNames = data
  ?.filter(item => item.active)
  .map(item => item.name);

// ❌ Over-generalized
function transform(data: any, config: any): any {
  // 100 lines of conditional logic
}

// ✅ Specific functions for specific jobs
function formatUserName(user: User): string {
  return `${user.firstName} ${user.lastName}`;
}

// ❌ Silent failures
async function loadData() {
  try {
    return await fetch("/api/data");
  } catch {
    return null;
  }
}

// ✅ Explicit error handling
async function loadData() {
  try {
    return await fetch("/api/data");
  } catch (error) {
    console.error("Failed to load data:", error);
    throw error;
  }
}

// ❌ Nested ternaries
const status = a ? (b ? (c ? "x" : "y") : "z") : "w";

// ✅ If/else or object map
const status = getStatus(a, b, c);
```

## Checklist

- [ ] Code is readable at a glance — no clever tricks
- [ ] Function names are clear and explicit
- [ ] Variable names explain their purpose
- [ ] Early returns reduce nesting
- [ ] Comments explain WHY, not WHAT
- [ ] No premature optimization
- [ ] Logic is extracted only if it's complex, reused, or has business intent
- [ ] Error handling is explicit (not silent)
- [ ] Code is testable — concerns are separated
- [ ] Functions follow Single Responsibility Principle
