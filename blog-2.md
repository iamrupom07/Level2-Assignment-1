# `Pick` and `Omit`: Keep Your TypeScript Code DRY

## Introduction

In large TypeScript projects, you often have a master interface that represents a full data model — a `User`, a `Product`, a `BlogPost`. But throughout the application, you rarely need the *entire* model at once. A form might need only a few fields. An API response might intentionally exclude sensitive ones. If you manually redeclare a partial version of the same interface everywhere, you create duplicated type definitions that quickly drift out of sync.

TypeScript's built-in utility types `Pick` and `Omit` solve this cleanly. They let you derive new, focused types directly from an existing interface — keeping everything in sync automatically and making your code **DRY** (Don't Repeat Yourself).

---

## The Problem: Manual Interface Duplication

Imagine a master `User` interface:

```typescript
interface User {
  id: number;
  name: string;
  email: string;
  passwordHash: string;
  createdAt: Date;
  role: "admin" | "member";
}
```

Without utility types, you might write separate interfaces for different use cases:

```typescript
// For a public profile page
interface PublicUser {
  id: number;
  name: string;
  role: "admin" | "member";
}

// For a registration form
interface RegistrationForm {
  name: string;
  email: string;
  passwordHash: string;
}
```

This is fragile. If `User.name` changes to `fullName`, you now have three places to update — and it's easy to forget one.

---

## `Pick`: Select Only What You Need

`Pick<T, K>` creates a new type by selecting a subset of keys `K` from type `T`.

```typescript
type PublicUser = Pick<User, "id" | "name" | "role">;

// Equivalent to:
// {
//   id: number;
//   name: string;
//   role: "admin" | "member";
// }
```

Now `PublicUser` is **derived** from `User`. If `User` changes, `PublicUser` updates automatically. No manual sync required.

A practical example — a function that renders a user card:

```typescript
function renderUserCard(user: Pick<User, "id" | "name" | "role">): string {
  return `[${user.role.toUpperCase()}] ${user.name} (#${user.id})`;
}
```

By using `Pick` directly in the function signature, you document exactly which fields are needed without creating a separate named type.

---

## `Omit`: Exclude What You Don't Want

`Omit<T, K>` is the inverse — it creates a new type with all keys from `T` *except* those in `K`.

```typescript
type SafeUser = Omit<User, "passwordHash">;

// Equivalent to:
// {
//   id: number;
//   name: string;
//   email: string;
//   createdAt: Date;
//   role: "admin" | "member";
// }
```

This is perfect for API responses where sensitive fields must be stripped before sending data to the client:

```typescript
function toSafeUser(user: User): Omit<User, "passwordHash"> {
  const { passwordHash, ...safeUser } = user;
  return safeUser;
}
```

When a new sensitive field is added to `User` (say, `twoFactorSecret`), the `Omit` definition is the only place you need to update — the function itself stays untouched.

---

## Combining Them

You can compose `Pick` and `Omit` with other utility types for more precise slices:

```typescript
// A type for updating user profile — only the fields a user can edit
type EditableProfile = Pick<User, "name" | "email">;

// Admin view — everything except internal fields
type AdminView = Omit<User, "passwordHash" | "createdAt">;
```

---

## `Pick` vs `Omit`: When to Use Which

| Scenario | Use |
|---|---|
| You need a small subset of a large interface | `Pick` |
| You want everything except one or two fields | `Omit` |
| You're building a form with specific inputs | `Pick` |
| You're sanitizing data before an API response | `Omit` |

---

## Conclusion

`Pick` and `Omit` are foundational tools for keeping TypeScript codebases maintainable. Instead of scattering partial copies of your interfaces across files, you define the source of truth once and derive everything else from it. When the master interface evolves, your derived types evolve with it — automatically, safely, and with zero extra effort. That is the DRY principle at its best.
