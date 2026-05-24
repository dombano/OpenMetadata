# TypeScript Checkstyle Skill

This skill enforces TypeScript/React code style standards for the OpenMetadata UI codebase.

## Trigger

Use this skill when:
- Reviewing TypeScript or TSX files
- Checking React component code quality
- Validating imports, exports, and module structure
- Enforcing naming conventions for components, hooks, and utilities

## Rules

### Naming Conventions

1. **Components**: PascalCase for React components and their files
   - ✅ `UserProfile.tsx`, `export const UserProfile = ...`
   - ❌ `userProfile.tsx`, `export const userProfile = ...`

2. **Hooks**: camelCase prefixed with `use`
   - ✅ `useUserData.ts`, `export const useUserData = ...`
   - ❌ `UserData.ts`, `export const getUserData = ...`

3. **Interfaces and Types**: PascalCase, interfaces prefixed with `I` only when disambiguating
   - ✅ `UserProfileProps`, `EntityType`
   - ❌ `userProfileProps`, `entitytype`

4. **Constants**: UPPER_SNAKE_CASE for module-level constants
   - ✅ `const MAX_RETRY_COUNT = 3;`
   - ❌ `const maxRetryCount = 3;`

5. **Enums**: PascalCase for enum name, PascalCase for members
   - ✅ `enum EntityType { Table, Dashboard }`
   - ❌ `enum entityType { table, dashboard }`

### Imports

1. **Import order** (enforced via ESLint `import/order`):
   - Built-in Node modules
   - External packages (react, antd, etc.)
   - Internal absolute imports (`src/...`)
   - Relative imports (`./`, `../`)
   - Type-only imports last

2. **No default exports** for utility functions and hooks; use named exports
   - ✅ `export const fetchEntity = ...`
   - ❌ `export default fetchEntity`

3. **React imports**: Do NOT import React explicitly in TSX files (React 17+ JSX transform)
   - ❌ `import React from 'react';`

### Component Structure

1. Props interface must be defined before the component
2. Destructure props in function signature
3. Use `FC` type annotation only when necessary; prefer explicit return types
4. Event handlers named with `handle` prefix: `handleClick`, `handleSubmit`
5. Boolean props named with `is`/`has`/`should` prefix: `isLoading`, `hasError`

### Type Safety

1. **No `any`**: Avoid `any` type; use `unknown` or proper generics
2. **Non-null assertions**: Avoid `!` non-null assertions; use optional chaining or type guards
3. **Type assertions**: Prefer `as` over angle-bracket syntax
4. **Explicit return types**: Required for exported functions and hooks

### File Structure

```
src/
  components/
    ComponentName/
      ComponentName.tsx        # Main component
      ComponentName.test.tsx   # Unit tests
      ComponentName.less       # Component styles (if needed)
      index.ts                 # Re-export
  hooks/
    useHookName.ts
    useHookName.test.ts
  utils/
    utilityName.ts
    utilityName.test.ts
```

### Anti-patterns to Flag

- `console.log` statements in non-test files (use logger utility)
- Inline styles (`style={{ ... }}`) — use CSS classes instead
- Magic numbers/strings — extract to named constants
- Deeply nested ternaries — extract to variables or helper functions
- Missing `key` props in list renders
- Direct DOM manipulation without refs

## How to Apply

When reviewing a TypeScript file, check each rule category systematically:

1. Scan naming conventions top-to-bottom
2. Verify import order and style
3. Check component/function structure
4. Validate type safety patterns
5. Look for anti-patterns

Report violations with:
- File path and line number
- Rule violated
- Current code snippet
- Suggested fix
