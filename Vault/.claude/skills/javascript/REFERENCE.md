# JavaScript Skill — Reference
ES2024 | Vite | ESLint 9 | Vitest | Last verified: 2026-04-27

## Research Sources
- ES2025 features guide (ExploringJS): https://exploringjs.com/js/book/ch_new-javascript-features.html
- ECMAScript 2024 features (InfoWorld): https://www.infoworld.com/article/2336858/ecmascript-2024-features-you-can-use-now.html
- ES2025 production features (Medium): https://medium.com/@mernstackdevbykevin/es2025-javascript-features-you-can-start-using-right-now-fed3784298b4
- What's new in ES2024 (Gaute Meek Olsen): https://gaute.dev/dev-blog/whats-new-in-javascript-es2024
- XSS still alive in 2025 (Hacker News): https://thehackernews.com/2025/07/why-react-didnt-kill-xss-new-javascript.html
- JavaScript security vulnerabilities (Aikido): https://www.aikido.dev/blog/javascript-security-vulnerabilities
- Prototype pollution exploit chains (Beyond XSS): https://aszx87410.github.io/beyond-xss/en/ch3/prototype-pollution/
- npm supply chain meltdown 2025 (DEV): https://dev.to/usman_awan/the-night-npm-caught-fire-inside-the-2025-javascript-supply-chain-meltdown-52o3
- North Korea targets Axios npm (Google Cloud): https://cloud.google.com/blog/topics/threat-intelligence/north-korea-threat-actor-targets-axios-npm-package
- CSP cheat sheet (OWASP): https://cheatsheetseries.owasp.org/cheatsheets/Content_Security_Policy_Cheat_Sheet.html
- DOMPurify official: https://dompurify.com/
- Async/await best practices (Poespas): https://blog.poespas.me/posts/2024/08/13/javascript-async-await-in-loops-best-practices/
- Async programming best practices (OpenReplay): https://blog.openreplay.com/best-practices-for-async-programming-in-javascript/
- AbortController complete guide (LogRocket): https://blog.logrocket.com/complete-guide-abortcontroller/
- AbortController in-flight cancellation (OpenReplay): https://blog.openreplay.com/cancelling-in-flight-fetch-abortcontroller/
- JavaScript modules 2025 (Medium): https://siddsr0015.medium.com/javascript-modules-in-2025-esm-import-maps-best-practices-7b6996fa8ea3
- JavaScript modules (MDN): https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules
- Vite vs Webpack 2025 (LogRocket): https://blog.logrocket.com/vite-vs-webpack-react-apps-2025-senior-engineer/
- Vite vs Webpack 2026 benchmarks (Tech Insider): https://tech-insider.org/vite-vs-webpack-2026/
- innerHTML vs createElement (JavaScriptTutorial): https://www.javascripttutorial.net/javascript-dom/javascript-innerhtml-vs-createelement/
- innerHTML alternatives (Envato Tuts+): https://code.tutsplus.com/the-downside-of-using-innerhtml-to-manipulate-the-dom-and-some-alternatives--cms-106722a
- Event delegation deep dive (LogRocket): https://blog.logrocket.com/deep-internals-event-delegation/
- MutationObserver 2025 best practices: https://martinuke0.github.io/posts/2025-12-17-mutationobserver-the-modern-way-to-watch-and-react-to-dom-changes/
- AbortController MDN: https://developer.mozilla.org/en-US/docs/Web/API/AbortController/abort
- Fetch abort (JavaScript.info): https://javascript.info/fetch-abort
- Constraint Validation API guide (DEV): https://dev.to/itxshakil/the-definitive-guide-to-the-constraint-validation-api-3l80
- Client-side form validation (MDN): https://developer.mozilla.org/en-US/docs/Learn_web_development/Extensions/Forms/Form_validation
- Accessible form validation 2025 (Pope Tech): https://blog.pope.tech/2025/09/30/accessible-form-validation-with-examples-and-code/
- Vitest vs Jest 2025 (Medium): https://medium.com/@ruverd/jest-vs-vitest-which-test-runner-should-you-use-in-2025-5c85e4f2bda9
- Vitest vs Jest (Better Stack): https://betterstack.com/community/guides/scaling-nodejs/vitest-vs-jest/
- Vitest vs Jest 2026 benchmarks (SitePoint): https://www.sitepoint.com/vitest-vs-jest-2026-migration-benchmark/
- ESLint 9 flat config (DEV): https://dev.to/aolyang/eslint-9-flat-config-tutorial-2bm5
- ESLint 9 + Vite + Prettier setup (DEV): https://dev.to/leon740/setup-eslint-prettier-husky-with-vite-860
- Modern linting 2025 (Advanced Frontends): https://advancedfrontends.com/eslint-flat-config-typescript-javascript/

