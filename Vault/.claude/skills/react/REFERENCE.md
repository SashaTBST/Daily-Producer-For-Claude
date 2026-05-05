# React Skill — Reference
React 19 | TypeScript strict | Last verified: 2026-04-29

## Research Sources
- React 19 release blog (official): https://react.dev/blog/2024/12/05/react-19
- React 19 upgrade guide (official): https://react.dev/blog/2024/04/25/react-19-upgrade-guide
- useOptimistic (official docs): https://react.dev/reference/react/useOptimistic
- useImperativeHandle (official docs): https://react.dev/reference/react/useImperativeHandle
- React 19 new hooks tutorial (DEV): https://dev.to/princeofv/react-19-new-hooks-complete-tutorial-2026-guide-119p
- React 19 TypeScript best practices (Medium): https://medium.com/@CodersWorld99/react-19-typescript-best-practices-the-new-rules-every-developer-must-follow-in-2025-3a74f63a0baf
- React event handlers with TypeScript (Carl Rippon): https://carlrippon.com/react-event-handlers-with-typescript/
- React TypeScript cheatsheet (GitHub): https://github.com/typescript-cheatsheets/react
- React hooks TypeScript typing guide (cheatsheet): https://react-typescript-cheatsheet.netlify.app/docs/basic/getting-started/hooks/
- React Hook Form vs React 19 (LogRocket): https://blog.logrocket.com/react-hook-form-vs-react-19/
- Forms in React 19 deep dive (Kieran Barker): https://barker.codes/blog/forms-in-react-19/
- React XSS guide (StackHawk): https://www.stackhawk.com/blog/react-xss-guide-examples-and-prevention/
- React security code review checklist (PropelCode): https://www.propelcode.ai/blog/react-security-vulnerabilities-code-review-checklist
- React anti-patterns 2025 (JSDev): https://jsdev.space/react-anti-patterns-2025/
- React hooks anti-patterns (TechInsights): https://techinsights.manisuec.com/javascript/react-hooks-antipatterns/
- React 19 ref-as-prop migration (Saeloun): https://blog.saeloun.com/2025/03/24/react-19-ref-as-prop/

---

## React 19 — Key Changes from React 18

| Feature | React 18 | React 19 |
|---|---|---|
| Ref | `forwardRef()` required | Pass `ref` as plain prop |
| Context | `<Context.Provider value={}>` | `<Context value={}>` |
| Form state hook | `useFormState` (deprecated) | `useActionState` |
| Async transitions | manual | Actions via `action` prop |
| Reading promises | not supported | `use(promise)` in render |

---

## Component Typing — WRITE Mode

```tsx
// Function declaration — preferred over React.FC
// Plain English: we define the prop shape with an interface, then use it as the parameter type
interface ButtonProps {
    label: string;
    onClick: (e: React.MouseEvent<HTMLButtonElement>) => void;
    disabled?: boolean;
}

function Button({ label, onClick, disabled = false }: ButtonProps): JSX.Element {
    return <button onClick={onClick} disabled={disabled}>{label}</button>;
}

// Extending native element props — add your own on top of everything a <div> accepts
interface CardProps extends React.ComponentPropsWithoutRef<'div'> {
    title: string;
}

function Card({ title, children, className, ...rest }: CardProps): JSX.Element {
    return <div className={`card ${className ?? ''}`} {...rest}><h2>{title}</h2>{children}</div>;
}

// Generic component in .tsx — trailing comma required to avoid JSX parse conflict
// Plain English: TypeScript would read <T> as a JSX tag without the comma
function List<T,>({ items, renderItem }: {
    items: T[];
    renderItem: (item: T, index: number) => JSX.Element;
}): JSX.Element {
    return <ul>{items.map((item, i) => renderItem(item, i))}</ul>;
}

// Ref as prop — React 19 pattern (no forwardRef needed)
function Input({ ref, ...props }: React.ComponentPropsWithRef<'input'>): JSX.Element {
    return <input ref={ref} {...props} />;
}

// React.FC — legacy pattern, shown for migration awareness
// Plain English: React.FC adds an implicit `children` prop (React 17 behaviour)
// and makes generics harder. Prefer function declaration.
// const Button: React.FC<ButtonProps> = ({ label }) => <button>{label}</button>; // ❌ legacy
```

---

## Event Handler Types — Quick Reference

```tsx
// Common event types — all narrowed to the specific element
const handleClick   = (e: React.MouseEvent<HTMLButtonElement>)  => {};
const handleChange  = (e: React.ChangeEvent<HTMLInputElement>)   => {};
const handleSubmit  = (e: React.FormEvent<HTMLFormElement>)      => {};
const handleKeyDown = (e: React.KeyboardEvent<HTMLInputElement>) => {};
const handleFocus   = (e: React.FocusEvent<HTMLInputElement>)    => {};
const handleDrop    = (e: React.DragEvent<HTMLDivElement>)       => {};

// Inline — TypeScript infers from context (no annotation needed)
<button onClick={(e) => console.log(e.currentTarget.name)}>Click</button>
```

