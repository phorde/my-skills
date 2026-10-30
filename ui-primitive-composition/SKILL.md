---
name: ui-primitive-composition
description: Enforce design-system-first component assembly. Prioritize existing UI primitives (shadcn/ui, Radix, Lucide icons, standard Tailwind semantic tokens) over custom-made markup. Use whenever creating or updating user interfaces, frontend screens, forms, dialogs, or interactive components.
---

# UI Primitive Composition (Design-System First)

Assemble user interfaces by composing established, accessible design system primitives. Never reinvent UI components from scratch.

---

## 1. Zero Reinvention of Primitives
- **Check Before Writing**: Always inspect `@/components/ui/` (or the project's established design system) before creating any interactive or structural element.
- **Mandatory Primitive Reuse**:
  - Buttons, Dialogs/Modals, Sheets, Dropdowns, Selects, Tabs, Tooltips, Tables, Popovers, and Inputs **must** use the official design system components.
  - If a primitive is missing from the project, install it via the official CLI (e.g., `npx shadcn@latest add dialog select`) instead of crafting a custom `<div>` with `useState` and raw CSS.
- **Accessible by Default**: Never write raw custom overlays or dropdowns that lack keyboard navigation (Esc, Arrow keys), focus trapping, and ARIA attributes already provided by Radix / shadcn/ui.

---

## 2. Semantic Theme Tokens Over Hardcoded Colors
- **Use CSS Variables / Theme Tokens**:
  - Backgrounds: `bg-background`, `bg-card`, `bg-muted`, `bg-popover`.
  - Text: `text-foreground`, `text-muted-foreground`, `text-primary`.
  - Borders & Rings: `border-border`, `focus:ring-ring`, `ring-offset-background`.
  - Destructive/Accent: `text-destructive`, `bg-destructive/10`, `bg-accent`.
- **Banned**: Hardcoded arbitrary colors (e.g., `text-[#1e293b]`, `bg-blue-600`, `border-gray-300`) unless explicitly demanded by an institutional brand guideline.

---

## 3. Composition Over Monoliths
- **Compound Components**: Build features by composing modular subcomponents (e.g., `<Dialog>` wrapping `<DialogTrigger>`, `<DialogContent>`, `<DialogHeader>`, and `<DialogFooter>`).
- **Separation of Concerns**:
  - Keep business logic, validation, and data fetching in hooks, domain modules, or server actions.
  - Keep UI components purely declarative, ergonomic, and responsive (testing properly at 1366x768, 1280x720, and mobile viewports).
