# TypeScript Assignment — Advanced Problem Solving with OOP

This repository contains the completed solutions for the TypeScript & OOP assignment.

## File Structure

```
├── solutions.ts   — All 7 coding solutions
├── blog-1.md      — Blog: any vs unknown & Type Narrowing
├── blog-2.md      — Blog: Pick and Omit Utility Types
└── README.md
```

## Solutions Overview

| Problem | Function | Description |
|---|---|---|
| 1 | `filterEvenNumbers` | Filters even numbers from an array |
| 2 | `reverseString` | Reverses a string |
| 3 | `checkType` | Union type guard returning "String" or "Number" |
| 4 | `getProperty` | Generic function to get a typed object property |
| 5 | `toggleReadStatus` | Adds `isRead: true` to a Book object |
| 6 | `Student` class | Extends `Person` with grade and `getDetails()` |
| 7 | `getIntersection` | Returns elements common to two arrays |

## Blog Posts

- **blog-1.md** — Explains why `any` is a type safety hole, why `unknown` is the safer alternative, and how type narrowing works with `typeof`, `instanceof`, and custom type guards.
- **blog-2.md** — Explains how `Pick` and `Omit` utility types eliminate interface duplication and keep TypeScript code DRY.

## Running the Code

```bash
# Install TypeScript globally if not already installed
npm install -g typescript

# Compile
tsc solutions.ts

# Or use ts-node for direct execution
npx ts-node solutions.ts
```