---

## ES2024 Features (stable — use by default)

```js
// Object.groupBy — Chrome 117+, Firefox 119+, Safari 17+
const byStatus = Object.groupBy(users, u => u.status);
// { active: [...], inactive: [...] }

// Promise.withResolvers — cleaner deferred promise pattern
const { promise, resolve, reject } = Promise.withResolvers();
setTimeout(() => resolve('done'), 1000);
await promise;

// Set methods — union, intersection, difference, symmetricDifference
const a = new Set([1, 2, 3]);
const b = new Set([2, 3, 4]);
a.union(b);        // Set {1, 2, 3, 4}
a.intersection(b); // Set {2, 3}
a.difference(b);   // Set {1}

// Array.prototype.toSorted / toReversed / with — non-mutating copies
const sorted = [3,1,2].toSorted();   // [1,2,3] — original unchanged
const flipped = [1,2,3].toReversed(); // [3,2,1] — original unchanged
```

**Require polyfill — always flag:**
- Temporal API (Stage 4, Firefox 139+ only — use `@js-temporal/polyfill`)
- Decorators (Stage 3 — use Babel or TypeScript transpilation)

---

## Security Patterns

### innerHTML — never with user data
```js
// WRONG — XSS vulnerability
div.innerHTML = userInput;

// RIGHT — user content as text only
div.textContent = userInput;

// RIGHT — building elements safely
const p = document.createElement('p');
p.textContent = userInput;
container.appendChild(p);

// RIGHT — third-party HTML (e.g. CMS rich text) — sanitise first
import DOMPurify from 'dompurify'; // pin to latest: npm install dompurify@latest
div.innerHTML = DOMPurify.sanitize(thirdPartyHtml);
// Keep DOMPurify updated — CVEs in 2024-2025 (2.5.4+ / 3.1.3+)
```

### eval() and new Function()
```js
// NEVER USE — remote code execution
eval(userInput);
new Function(userInput)();

// If you need dynamic code: use a data-driven approach instead
const actions = { greet: () => 'Hello', bye: () => 'Goodbye' };
const result = actions[userInput]?.(); // safe — limited to defined actions
```

### Prototype Pollution
```js
// NEVER merge untrusted objects into your own objects directly
Object.assign(target, untrustedObject); // dangerous if untrustedObject has __proto__

// Safe pattern — validate keys before merging
function safeMerge(target, source) {
    for (const key of Object.keys(source)) {
        if (key === '__proto__' || key === 'constructor' || key === 'prototype') continue;
        target[key] = source[key];
    }
    return target;
}

// Or use structuredClone for deep copies of trusted data
const copy = structuredClone(data);
```

### CSP script-src
```
# Nonce approach (preferred — regenerate per request)
Content-Security-Policy: script-src 'self' 'nonce-{random}' 'strict-dynamic'

# Never use:
script-src 'unsafe-inline'  ← defeats XSS protection
script-src 'unsafe-eval'    ← required by eval() — never use eval
```