---

## Hooks Typing — HOOKS Mode

```tsx
// useState — provide generic when initial value doesn't show the type
const [count, setCount] = useState<number>(0);
const [user, setUser]   = useState<User | null>(null);

// useRef — two uses: DOM element (null initial), mutable value (no null)
const inputRef  = useRef<HTMLInputElement>(null);   // DOM: null because React fills it
const timerRef  = useRef<ReturnType<typeof setTimeout>>();  // mutable: no null needed

// useCallback — return type inferred; annotate params
const handleClick = useCallback((id: number): void => {
    setCount(c => c + id);
}, []);

// useMemo — return type inferred from callback
const sorted = useMemo(() => [...items].sort(), [items]);

// useContext — createContext with T | null, guard in consumer
// Plain English: null! is an anti-pattern — it tells TS to trust you, but can crash at runtime
const UserContext = createContext<User | null>(null);

function useUser(): User {
    const ctx = useContext(UserContext);
    if (!ctx) throw new Error('useUser must be inside UserContext.Provider');
    return ctx;
}

// useReducer — discriminated union for actions (exhaustive checking)
type Action =
    | { type: 'increment' }
    | { type: 'set'; value: number };

function reducer(state: number, action: Action): number {
    switch (action.type) {
        case 'increment': return state + 1;
        case 'set': return action.value;
    }
}

// [React 19] useActionState — async form action with typed state
// Plain English: replaces useFormState from React 18 (deprecated)
const [state, action, isPending] = useActionState(
    async (prev: string, formData: FormData): Promise<string> => {
        const name = formData.get('name') as string;
        await saveToServer(name);
        return `Saved: ${name}`;
    },
    '' // initial state
);

// [React 19] useOptimistic — show optimistic UI while action runs
const [optimisticItems, addOptimistic] = useOptimistic(
    items,
    (state: Item[], newItem: Item) => [...state, newItem]
);
```

---

## Form Actions — FORMS Mode

```tsx
// React 19 native form Action — no onSubmit handler needed
// Plain English: the `action` prop on <form> now accepts an async function in React 19
interface FormState {
    message: string;
    error: string | null;
}

const initialState: FormState = { message: '', error: null };

async function submitAction(prev: FormState, formData: FormData): Promise<FormState> {
    const email = formData.get('email');
    if (typeof email !== 'string' || !email.includes('@')) {
        return { message: '', error: 'Valid email required' };
    }
    await saveEmail(email);
    return { message: 'Saved!', error: null };
}

function ContactForm(): JSX.Element {
    const [state, action, isPending] = useActionState(submitAction, initialState);

    return (
        <form action={action}>
            <input name="email" type="email" required aria-describedby="email-error" />
            {state.error && <p id="email-error" role="alert">{state.error}</p>}
            {state.message && <p>{state.message}</p>}
            <button type="submit" disabled={isPending}>
                {isPending ? 'Saving...' : 'Save'}
            </button>
        </form>
    );
}
```

---

## Security Patterns

```tsx
// dangerouslySetInnerHTML — NEVER with user data
// XSS: attacker controls the string → injects script tags
<div dangerouslySetInnerHTML={{ __html: userInput }} />  // ❌

// For trusted CMS/rich text — sanitize first
import DOMPurify from 'dompurify';
<div dangerouslySetInnerHTML={{ __html: DOMPurify.sanitize(cmsHtml) }} />  // ✓

// Dynamic href — React does NOT sanitize URLs
// javascript:alert(1) is a valid href value — React will render it
<a href={userUrl}>Click</a>  // ❌ if userUrl is unvalidated

// Validate URL prefix before rendering
function SafeLink({ url, label }: { url: string; label: string }): JSX.Element | null {
    if (!url.startsWith('https://') && !url.startsWith('http://')) return null;
    return <a href={url}>{label}</a>;
}
```

---

## REVIEW Checklist

| # | Check | What to look for | Report |
|---|---|---|---|
| 1 | dangerouslySetInnerHTML | Any use without DOMPurify.sanitize() | PASS/FAIL |
| 2 | Dynamic href/src | href={variable} without URL validation | PASS/WARN |
| 3 | Prop types complete | All components have interface/type for props | PASS/WARN |
| 4 | No React.FC | Function declaration used, not React.FC | PASS/WARN |
| 5 | No forwardRef (React 19) | New components pass ref as prop | PASS/WARN |
| 6 | Context null safety | createContext uses T\|null, no null! assertion | PASS/WARN |
| 7 | Hook dependencies | useEffect/useCallback deps arrays correct | PASS/WARN |
| 8 | No direct state mutation | state.items.push() etc. — must use spread/copy | PASS/FAIL |
