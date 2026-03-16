# Accessibility (a11y) Standards

## Core Principle

**Every user should be able to use the application regardless of ability.** Accessibility is not optional — it's a quality requirement like security or performance.

Target: **WCAG 2.1 Level AA** compliance.

## Semantic HTML

Use the right element for the job. Semantic HTML gives you accessibility for free.

```html
<!-- GOOD — semantic, accessible by default -->
<button onClick={handleSubmit}>Submit</button>
<nav aria-label="Main navigation">...</nav>
<main>...</main>
<form onSubmit={handleSubmit}>...</form>
<a href="/courses">View courses</a>

<!-- BAD — div soup, requires manual ARIA -->
<div onClick={handleSubmit} class="button">Submit</div>
<div class="nav">...</div>
<div class="main-content">...</div>
<div onClick={() => navigate('/courses')}>View courses</div>
```

### Heading Hierarchy

```html
<!-- GOOD — logical hierarchy -->
<h1>Course Dashboard</h1>
  <h2>Active Courses</h2>
    <h3>Introduction to Psychology</h3>
  <h2>Completed Courses</h2>

<!-- BAD — skipping levels for styling -->
<h1>Course Dashboard</h1>
  <h4>Active Courses</h4>  <!-- Skipped h2, h3 -->
```

Never skip heading levels. Use CSS for visual sizing, not heading tags.

## Keyboard Navigation

### Requirements

- All interactive elements must be **focusable** and **operable** with keyboard
- **Tab order** must follow visual order (don't rearrange with `tabIndex > 0`)
- **Focus must be visible** — never remove focus outlines without replacement
- **Escape** should close modals, dropdowns, and popovers
- **Enter/Space** should activate buttons and links

### Focus Management

```typescript
// After opening a modal, move focus into it
function openModal() {
  setIsOpen(true);
  // Focus the first focusable element in the modal
  requestAnimationFrame(() => {
    modalRef.current?.querySelector<HTMLElement>('[tabindex="-1"], button, input')?.focus();
  });
}

// After closing, return focus to the trigger element
function closeModal() {
  setIsOpen(false);
  triggerRef.current?.focus();
}
```

### Focus Trap

Modals and dialogs must trap focus — Tab should cycle within the modal, not escape to the page behind it.

```typescript
// Use a library like @radix-ui/react-dialog or headlessui
// They handle focus trapping, Escape to close, and focus restoration
import * as Dialog from '@radix-ui/react-dialog';

<Dialog.Root>
  <Dialog.Trigger>Open</Dialog.Trigger>
  <Dialog.Portal>
    <Dialog.Overlay />
    <Dialog.Content>
      {/* Focus is automatically trapped here */}
    </Dialog.Content>
  </Dialog.Portal>
</Dialog.Root>
```

## Images and Media

### Images

```html
<!-- Informative image — describe the content -->
<img src="chart.png" alt="Bar chart showing enrollment increased 40% in Q3 2025" />

<!-- Decorative image — empty alt -->
<img src="divider.png" alt="" />

<!-- Complex image — use figure + figcaption -->
<figure>
  <img src="architecture.png" alt="System architecture diagram" />
  <figcaption>
    Three microservices communicate via message queue.
    The API Gateway routes requests to Course, User, and Analytics services.
  </figcaption>
</figure>
```

### Video and Audio

- Provide **captions** for all video content
- Provide **transcripts** for audio content
- Never autoplay media with sound
- Provide playback controls (play, pause, volume, seek)

## Forms

```html
<!-- GOOD — labeled, described, with error handling -->
<div>
  <label htmlFor="email">Email address</label>
  <input
    id="email"
    type="email"
    aria-describedby="email-hint email-error"
    aria-invalid={hasError}
    required
  />
  <p id="email-hint">We'll never share your email</p>
  {hasError && <p id="email-error" role="alert">Please enter a valid email</p>}
</div>

<!-- BAD — no label, no error association -->
<input type="email" placeholder="Email" />
<span class="error">Invalid email</span>
```

### Form Rules

- Every input must have a visible `<label>` (not just `placeholder`)
- Use `aria-describedby` for hints and error messages
- Use `aria-invalid` to indicate error state
- Use `role="alert"` for dynamic error messages (screen readers announce them)
- Group related inputs with `<fieldset>` and `<legend>`
- Mark required fields with `required` attribute and visual indicator

## Color and Contrast

- **Minimum contrast ratio**: 4.5:1 for normal text, 3:1 for large text (18px+ bold)
- **Don't rely on color alone** to convey information — add icons, text, or patterns
- Support `prefers-color-scheme` and `prefers-reduced-motion`

```css
/* Respect user preferences */
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
  }
}

@media (prefers-color-scheme: dark) {
  :root {
    --bg: #1a1a2e;
    --text: #e0e0e0;
  }
}
```

### Status Indicators

```html
<!-- BAD — color only -->
<span style="color: red">Failed</span>
<span style="color: green">Passed</span>

<!-- GOOD — color + icon + text -->
<span class="status-failed">&#10007; Failed</span>
<span class="status-passed">&#10003; Passed</span>
```

## ARIA (When HTML Isn't Enough)

Use ARIA only when native HTML elements can't do the job.

| Pattern | ARIA |
|---------|------|
| Custom dropdown | `role="listbox"`, `role="option"`, `aria-expanded` |
| Tabs | `role="tablist"`, `role="tab"`, `role="tabpanel"`, `aria-selected` |
| Toast/notification | `role="alert"` or `aria-live="polite"` |
| Loading state | `aria-busy="true"`, `aria-live="polite"` |
| Icon-only button | `aria-label="Close dialog"` |

### Rules

- **First rule of ARIA**: Don't use ARIA if native HTML works
- **Don't change semantics**: `<button role="link">` — just use `<a>`
- **Keep ARIA states updated**: If `aria-expanded="true"`, update it when collapsed
- **Test with a screen reader**: ARIA attributes are only useful if they work in practice

## Testing

### Automated Testing

```bash
# Axe — catches ~30% of accessibility issues
npm install -D @axe-core/playwright  # for Playwright
npm install -D jest-axe              # for Jest/Vitest

# Lighthouse — includes accessibility audit
npx lighthouse http://localhost:3000 --only-categories=accessibility
```

```typescript
// Playwright accessibility test
import AxeBuilder from '@axe-core/playwright';

test('course page has no accessibility violations', async ({ page }) => {
  await page.goto('/courses');
  const results = await new AxeBuilder({ page }).analyze();
  expect(results.violations).toEqual([]);
});
```

### Manual Testing Checklist

- [ ] Navigate the entire page using only keyboard (Tab, Enter, Escape, Arrow keys)
- [ ] Test with a screen reader (VoiceOver on macOS, NVDA on Windows)
- [ ] Zoom to 200% — is the layout still usable?
- [ ] Check color contrast ratios with browser DevTools
- [ ] Disable CSS — does the content still make sense?

## Anti-Patterns

- **`outline: none` without replacement** — Users can't see where focus is
- **Placeholder as label** — Disappears when typing, not announced by all screen readers
- **Click handlers on divs** — Use `<button>` or `<a>`, not `<div onClick>`
- **Images without alt text** — Screen readers say "image" with no context
- **Autoplaying media** — Disorienting and disruptive for many users
- **Infinite scroll without alternative** — Keyboard users can never reach the footer
