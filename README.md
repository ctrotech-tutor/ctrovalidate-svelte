# ctrovalidate-svelte

**Reactive form validation for Svelte.**

`ctrovalidate-svelte` provides a `useCtrovalidate` function that wraps [`ctrovalidate-core`](https://www.npmjs.com/package/ctrovalidate-core)'s validation engine with Svelte stores. Returns `writable` and `derived` stores for values, errors, dirty state, and async validation status.

[![npm version](https://img.shields.io/npm/v/ctrovalidate-svelte.svg)](https://www.npmjs.com/package/ctrovalidate-svelte)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)

---

## Installation

```bash
npm install ctrovalidate-svelte ctrovalidate-core
```

**Requirements:** Svelte >=3.0.0

---

## Quick Start

```svelte
<script lang="ts">
  import { useCtrovalidate } from 'ctrovalidate-svelte';

  const { values, errors, handleChange, handleBlur, validateForm, isValidating } =
    useCtrovalidate<{ email: string; password: string }>({
      initialValues: { email: '', password: '' },
      schema: { email: 'required|email', password: 'required|minLength:8' },
    });
</script>

<form on:submit|preventDefault={async () => { if (await validateForm()) { /* submit */ } }}>
  <input value={$values.email} on:input={(e) => handleChange('email', e.target.value)} on:blur={() => handleBlur('email')} />
  {#if $errors.email}<span>{$errors.email}</span>{/if}

  <input type="password" value={$values.password} on:input={(e) => handleChange('password', e.target.value)} on:blur={() => handleBlur('password')} />
  {#if $errors.password}<span>{$errors.password}</span>{/if}

  <button disabled={$isValidating.email || $isValidating.password}>
    {$isValidating.email || $isValidating.password ? 'Validating...' : 'Login'}
  </button>
</form>
```

---

## API

```typescript
const {
  values,         // Writable<T>
  errors,         // Writable<Partial<Record<keyof T, string>>>
  isDirty,        // Writable<Partial<Record<keyof T, boolean>>>
  isValidating,   // Writable<Partial<Record<keyof T, boolean>>>
  isValid,        // Readable<boolean> — derived from errors
  handleChange,   // (name, value) => void — updates value, marks dirty, validates
  handleBlur,     // (name) => void — marks dirty, validates
  validateField,  // (name, value?) => Promise<boolean>
  validateForm,   // () => Promise<boolean>
  reset,          // (newValues?) => void
} = useCtrovalidate<T>({
  schema,              // Required
  initialValues,       // Optional (default: {})
  validateOnBlur,      // Optional (default: true)
  validateOnChange,    // Optional (default: true)
  customRules,         // Optional
  aliases,             // Optional
  messages,            // Optional
  locale,              // Optional
});
```

---

## Behavior

| Call | Marks dirty | Validates |
|------|-------------|-----------|
| `handleChange(name, value)` | Yes | If `validateOnChange` |
| `handleBlur(name)` | Yes | If `validateOnBlur` |

- `validateField` aborts any in-flight async rule for the same field
- All `AbortController`s are aborted on `onDestroy`
- `isValid` is a `derived` store: `true` when no error string is truthy
- `reset()` re-initializes all stores and clears validation state

---

## Related Packages

- **[ctrovalidate-core](https://www.npmjs.com/package/ctrovalidate-core)** — Validation engine
- **[ctrovalidate-browser](https://www.npmjs.com/package/ctrovalidate-browser)** — Vanilla JS DOM integration
- **[ctrovalidate-react](https://www.npmjs.com/package/ctrovalidate-react)** — React hook
- **[ctrovalidate-vue](https://www.npmjs.com/package/ctrovalidate-vue)** — Vue composable
- **[ctrovalidate-next](https://www.npmjs.com/package/ctrovalidate-next)** — Next.js server actions

---

## License

MIT © [Ctrotech](https://github.com/ctrotech-tutor)

Full documentation: https://ctrovalidate.vercel.app
