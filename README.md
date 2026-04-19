# Todo Item Card

A self-contained, interactive task card component built with vanilla HTML, CSS, and JavaScript. No dependencies, no build step.

---

### Live URL

```
https://advanced-todo-card-interactive-and.vercel.app
```

## How to Run Locally

### Option 1 — Clone the repo

```bash
git clone https://github.com/UkannaRaymond/Advanced-Todo-Card--Interactive---Stateful-.git
cd todo-item-card
```

Then open the file directly in your browser:

```
open todo-item-card.html        # macOS
start todo-item-card.html       # Windows
xdg-open todo-item-card.html    # Linux
```

Or serve it locally to avoid any file-protocol quirks:

```bash
npx serve .
# then visit http://localhost:3000
```

### Option 2 — Download manually

Download both files into the same directory:

- `todo-item-card.html`
- `todo-item-card.css`

Then open `todo-item-card.html` in any modern browser.

No npm install, no bundler, no framework.

---

## What Changed from Stage 0

### New Features

**Collapsible description.** A 10-word truncated preview is always visible beneath the title. A "Show more / Show less" toggle expands the full description using a smooth CSS `max-height` transition — no JavaScript-driven height calculation.

**Inline edit form.** Clicking "Edit" reveals a form inside the card for updating the title, description, priority, and due date. The form appears in context rather than in a modal or a separate view, and is dismissed with "Save", "Cancel", or the `Escape` key.

**Live countdown timer.** A `setInterval` running every 45 seconds recalculates and re-renders the time-remaining display continuously while the page is open. The urgency class updates automatically as time passes.

**Overdue state.** When the due date passes, a red "Overdue" pill badge appears, the time-remaining text switches to a red color class, and the card gains a red border tint. All three update reactively on the next timer tick.

**Edit snapshot and cancel.** Opening the edit form captures a plain-object snapshot of the current title, description, priority, and due date. Cancelling restores from the snapshot cleanly without re-fetching or diffing.

**Delete animation.** Before removing the card from the DOM, the delete handler fades the card to `opacity: 0` and scales it to `0.97`, giving the removal a physical feel.

### Changed Behaviour

- Priority updates now apply simultaneously across all three visual signals — the left-border accent stripe, the badge background/text color, and the dot indicator — whereas Stage 0 treated priority as a static initial value.
- The completion checkbox and the status segmented control are now kept in sync bidirectionally. Ticking the checkbox calls `setStatus('done')`; setting status to "done" via the segmented control checks the checkbox. Stage 0 had these as independent states.
- The description preview and the full collapsible text are now updated together on save, fixing a Stage 0 inconsistency where editing could leave the two out of sync.
- The edit form now moves focus to the title input on open and returns focus to the Edit button on close.

---

## Features

| Interaction               | Behaviour                                                                                                                                |
| ------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| **Checkbox**              | Marks card as complete — fades the card, strikes through the title, flips the status badge and segmented control to "Done"               |
| **Show more / Show less** | Expands or collapses the full task description with a CSS transition                                                                     |
| **Edit**                  | Opens an inline form to update title, description, priority, and due date; Save writes changes, Cancel restores the snapshot             |
| **Delete**                | Confirms via native dialog, then fades and scales the card out before removing it from the DOM                                           |
| **Due date & timer**      | Calculated relative to page-load time (`now + ~3 days 5 hours`); refreshes every 45 seconds; turns amber when due soon, red when overdue |
| **Dark mode**             | Automatic via `prefers-color-scheme: dark` — no toggle needed                                                                            |

---

## Design Decisions

### Vanilla stack

The card is a single HTML file with an external stylesheet and an inline script block. No framework, no build tool, no package manager required. This keeps it portable as a reference implementation and easy to drop into any project regardless of stack.

### CSS-only dark mode via `prefers-color-scheme`

Dark mode is handled entirely in a CSS media query. It respects the OS preference immediately on load with zero flash of the wrong theme and requires no JavaScript state management or localStorage.

### Triple-redundant priority signals

Priority is communicated through three simultaneous signals: the 4px left-border stripe on the card, the colored dot inside the priority badge, and the badge background/text color. The redundancy ensures priority remains legible in high-contrast mode, with color-blindness filters applied, or in contexts where only one signal is visible at a time.

### `data-testid` attributes on every interactive element

Every meaningful element carries a `data-testid` (e.g. `test-todo-complete-toggle`, `test-todo-edit-button`). These are dedicated test-targeting hooks, intentionally decoupled from styling classes so that automated tests remain stable across visual refactors.

### Segmented status control

Status uses a segmented button group rather than a dropdown. Surfacing all three states (Pending / In Progress / Done) at once reduces interaction cost for a field that changes frequently, and `role="group"` with `aria-pressed` per button makes the current state machine-readable without inspecting CSS classes.

### Collapsible description via CSS `max-height`

The full description is always present in the DOM — hidden via `max-height: 0` and `opacity: 0` when collapsed — which keeps it accessible to search engines and screen readers regardless of expanded state. CSS transitions handle the animation without any JavaScript-driven height measurement.

### Snapshot-and-cancel edit pattern

`openEdit()` captures a plain object snapshot before revealing the form. Cancel discards any in-progress input and closes the form — no diffing, no re-render, no additional state layer. This keeps the cancel path cheap and predictable.

