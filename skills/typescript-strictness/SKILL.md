---
name: typescript-strictness
description: Use when writing any TypeScript code - bans the use of any, enforces proper typing patterns including generics, discriminated unions, and type guards
---

# TypeScript Strictness

## The Rule

NEVER use `any`. If you write `any`, delete it and type it properly. No exceptions.

## What to Use Instead

| Instead of `any` for... | Use this |
|---|---|
| Unknown API response | `unknown` + type guard, or Zod schema with `z.infer` |
| Complex object | Define an `interface` or `type` |
| Generic function | Generics `<T>` |
| Event handlers | React event types: `React.MouseEvent<HTMLButtonElement>`, `React.ChangeEvent<HTMLInputElement>` |
| Third-party lib without types | `declare module` or write a `.d.ts` file |
| Temporary during refactor | `unknown` — never `any` |
| Union of possibilities | Discriminated unions with a `type` or `kind` field |
| Props | Always use `interface` with explicit types |
| API responses | Define response types, use Zod for runtime validation |
| Callback functions | Explicit function signatures: `(args: ArgType) => ReturnType` |

## Rationalization Resistance

| Excuse | Reality |
|--------|---------|
| "The API shape is too complex" | Define the type. Complex shapes need types MORE, not less. |
| "I'll type it properly later" | You won't. Type it now. |
| "It's just a utility function" | Utility functions spread everywhere. Bad types propagate. |
| "The types would be too verbose" | Extract a type alias. Verbose types > invisible bugs. |
| "TypeScript can infer it" | If it infers correctly, you don't need `any`. Let it infer. |
| "I need `any` for this pattern" | You need generics for this pattern. |
| "It's a quick prototype" | Prototypes become production. Type it now. |
| "The library doesn't have types" | Write a declaration file. 5 minutes now saves hours. |

## Red Flags — STOP and Retype

- `as any`
- `: any`
- `any[]`
- `Record<string, any>`
- `Function` (use specific signature)
- `object` when you know the shape
- Type assertions (`as Type`) to silence errors — fix the actual type instead
- `@ts-ignore` or `@ts-expect-error` without a clear, documented reason

## tsconfig Standards

- `strict: true`
- `noImplicitAny: true`
- `strictNullChecks: true`
- `noUncheckedIndexedAccess: true` (recommended)