---

## Async Patterns

```js
// Parallel operations — use Promise.all, NOT sequential await
// WRONG — sequential (3x slower)
const a = await fetchA();
const b = await fetchB();
const c = await fetchC();

// RIGHT — parallel
const [a, b, c] = await Promise.all([fetchA(), fetchB(), fetchC()]);

// Fault-tolerant parallel — allSettled waits for all, doesn't fail fast
const results = await Promise.allSettled([fetchA(), fetchB(), fetchC()]);
const values = results
    .filter(r => r.status === 'fulfilled')
    .map(r => r.value);

// Fetch with timeout — AbortSignal.timeout() (ES2024, no setTimeout needed)
// Plain English: AbortController lets us cancel this request if it takes too long
async function fetchWithTimeout(url, ms = 5000) {
    try {
        const response = await fetch(url, {
            signal: AbortSignal.timeout(ms),
        });
        if (!response.ok) {
            throw new Error(`HTTP error: ${response.status}`);
        }
        return await response.json();
    } catch (err) {
        if (err.name === 'TimeoutError') {
            throw new Error('Request timed out');
        }
        if (err.name === 'AbortError') {
            throw new Error('Request cancelled');
        }
        throw err; // re-throw network/parse errors
    }
}

// Fetch error handling — network errors vs HTTP errors are different
const response = await fetch(url);
// response.ok is false for 4xx/5xx — fetch does NOT throw on HTTP errors
if (!response.ok) throw new Error(`HTTP ${response.status}`);
```

---

## DOM Patterns

```js
// Build DOM safely — createElement + DocumentFragment (faster + safer than innerHTML)
// Plain English: DocumentFragment builds the elements in memory before adding to the page,
// which is faster and avoids security risks from innerHTML
function buildList(items) {
    const fragment = document.createDocumentFragment();
    for (const item of items) {
        const li = document.createElement('li');
        li.textContent = item.name; // textContent — never innerHTML for user data
        fragment.appendChild(li);
    }
    return fragment;
}
document.querySelector('ul').appendChild(buildList(items));

// Event delegation — one listener on the parent handles all children
// Plain English: instead of adding a click listener to every button,
// add one listener to the container and check which button was clicked
document.querySelector('#list').addEventListener('click', (e) => {
    const btn = e.target.closest('[data-action]');
    if (!btn) return;
    const action = btn.dataset.action;
    handlers[action]?.(btn);
});

// MutationObserver — watch for DOM changes (Mutation Events removed Chrome 127+ July 2024)
const observer = new MutationObserver((mutations) => {
    for (const mutation of mutations) {
        if (mutation.type === 'childList') {
            // handle added/removed nodes
        }
    }
});
observer.observe(container, { childList: true, subtree: false });
// Always disconnect when done — observer.disconnect()
```

---

## Form Validation Pattern

```js
// Constraint Validation API + accessibility layer
const form = document.querySelector('#contact-form');

form.addEventListener('submit', (e) => {
    e.preventDefault();

    // Clear previous errors
    form.querySelectorAll('[aria-describedby]').forEach(field => {
        const errorEl = document.getElementById(field.getAttribute('aria-describedby'));
        if (errorEl) errorEl.textContent = '';
        field.removeAttribute('aria-invalid');
    });

    if (!form.checkValidity()) {
        // Report errors accessibly
        const firstInvalid = form.querySelector(':invalid');
        showError(firstInvalid);
        firstInvalid.focus();
        return;
    }

    // Submit
});

function showError(field) {
    // aria-invalid announces the field as invalid to screen readers
    field.setAttribute('aria-invalid', 'true');
    const errorId = field.getAttribute('aria-describedby');
    if (errorId) {
        const errorEl = document.getElementById(errorId);
        if (errorEl) {
            // role="alert" announces the message immediately
            errorEl.textContent = field.validationMessage || 'This field is required';
        }
    }
}
```