### In-place edit form

The edit form slides in within the card itself rather than opening a modal. This avoids disrupting surrounding list context and keeps the user oriented. The form uses a slightly off-white background (`#faf9f7`) to visually separate it from the card surface without a heavy border.

### Relative due-date calculation

The due date is derived from `Date.now()` at page load rather than a data source. This keeps the demo self-contained and always renders a meaningful "due in N days" state without requiring a backend, localStorage, or data props.

---

## Known Limitations

- **Single card only.** There is no list container, drag-to-reorder, or multi-card state management. All state is scoped to one card instance.
- **No persistence.** State lives in JavaScript variables and resets on every page refresh.
- **Hardcoded initial data.** Title, description, tags, and the due-date offset are written directly into the HTML and the `openEdit()` snapshot fallback string. A real integration would pass these in via data attributes or a JavaScript initializer.
- **Word-count description truncation.** The 10-word preview cutoff does not account for very long individual words or languages that do not use spaces as word delimiters (CJK, Thai, etc.), which can produce a short or empty preview.
- **Timer interval is never paused.** The 45-second `setInterval` runs for the full page lifetime and is not cleaned up when the card is deleted. In a list of many cards, accumulated intervals could become a performance concern.
- **Delete is irreversible.** The card is removed from the DOM with no undo. The `confirm()` dialog provides one confirmation step, but there is no toast or snackbar undo affordance.
- **Tags are static.** Tags are hard-coded in HTML and cannot be added, removed, or edited through the UI.
- **No input validation beyond the browser's native `datetime-local` enforcement.** Empty titles fall back silently to "Untitled task"; no length limits or format checks are applied to other fields.
- **Google Fonts dependency.** Typography degrades to system sans-serif if the CDN is unreachable (e.g., offline environments or networks that block Google domains).

---

## Accessibility Notes

### What is implemented

- **Labelled checkbox.** The complete toggle uses a visually hidden `<label>` (`.sr-only`) linked by `for`/`id`, giving screen readers the text "Mark task as complete."
- **`aria-expanded` and `aria-controls` on the expand toggle.** The button declares its open/closed state and the `id` of the region it controls. Both attributes stay in sync with DOM state on every click.
- **`aria-hidden` on the collapsible section.** The collapsed region is removed from the accessibility tree when not expanded, preventing screen readers from encountering unreachable content.
- **Status badge `aria-label`.** The status badge carries a human-readable `aria-label` (e.g. `"Status: In Progress"`) updated programmatically whenever status changes, since the badge text alone could be ambiguous outside of visual context.
- **`aria-pressed` on segmented status buttons.** Each button in the control carries `aria-pressed="true/false"`, making the active state discoverable without relying on CSS class inspection.
- **`role="group"` with `aria-label` on the status control.** The segmented control is a labelled group so screen reader users hear "Task status" before the individual buttons.
- **Edit form `role="form"` with `aria-label`.** The edit panel is a named form landmark ("Edit task"), discoverable via landmark navigation.
- **All edit fields have explicit `<label>` elements** linked by `for`/`id`. Placeholder text supplements but does not replace labels.
- **Focus management.** Opening the edit form moves focus to the title input. Closing the form returns focus to the Edit button, preventing loss of document position.
- **`Escape` key closes the edit form.** A document-level `keydown` listener fires `cancelEdit()` on `Escape` whenever `isEditing` is true.
- **`aria-live="polite"` on the overdue indicator.** When the overdue pill becomes visible, the change is announced to screen readers without interrupting ongoing speech.
- **`aria-hidden="true"` on decorative icons.** The expand chevron (▾) and emoji icons (📅, ⏱) are marked presentational so they are skipped by screen readers.
- **`datetime` attribute on `<time>` elements.** Both the due-date and time-remaining elements carry a machine-readable ISO string that assistive technologies and browser extensions can parse independently of the visible text.
- **Keyboard support on expand toggle.** An explicit `keydown` handler fires the toggle on `Enter` and `Space` in addition to native `click`.

### Known gaps

- **Color as the sole signal on the left border stripe.** The border accent is purely color-differentiated with no pattern or text alternative. Priority is still conveyed via the badge text, so no information is technically lost, but users in high-contrast mode may lose the border signal.
- **No save-confirmation announcement.** After saving edits there is no live-region message confirming success. A screen reader user would infer the change from the updated card content, but an explicit status message would be clearer.
- **Delete uses `window.confirm`.** The native dialog is accessible but unstyled and may be suppressed in some browser environments. A custom `role="alertdialog"` would provide more control.
- **Tags carry no interactive semantics.** Tag elements are informational only. If tag-based filtering is introduced in a future stage, they will need `role="button"` or `role="checkbox"` plus keyboard event handling.
- **No announcement on urgency class changes.** The live countdown re-renders silently every 45 seconds. Transitions between urgency states (neutral → "soon" → "overdue") are not announced — adding `aria-live` to the time-remaining element would surface these changes but may be too noisy in a multi-card list, a trade-off pending further UX review.

---

👤 **Author**

Ukanna Raymond | Frontend | Backend

This project is part of my portfolio, showcasing frontend skills for a fullstack role. If you have questions, feedback, or would like to collaborate, feel free to get in touch!
