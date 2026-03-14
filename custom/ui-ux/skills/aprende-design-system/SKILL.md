---
name: aprende-design-system
description: >
  Complete guide for working on the aprende comigo. landing repo at /Users/anapmc/Code/landing — design tokens, kit.css components, page block patterns, React components, Tailwind v4 theme, and design guidelines documentation. Use this skill whenever adding or styling components, writing kit.css rules, building landing page sections, updating design-guidelines HTML, checking brand compliance, choosing colour tokens, following typography or spacing rules, or implementing any UI pattern. Trigger phrases include: "add a component", "what token should I use", "style this section", "follow the design system", "add to design guidelines", "build a page block", "check the brand rules", "what colour is", "how do buttons work", "arc signature", "kit.css", "landing page design", "React component", "design guidelines".
metadata:
  author: aprende-comigo
  version: "1.0.0"
---

# aprende comigo. Design System

Working directory: `/Users/anapmc/Code/landing`

---

## File Map — Read These, Don't Guess

| What you need | Read this file |
|---|---|
| Colour, spacing, shadow, radius, z-index tokens | `src/css/tokens.css` |
| All shared component classes (buttons, cards, badges, nav, arc, etc.) | `src/css/kit.css` |
| Tailwind v4 `@theme` — brand colour utilities, font utilities, keyframes | `src/css/app.css` |
| Component docs with live previews + HTML examples | `design-guidelines/components.html` |
| Full landing page section patterns (Tailwind v4 code blocks) | `design-guidelines/page-blocks.html` |
| Brand rules — colours, gradients, typography, do's/don'ts, accessibility | `design-guidelines/brand.html` |
| Project setup — CSS load order, Tailwind config | `design-guidelines/setup.html` |
| React components (live + copy-ready source) | `design-guidelines/react-preview.html` |
| SearchableTable source | `src/components/SearchableTable.tsx` |
| AppCalendar source | `src/components/AppCalendar.tsx` |
| UpcomingEvents source | `src/components/UpcomingEvents.tsx` |
| React app shell — how components are composed and rendered | `src/PreviewApp.tsx` |

---

## Rules Not Obvious From Reading Code

These are architectural decisions — apply them even when the code nearby doesn't remind you.

**All shared CSS lives in `kit.css`.** Never add component styles to a page `<style>` block. Design-guidelines pages may only have docs-specific overrides (e.g. `.code-pane`, tab badge) in their `<style>` block.

**Gradients always flow purple → orange.** Never reverse. This applies to the arc line, section backgrounds, CTA buttons, footer accent — all of them.

**Section backgrounds always use gradients, never solid colours.** Even a subtle one.

**The arc signature colour order is fixed.** Purple dot on the left, orange dot on the right. Never invert. Exception: inside `.plan.featured` it overrides to white — that's already in kit.css.

**FAQ accordion uses `max-height` transitions, never `display: none`.** This preserves ARIA and animation.

**Two fonts only — Fraunces (display) and Plus Jakarta Sans (body).** Never substitute, never introduce a third.

**Always reference tokens, never hardcode hex.** Even in one-off inline styles.

**react-big-calendar injects `.rbc-*` CSS after kit.css** in Vite's injection order — use `!important` when overriding those rules in kit.css.

---

## Adding a New React Component

1. Create `src/components/MyComponent.tsx`
2. Add styles to `src/css/kit.css`
3. In `src/PreviewApp.tsx`, import the component and its raw source:
   ```ts
   import MyComponent from './components/MyComponent'
   import MyComponentSrc from './components/MyComponent.tsx?raw'
   ```
   Then render both `<MyComponent />` and `<CodeBlock code={MyComponentSrc} />` in the relevant section.
4. If adding a new HTML page, register it in `vite.config.ts` rollup inputs.

## Adding to Design Guidelines (Static Pages)

Static pages (`components.html`, `page-blocks.html`) use a JS-driven 3-tab system. Wrap new examples in:

```html
<div class="comp-block">
  <div class="comp-preview">
    <!-- live visual demo -->
  </div>
  <pre class="code-block"><code><!-- copy-ready source --></code></pre>
</div>
```

The page JS auto-generates Preview / HTML tab buttons and copy buttons from this structure.