---

## SETUP Mode — Project Scaffold

### Terminal commands (run in order)
```bash
# 1. Create Vite project (vanilla JS — no framework)
npm create vite@latest my-project -- --template vanilla
cd my-project
npm install

# 2. Add development tools
npm install -D vitest eslint prettier
npm install -D @eslint/js eslint-config-prettier

# 3. Run dev server
npm run dev
```

### eslint.config.js (ESLint 9 flat config — replaces .eslintrc)
```js
import js from '@eslint/js';
import prettierConfig from 'eslint-config-prettier';

export default [
    js.configs.recommended,
    prettierConfig,
    {
        rules: {
            'no-eval': 'error',
            'no-implied-eval': 'error',
            'no-new-func': 'error',
            'no-proto': 'error',
        },
    },
];
```

### .prettierrc
```json
{
    "singleQuote": true,
    "semi": true,
    "tabWidth": 4,
    "trailingComma": "es5"
}
```

### vitest.config.js
```js
import { defineConfig } from 'vitest/config';

export default defineConfig({
    test: {
        environment: 'jsdom',
        globals: true,
    },
});
```

---

## TEST Mode — Vitest Basics

```js
// src/utils/format.test.js — co-located with the module it tests
import { describe, it, expect, vi, beforeEach } from 'vitest';
import { formatCurrency, fetchUser } from './format.js';

describe('formatCurrency', () => {
    it('formats cents as dollars', () => {
        expect(formatCurrency(1999)).toBe('$19.99');
    });

    it('handles zero', () => {
        expect(formatCurrency(0)).toBe('$0.00');
    });
});

describe('fetchUser', () => {
    beforeEach(() => vi.restoreAllMocks());

    it('returns user on success', async () => {
        // vi.fn() mocks a function — vi.spyOn() for existing object methods
        global.fetch = vi.fn().mockResolvedValue({
            ok: true,
            json: () => Promise.resolve({ id: 1, name: 'Alice' }),
        });

        const user = await fetchUser(1);
        expect(user.name).toBe('Alice');
    });

    it('throws on HTTP error', async () => {
        global.fetch = vi.fn().mockResolvedValue({ ok: false, status: 404 });
        await expect(fetchUser(1)).rejects.toThrow('HTTP 404');
    });
});
```

Run: `npx vitest` (watch mode) or `npx vitest run` (single pass)

---

## REVIEW Checklist

Run on every REVIEW call. Report: PASS / WARN / FAIL.

### Both npm and standalone projects
| # | Check | What to look for |
|---|---|---|
| 1 | No innerHTML with user data | Search for `innerHTML =` — confirm source is trusted or DOMPurify used |
| 2 | No eval() | Search for `eval(`, `new Function(`, `setTimeout(string` |
| 3 | No document.write() | Deprecated + XSS risk |
| 4 | Prototype pollution | Check Object.assign, lodash merge with untrusted input; no `__proto__` keys |
| 5 | Fetch error handling | Every fetch checks both catch AND response.ok |
| 6 | No Mutation Events | No `DOMNodeInserted`, `DOMNodeRemoved` — removed Chrome 127+ |
| 7 | CSP compatible | No inline event handlers (onclick=); no eval() required by code |

### npm projects only
| # | Check | What to look for |
|---|---|---|
| 8 | npm audit | Run `npm audit` — FAIL if high/critical vulnerabilities |
| 9 | DOMPurify version | If used: confirm version 2.5.4+ or 3.1.3+ (earlier versions have CVEs) |
| 10 | No unused packages | Large `node_modules` footprint; remove unused deps |

### Standalone (script tags) only
| # | Check | What to look for |
|---|---|---|
| 8 | SRI on CDN scripts | Every `<script src="https://cdn...">` has `integrity` + `crossorigin` attributes |
| 9 | No CDN scripts from unknown sources | Verify CDN provider reputation |
