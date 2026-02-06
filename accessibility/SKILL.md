---
name: accessibility
description: Web accessibility checklist covering WCAG guidelines, ARIA patterns, keyboard navigation, color contrast, screen reader support, and form accessibility. Use when building UI components, reviewing frontend code, or when accessibility is mentioned.
metadata:
  version: "1.0"
---

# Accessibility

## Decision Tree

```
Building UI → What needs a11y attention?
    ├─ Interactive element (button, link, form) → Keyboard + screen reader + focus
    ├─ Image or icon → Alt text or aria-hidden
    ├─ Custom component (dropdown, modal, tabs) → ARIA roles + keyboard patterns
    ├─ Color or styling → Contrast + not color-only indicators
    ├─ Dynamic content (toast, live region) → aria-live announcements
    └─ Full page/app audit → Run checklist below
```

## WCAG Quick Reference (Level AA)

| Principle | Requirement | Check |
|-----------|------------|-------|
| **Perceivable** | Text alternatives for images | All `<img>` have meaningful `alt` (or `alt=""` if decorative) |
| | Color contrast | 4.5:1 for normal text, 3:1 for large text |
| | Don't rely on color alone | Use icons, patterns, or text alongside color |
| | Captions for video | Provide captions and transcripts |
| **Operable** | Keyboard accessible | Every interactive element reachable and operable via keyboard |
| | Visible focus indicator | `:focus-visible` styles on all interactive elements |
| | No keyboard traps | User can always Tab out of any component |
| | Skip navigation | "Skip to main content" link as first focusable element |
| **Understandable** | Page language set | `<html lang="en">` |
| | Form labels | Every input has a visible `<label>` with `for` attribute |
| | Error identification | Errors described in text, not just red border |
| **Robust** | Valid HTML | Proper heading hierarchy (h1→h2→h3, no skipping) |
| | ARIA used correctly | Only when native HTML can't do it |

## Keyboard Patterns

| Component | Expected Keyboard Behavior |
|-----------|--------------------------|
| Button | `Enter` or `Space` activates |
| Link | `Enter` navigates |
| Modal/Dialog | `Escape` closes, focus trapped inside, returns focus on close |
| Dropdown/Menu | `Arrow keys` navigate, `Enter` selects, `Escape` closes |
| Tabs | `Arrow keys` switch tabs, `Tab` moves to panel content |
| Checkbox | `Space` toggles |
| Form | `Tab` moves between fields, `Enter` submits |

## ARIA — Only When Needed

```
Rule #1: Don't use ARIA if native HTML works.
  <button> not <div role="button">
  <nav> not <div role="navigation">
  <input type="checkbox"> not <div role="checkbox">
```

### When ARIA IS needed

| Pattern | ARIA |
|---------|------|
| Custom dropdown | `role="listbox"`, `role="option"`, `aria-expanded`, `aria-activedescendant` |
| Modal dialog | `role="dialog"`, `aria-modal="true"`, `aria-labelledby` |
| Tabs | `role="tablist"`, `role="tab"`, `role="tabpanel"`, `aria-selected` |
| Toast/notification | `role="alert"` or `aria-live="polite"` |
| Loading state | `aria-busy="true"`, `aria-live="polite"` with status text |
| Icon button | `aria-label="Close"` (no visible text) |
| Progress | `role="progressbar"`, `aria-valuenow`, `aria-valuemin`, `aria-valuemax` |

## Form Accessibility

```html
<!-- Always: visible label linked to input -->
<label for="email">Email address</label>
<input id="email" type="email" required aria-describedby="email-error">
<span id="email-error" role="alert">Please enter a valid email</span>

<!-- Never: placeholder as the only label -->
<input placeholder="Email"> <!-- Screen reader can't reliably read this -->
```

**Checklist:**
- [ ] Every input has a `<label>` with `for` attribute
- [ ] Required fields indicated (not just by color)
- [ ] Error messages linked via `aria-describedby`
- [ ] Error messages use `role="alert"` for screen reader announcement
- [ ] Submit button has clear text (not just an icon)
- [ ] Autocomplete attributes set (`autocomplete="email"`, etc.)

## Color Contrast

| Element | Minimum Ratio (AA) | Tools |
|---------|-------------------|-------|
| Normal text (<18px) | 4.5:1 | WebAIM Contrast Checker |
| Large text (≥18px bold or ≥24px) | 3:1 | Chrome DevTools |
| UI components (borders, icons) | 3:1 | Figma a11y plugins |

**Don't rely on color alone:**
```
BAD:  Red text for errors (colorblind users can't see it)
GOOD: Red text + error icon + descriptive message
```

## Testing

```bash
# Automated (catches ~30% of issues)
npx axe-core-cli http://localhost:3000   # axe accessibility scanner
npx pa11y http://localhost:3000           # WCAG compliance checker

# Chrome DevTools
# Lighthouse → Accessibility audit
# Elements → Accessibility pane (inspect ARIA tree)
```

**Manual testing (catches the rest):**
- Tab through the entire page — can you reach and operate everything?
- Turn off CSS — does the content still make sense?
- Use a screen reader (VoiceOver on Mac, NVDA on Windows)
- Zoom to 200% — does the layout break?

## Anti-Patterns

| Anti-Pattern | Fix |
|-------------|-----|
| `<div onclick>` instead of `<button>` | Use semantic HTML elements |
| Missing alt text on images | Add `alt="description"` or `alt=""` if decorative |
| Removing focus outlines (`:focus { outline: none }`) | Style `:focus-visible` instead of removing |
| `tabindex="5"` (positive tabindex) | Use `tabindex="0"` or `-1` only |
| `aria-label` on elements that have visible text | Remove — screen readers read visible text |
| Color-only error indication | Add text + icon alongside color |
| Auto-playing media | Never autoplay, or provide immediate stop control |
| Missing skip navigation | Add "Skip to main content" as first link |
| Custom components without keyboard support | Implement full keyboard pattern |
| `role="button"` without Enter/Space handling | Use native `<button>` instead |
