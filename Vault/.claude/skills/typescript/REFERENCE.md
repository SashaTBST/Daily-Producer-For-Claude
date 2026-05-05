# TypeScript Skill — Reference
TypeScript 5.x | strict: true | Last verified: 2026-04-27

## Research Sources
- TypeScript 5.0 release notes: https://www.typescriptlang.org/docs/handbook/release-notes/typescript-5-0.html
- Announcing TypeScript 5.0 (Microsoft): https://devblogs.microsoft.com/typescript/announcing-typescript-5-0/
- State of TypeScript 2025: https://medium.com/@noroavetisyan/the-state-of-typescript-in-2025-architectural-maturity-ecosystem-dominance-and-the-erasable-fa746201c2e0
- TypeScript 5 features (LogRocket): https://blog.logrocket.com/exploring-typescript-5-features-smaller-simpler-faster/
- TypeScript strict mode guide: https://oneuptime.com/blog/post/2026-02-20-typescript-strict-mode-guide/view
- strictNullChecks is the only setting that matters: https://medium.com/totally-typescript/strictnullchecks-is-the-only-ts-setting-that-matters-ece507694d7d
- Strict mode best practices: https://rishikc.com/articles/typescript-strict-mode-best-practices/
- tsconfig reference (official): https://www.typescriptlang.org/tsconfig/
- tsconfig best practices 2025 (DEV): https://dev.to/mitu_mariam/typescript-best-practices-in-2025-57hb
- tsconfig for speed 2025: https://levelup.gitconnected.com/how-to-properly-configure-your-tsconfig-json-for-speed-2025-fee4dfb2e4c7
- TypeScript configuration best practices: https://stevekinney.com/courses/full-stack-typescript/typescript-configuration-best-practices
- Utility types (official): https://www.typescriptlang.org/docs/handbook/utility-types.html
- Utility types guide: https://medium.com/@robinviktorsson/typescript-utility-types-how-and-when-to-use-them-partial-required-readonly-pick-omit-d47c2466575c
- Utility types (Better Stack): https://betterstack.com/community/guides/scaling-nodejs/ts-utility-types/
- Vite TypeScript features: https://vite.dev/guide/features
- TypeScript with Vite setup: https://medium.com/@robinviktorsson/complete-guide-to-setting-up-react-with-typescript-and-vite-2025-468f6556aaf2
- Running TS in Node.js (LogRocket): https://blog.logrocket.com/running-typescript-node-js-tsx-vs-ts-node-vs-native/
- Node.js native TypeScript support: https://nodejs.org/learn/typescript/run-natively
- Node.js TS module docs: https://nodejs.org/api/typescript.html
- Modern Node + TS setup 2025: https://dev.to/woovi/a-modern-nodejs-typescript-setup-for-2025-nlk
- TypeScript generics handbook: https://www.typescriptlang.org/docs/handbook/2/generics.html
- Mastering TypeScript generics: https://nateross.dev/blog/mastering-typescript-generics
- Generics tips (DEV): https://dev.to/mr_mornin_star/10-tips-for-mastering-typescript-generics-1aph
- Discriminated unions (Convex): https://www.convex.dev/typescript/advanced/type-operators-manipulation/typescript-discriminated-union
- TypeScript discriminated unions: https://oneuptime.com/blog/post/2026-01-24-typescript-discriminated-unions/view
- Never type for error handling (DEV): https://dev.to/steal/the-never-type-and-error-handling-in-typescript-4k39
- TypeScript deep dive — discriminated unions: https://basarat.gitbook.io/typescript/type-system/discriminated-unions
- Unions and intersections (official): https://www.typescriptlang.org/docs/handbook/unions-and-intersections.html

---

## TS 5.x Features — Use by Default

### Decorators (TS 5.0+ — no flag needed)
```ts
// No experimentalDecorators required in TS 5+
function log(target: any, context: ClassMethodDecoratorContext) {
    return function (this: any, ...args: any[]) {
        console.log(`Calling ${String(context.name)}`);
        return (target as Function).apply(this, args);
    };
}

class UserService {
    @log
    getUser(id: number) { /* ... */ }
}
```

