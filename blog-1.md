# `any` vs `unknown`: Why TypeScript's Safer Type Matters

## Introduction

TypeScript's entire purpose is to catch errors before runtime through a strong type system. But what happens when you genuinely don't know the type of some data — like a response from an external API, user input, or a third-party library? TypeScript offers two types for this: `any` and `unknown`. They look similar at first glance, but they behave very differently — and that difference is critical for writing safe, maintainable code.

---

## The Problem with `any`

`any` is essentially a backdoor out of the TypeScript type system. When you annotate something as `any`, TypeScript stops checking it entirely. You can call methods on it, index into it, assign it to anything — and TypeScript won't complain, even if what you're doing is completely wrong.

```typescript
function processData(data: any) {
  console.log(data.name.toUpperCase()); // No error from TypeScript
}

processData(42); // Runtime crash: Cannot read properties of undefined
```

TypeScript stays silent, but at runtime, `42.name` is `undefined`, and calling `.toUpperCase()` on it throws an error. This is why `any` is often called a **"type safety hole"** — it silently disables all protection.

Using `any` is like wearing a seatbelt but cutting the strap — it gives you a false sense of security.

---

## `unknown`: The Safe Alternative

`unknown` is TypeScript's way of saying: *"I acknowledge this value exists, but I won't let you use it carelessly."* You can assign any value to an `unknown` variable, but you **cannot** use it until you've told TypeScript what it is.

```typescript
function processData(data: unknown) {
  console.log(data.name.toUpperCase()); // ❌ Error: Object is of type 'unknown'
}
```

TypeScript forces you to verify the type before you proceed. This is the correct behavior — it prevents silent runtime crashes.

---

## Type Narrowing: Teaching TypeScript What You Know

**Type narrowing** is the technique of using runtime checks to tell TypeScript more about a value's type inside a specific code block. Once narrowed, TypeScript updates its understanding automatically.

### Using `typeof`

```typescript
function display(value: unknown): string {
  if (typeof value === "string") {
    return value.toUpperCase(); // ✅ Safe: TypeScript knows it's a string here
  }
  if (typeof value === "number") {
    return value.toFixed(2); // ✅ Safe: TypeScript knows it's a number here
  }
  return "Unsupported type";
}
```

### Using `instanceof`

```typescript
function handleError(error: unknown): string {
  if (error instanceof Error) {
    return error.message; // ✅ Safe
  }
  return "An unknown error occurred";
}
```

### Using a Type Guard Function

For complex objects, you can write a custom type guard:

```typescript
interface User {
  id: number;
  name: string;
}

function isUser(value: unknown): value is User {
  return (
    typeof value === "object" &&
    value !== null &&
    "id" in value &&
    "name" in value
  );
}

function greet(data: unknown): string {
  if (isUser(data)) {
    return `Hello, ${data.name}!`; // ✅ Fully typed
  }
  return "Not a user";
}
```

---

## `any` vs `unknown` at a Glance

| Feature | `any` | `unknown` |
|---|---|---|
| Accepts any value | ✅ | ✅ |
| Can be used without checking | ✅ | ❌ |
| TypeScript checks usage | ❌ | ✅ |
| Type safe | ❌ | ✅ |

---

## Conclusion

Reach for `unknown` whenever you're dealing with data whose shape you can't guarantee at compile time — API responses, parsed JSON, dynamic form inputs, or caught errors. Pair it with type narrowing to safely unlock the value. Reserve `any` only as a last resort when migrating legacy code or working around a stubborn third-party library — and even then, document why.

The few extra lines of narrowing code are a small price for the guarantee that your program won't silently crash in production.
