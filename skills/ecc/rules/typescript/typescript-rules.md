# TypeScript Rules

## Types & Interfaces
- Prefer interfaces for object shapes that will be extended
- Use type aliases for unions, intersections, and complex types
- Enable `strict: true` in tsconfig
- Avoid `any` - use `unknown` when type is truly unknown
- Use `as const` for literal types
- Leverage branded types for domain primitives

## Generics
- Use generics to create reusable, type-safe utilities
- Constrain generics with `extends` for better DX
- Prefer generic functions over type assertions

## State Management
- Use discriminated unions for complex state
- Leverage TypeScript's type narrowing
- Use `satisfies` operator for type validation

## Async Code
- Type async function return values explicitly
- Use `Promise.all` for parallel operations
- Handle errors with try/catch and typed error classes

## Project Structure
- Co-locate types with their usage
- Use barrel files (index.ts) for clean exports
- Keep type definitions close to implementation