### satisfies Operator (TS 4.9+)
```ts
// Validates shape while keeping precise type inference
type Palette = Record<string, [number, number, number] | string>;

const palette = {
    red: [255, 0, 0],
    blue: '#0000ff',
} satisfies Palette;

// palette.red is [number, number, number] — not string | [number, number, number]
// Without satisfies: would be the wider union type
```

### const Type Parameters (TS 5.0+)
```ts
// Infers literal types without needing `as const`
function first<const T extends readonly unknown[]>(arr: T): T[0] {
    return arr[0];
}

const result = first(['a', 'b', 'c']); // type: "a" not string
```

### Enums → const Objects (Vite/isolatedModules safe)
```ts
// NEVER use in Vite projects — breaks isolatedModules
enum Direction { Up, Down, Left, Right } // ❌

// USE THIS INSTEAD — identical behaviour, no build issues
const Direction = {
    Up: 'up',
    Down: 'down',
    Left: 'left',
    Right: 'right',
} as const;

type Direction = typeof Direction[keyof typeof Direction];
// Plain English: `as const` locks the values so TypeScript knows
// Direction.Up is always 'up', not just any string.
```

---

## tsconfig Templates

### Browser / Vite Project
```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "ESNext",
    "moduleResolution": "bundler",
    "lib": ["ES2022", "DOM", "DOM.Iterable"],
    "strict": true,
    "isolatedModules": true,
    "noEmit": true,
    "skipLibCheck": true,
    "incremental": true,
    "paths": {
      "@/*": ["./src/*"]
    }
  },
  "include": ["src"]
}
```

**package.json script — required for Vite projects:**
```json
{
  "scripts": {
    "typecheck": "tsc --noEmit",
    "build": "tsc --noEmit && vite build"
  }
}
```
Vite transpiles but does NOT type-check. Always run `npm run typecheck` before committing.

### Node.js (22+)
```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "ESNext",
    "moduleResolution": "node",
    "lib": ["ES2022"],
    "strict": true,
    "isolatedModules": true,
    "outDir": "dist",
    "rootDir": "src",
    "resolveJsonModule": true,
    "skipLibCheck": true,
    "incremental": true
  },
  "include": ["src"],
  "exclude": ["node_modules", "dist"]
}
```

**Running TS in Node:**
```bash
# Dev (fast, no type-check):
npx tsx src/index.ts

# Type-check only (no emit):
npx tsc --noEmit

# Production build:
npx tsc && node dist/index.js
```

### Library / Package
```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "ESNext",
    "moduleResolution": "bundler",
    "declaration": true,
    "declarationMap": true,
    "sourceMap": true,
    "outDir": "dist",
    "strict": true,
    "isolatedModules": true,
    "skipLibCheck": true
  },
  "include": ["src"],
  "exclude": ["node_modules", "dist", "**/*.test.ts"]
}
```

---

## Utility Types — Quick Reference

```ts
type User = { id: number; name: string; email: string; password: string };

// Partial<T> — all properties optional (partial updates)
function updateUser(id: number, changes: Partial<User>) { /* ... */ }

// Required<T> — all properties required (ensure completeness)
function createUser(data: Required<Omit<User, 'id'>>) { /* ... */ }

// Readonly<T> — immutable (prevents mutation)
function displayUser(user: Readonly<User>) { /* ... */ }

// Pick<T, K> — select specific properties
type UserSummary = Pick<User, 'id' | 'name'>;

// Omit<T, K> — exclude specific properties (e.g. hide password)
type PublicUser = Omit<User, 'password'>;

// NonNullable<T> — remove null/undefined from union
type SafeUser = NonNullable<User | null | undefined>; // User

// ReturnType<T> — extract function return type
type CreateResult = ReturnType<typeof createUser>;

// Parameters<T> — extract function parameter types as tuple
type CreateParams = Parameters<typeof createUser>;

// Exclude<T, U> — remove types from union
type StringOrNumber = string | number | boolean;
type NoBoolean = Exclude<StringOrNumber, boolean>; // string | number

// Extract<T, U> — keep only matching types from union
type OnlyString = Extract<StringOrNumber, string>; // string
```

---

## Generics — Patterns

```ts
// Basic generic function — let TS infer, don't over-specify
function first<T>(arr: T[]): T | undefined {
    return arr[0];
}
const name = first(['Alice', 'Bob']); // inferred: string | undefined

// Constrained generic — ensure required properties
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
    return obj[key];
}

// Generic with default
interface ApiResponse<T = unknown> {
    data: T;
    status: number;
    message: string;
}

// When NOT to use generics — prefer union if it's simpler
type StringOrNumber = string | number; // ✓ simpler
function process<T extends string | number>(val: T): T { /* ... */ } // ✗ overkill here
```

---

## Error Handling Patterns

### Standard: try/catch with typed errors (default)
```ts
async function fetchUser(id: number): Promise<User> {
    const response = await fetch(`/api/users/${id}`);
    if (!response.ok) {
        throw new Error(`HTTP ${response.status}: failed to fetch user ${id}`);
    }
    return response.json() as Promise<User>;
}

// Caller
try {
    const user = await fetchUser(1);
} catch (err) {
    // err is unknown in strict mode — narrow before using
    if (err instanceof Error) {
        console.error(err.message);
    }
}
```

### Advanced: Result type pattern (opt-in — ask for it)
```ts
// Discriminated union — status property is the discriminant
type Result<T> =
    | { status: 'ok'; data: T }
    | { status: 'fail'; message: string };

async function fetchUser(id: number): Promise<Result<User>> {
    try {
        const response = await fetch(`/api/users/${id}`);
        if (!response.ok) return { status: 'fail', message: `HTTP ${response.status}` };
        const data = await response.json() as User;
        return { status: 'ok', data };
    } catch (err) {
        return { status: 'fail', message: err instanceof Error ? err.message : 'Unknown error' };
    }
}

// Caller — TypeScript narrows type in each branch
const result = await fetchUser(1);
if (result.status === 'ok') {
    console.log(result.data.name); // data available here
} else {
    console.error(result.message); // message available here
}
```

### never for exhaustive checking
```ts
// Ensures all union cases are handled — new cases cause a compile error
function assertNever(value: never): never {
    throw new Error(`Unhandled case: ${JSON.stringify(value)}`);
}

type Shape = { kind: 'circle'; radius: number } | { kind: 'square'; side: number };

function area(shape: Shape): number {
    switch (shape.kind) {
        case 'circle': return Math.PI * shape.radius ** 2;
        case 'square': return shape.side ** 2;
        default: return assertNever(shape); // TS errors if a case is missing
    }
}
```

---

## REVIEW Checklist

| # | Check | What to look for | Report |
|---|---|---|---|
| 1 | strict: true | tsconfig has `"strict": true` — not partial flags | PASS/FAIL |
| 2 | No bare any | Search for `: any` — use `unknown` + narrowing instead | PASS/WARN |
| 3 | No unguarded assertions | `as Type` without preceding type guard or instanceof check | PASS/WARN |
| 4 | No unguarded ! | Non-null assertions without explanatory comment | PASS/WARN |
| 5 | No @ts-ignore | Without a comment explaining why it's necessary | PASS/WARN |
| 6 | Return types on exports | Exported functions have explicit return type annotations | PASS/WARN |
| 7 | Null handled | All nullable values narrowed before use | PASS/FAIL |
| 8 | No enums (Vite) | If Vite project: no enum declarations — use const objects | PASS/FAIL |
| 9 | isolatedModules | Vite tsconfig has `"isolatedModules": true` | PASS/FAIL |
| 10 | typecheck script | package.json has `"typecheck": "tsc --noEmit"` (Vite projects) | PASS/WARN |
