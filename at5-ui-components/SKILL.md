---
name: at5-ui-components
description: Complete AT5 Design System component library built on Base UI + Tailwind CSS v4. Documents composition patterns, color scales, npm dependencies, and houses a comprehensive catalog of 59 production-ready React components, hooks, and utilities. Always trigger on keywords like component, button, modal, form, input, calendar, card, kanban, filters, table, chart, design system, Base UI, render prop, or tailwind-variants.
---

# at5-ui-components — Complete Design System Component Catalog & Patterns

This autonomous skill documents the complete **AT5 Design System** component library. It provides key implementation patterns, list of dependencies, and a comprehensive, categorized catalog containing the **complete real production code** for all 59 React components, hooks, and utilities.

Instead of referencing external repositories or letting AIs implement components ad-hoc, this skill contains the absolute real implementations to guarantee perfect UX consistency, accessibility, and high performance.

---

## 🔑 1. Composition Pattern: Slot Pattern (`render` prop vs `asChild`)

AT5 adopts **Base UI (`@base-ui/react`)** as the standard headless library. Unlike other libraries (e.g. Radix UI Primitives) which use the `asChild` prop to delegate rendering, **Base UI uses the Slot Pattern based on the `render` prop**.

> [!IMPORTANT]
> **GOLDEN RULE**: **Never** use the `asChild` property on triggers, close triggers, or selectors when building or consuming Base UI components. Always use the `render` prop passing the inner component call or a render function.
>
> The design system's `Button` component accepts `asChild` internally only as a compatibility helper for legacy code, but **should not be used for new development**. Always prefer `render={<button />}` or `render={<a />}`.

### ❌ Incorrect Example (Radix Style):
```tsx
<DialogTrigger asChild>
  <Button variant="solid">Open Dialog</Button>
</DialogTrigger>
```

### ✅ Correct Example (Base UI Style):
```tsx
<DialogTrigger render={<Button variant="solid" />}>
  Open Dialog
</DialogTrigger>
```

---

## 🎨 2. Neutral Color Scales (`mauve` vs `slate`)

To create high-premium depth and warmth, components in this design system use two distinct neutral scales:
- **`mauve` (slightly purple-tinted)**: Exclusively for **form elements** (inputs, textareas, selects, checkbox inputs, badges) to provide a temperature-neutral, warm interactive feel.
- **`slate` (slightly blue-tinted)**: Exclusively for **structure and containers** (cards, dialogs, sidebars, page frames, backgrounds) to establish clear hierarchy and structural depth.

Both scales are mapped in Tailwind CSS and support automatic dark mode using the `.dark` class selector. **Never replace `mauve` with `slate` or vice versa** — each has its semantic role.

---

## 📦 3. Core Workspace Dependencies

When deploying these components into new workspaces or projects, make sure the following dependencies are installed to ensure smooth compilation:

```json
{
  "dependencies": {
    "@base-ui/react": "^1.4.1",
    "@dnd-kit/core": "^6.3.1",
    "@dnd-kit/modifiers": "^9.0.0",
    "@dnd-kit/sortable": "^10.0.0",
    "@dnd-kit/utilities": "^3.2.2",
    "@radix-ui/react-slot": "^1.2.4",
    "@untitled-ui/icons-react": "^0.1.4",
    "class-variance-authority": "^0.7.0",
    "clsx": "^2.1.0",
    "cmdk": "^1.1.1",
    "input-otp": "^1.1.0",
    "lucide-react": "^0.363.0",
    "maplibre-gl": "^5.15.0",
    "next-themes": "^0.4.6",
    "react-day-picker": "^9.12.0",
    "react-hook-form": "^7.51.0",
    "react-resizable-panels": "^3.0.6",
    "recharts": "^2.12.3",
    "tailwind-merge": "^2.2.1",
    "tailwind-variants": "^3.2.2"
  }
}
```

---

## 📂 4. Categorized Component Catalog

All 59 React components, hooks, and utilities are compiled in full inside the specialized markdown reference files under the `references/` directory.

### 4.1 Buttons & Actions
*Botões, grupos de botões, seletores de alternância e elementos interativos de ação.*
* **Reference File**: [buttons_and_actions.md](file:///home/allysonsoares/at5-skills/at5-ui-components/references/buttons_and_actions.md)
* **Included Components**:
  * [button.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/buttons_and_actions.md#📂-buttontsx) — Core button built with Base UI `useRender` and `tailwind-variants`
  * [button-group.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/buttons_and_actions.md#📂-button-grouptsx) — Component to group button components together
  * [toggle.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/buttons_and_actions.md#📂-toggletsx) — Single toggle button
  * [toggle-group.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/buttons_and_actions.md#📂-toggle-grouptsx) — Group of toggle buttons
  * [kbd.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/buttons_and_actions.md#📂-kbdtsx) — Keyboard shortcut badge

### 4.2 Inputs & Form Elements
*Campos de entrada de texto, seleção, caixas de seleção, botões de rádio e suporte a formulários.*
* **Reference File**: [inputs_and_form_elements.md](file:///home/allysonsoares/at5-skills/at5-ui-components/references/inputs_and_form_elements.md)
* **Included Components**:
  * [input.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/inputs_and_form_elements.md#📂-inputtsx) — Classic single-line input field
  * [textarea.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/inputs_and_form_elements.md#📂-textareatsx) — Multi-line text field with field-sizing auto-grow
  * [checkbox.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/inputs_and_form_elements.md#📂-checkboxtsx) — Accessible checkbox component
  * [switch.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/inputs_and_form_elements.md#📂-switchtsx) — Toggle switch
  * [radio-group.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/inputs_and_form_elements.md#📂-radio-grouptsx) — Group of radio inputs
  * [input-group.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/inputs_and_form_elements.md#📂-input-grouptsx) — Inputs decorated with addons, buttons, or text
  * [input-otp.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/inputs_and_form_elements.md#📂-input-otptsx) — One-Time Password input fields
  * [autocomplete.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/inputs_and_form_elements.md#📂-autocompletetsx) — Searchable select dropdown combobox
  * [select.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/inputs_and_form_elements.md#📂-selecttsx) — Headless select trigger and popup
  * [form.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/inputs_and_form_elements.md#📂-formtsx) — Form wrappers and context providers
  * [form-field.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/inputs_and_form_elements.md#📂-form-fieldtsx) — Form field wrappers for react-hook-form integration
  * [field.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/inputs_and_form_elements.md#📂-fieldtsx) — Granular field structure blocks
  * [label.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/inputs_and_form_elements.md#📂-labeltsx) — Standalone accessible label component
  * [calendar.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/inputs_and_form_elements.md#📂-calendartsx) — Datepicker and month picker built with react-day-picker

### 4.3 Overlays & Feedback
*Caixas de diálogo, modais flutuantes, popovers, alertas, menus de contexto, folhas deslizantes e toasts.*
* **Reference File**: [overlays_and_feedback.md](file:///home/allysonsoares/at5-skills/at5-ui-components/references/overlays_and_feedback.md)
* **Included Components**:
  * [dialog.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/overlays_and_feedback.md#📂-dialogtsx) — Base dialog structure
  * [alert-dialog.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/overlays_and_feedback.md#📂-alert-dialogtsx) — Focus-locked dialog for critical decisions
  * [modal.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/overlays_and_feedback.md#📂-modaltsx) — Drag-and-drop support container for floating dialogs
  * [popover.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/overlays_and_feedback.md#📂-popovertsx) — Anchor-positioned information card
  * [hover-card.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/overlays_and_feedback.md#📂-hover-cardtsx) — Card opened when hovering over an anchor
  * [sheet.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/overlays_and_feedback.md#📂-sheettsx) — Side panel drawer (sheets)
  * [tooltip.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/overlays_and_feedback.md#📂-tooltiptsx) — Tiny float labels shown on hover
  * [toast.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/overlays_and_feedback.md#📂-toasttsx) — Custom toast notification system
  * [context-menu.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/overlays_and_feedback.md#📂-context-menutsx) — Triggered context dropdown options
  * [dropdown-menu.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/overlays_and_feedback.md#📂-dropdown-menutsx) — Dropdown menu list
  * [menubar.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/overlays_and_feedback.md#📂-menubartsx) — Multi-list horizontal menubar panel
  * [alert.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/overlays_and_feedback.md#📂-alerttsx) — Informative block notifications

### 4.4 Data Display & Navigation
*Exibição de dados tabulares, avatares, breadcrumbs, barras de progresso, acordions e linhas do tempo.*
* **Reference File**: [data_display_and_navigation.md](file:///home/allysonsoares/at5-skills/at5-ui-components/references/data_display_and_navigation.md)
* **Included Components**:
  * [accordion.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/data_display_and_navigation.md#📂-accordiontsx) — Foldable content slots
  * [avatar.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/data_display_and_navigation.md#📂-avatartsx) — Rounded user avatar and fallback labels
  * [badge.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/data_display_and_navigation.md#📂-badgetsx) — Clean status pill badge
  * [breadcrumb.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/data_display_and_navigation.md#📂-breadcrumbtsx) — Page hierarchy breadcrumb navigation paths
  * [card.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/data_display_and_navigation.md#📂-cardtsx) — Structural card panel layout
  * [chart.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/data_display_and_navigation.md#📂-charttsx) — Custom graphing utilities using Recharts
  * [empty.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/data_display_and_navigation.md#📂-emptytsx) — Empty state placeholder component
  * [navigation-menu.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/data_display_and_navigation.md#📂-navigation-menutsx) — Navigation links with dropdown sub-panels
  * [pagination.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/data_display_and_navigation.md#📂-paginationtsx) — Pagination list handlers
  * [progress.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/data_display_and_navigation.md#📂-progresstsx) — Visual loading/progress bar
  * [table.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/data_display_and_navigation.md#📂-tabletsx) — Styled grid data tables
  * [tabs.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/data_display_and_navigation.md#📂-tabstsx) — Tab switching containers
  * [timeline.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/data_display_and_navigation.md#📂-timelinetsx) — Chronological history timelines

### 4.5 Layouts & Containers
*Contêineres estruturais, áreas roláveis, painéis redimensionáveis e sidebars.*
* **Reference File**: [layouts_and_containers.md](file:///home/allysonsoares/at5-skills/at5-ui-components/references/layouts_and_containers.md)
* **Included Components**:
  * [aspect-ratio.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/layouts_and_containers.md#📂-aspect-ratiotsx) — Keeps child visual ratio bounds intact
  * [collapsible.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/layouts_and_containers.md#📂-collapsibletsx) — Collapsing block slots
  * [frame.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/layouts_and_containers.md#📂-frametsx) — Clean UI frame panel container
  * [resizable.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/layouts_and_containers.md#📂-resizabletsx) — Custom split layouts with resizable handlers
  * [scroll-area.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/layouts_and_containers.md#📂-scroll-areatsx) — Custom scrollbars container
  * [separator.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/layouts_and_containers.md#📂-separatortsx) — Semantic UI divider rule
  * [sidebar.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/layouts_and_containers.md#📂-sidebartsx) — Full collapsible multi-tier sidebar shell
  * [skeleton.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/layouts_and_containers.md#📂-skeletontsx) — Shimmer loading shapes
  * [spinner.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/layouts_and_containers.md#📂-spinnertsx) — Standard spinner wheel
  * [loader.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/layouts_and_containers.md#📂-loadertsx) — Loading overlay and indicator states

### 4.6 Advanced & Complex Components
*Componentes complexos de alta densidade como Kanban boards, filtros e mapas.*
* **Reference File**: [advanced_and_complex.md](file:///home/allysonsoares/at5-skills/at5-ui-components/references/advanced_and_complex.md)
* **Included Components**:
  * [filters.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/advanced_and_complex.md#📂-filterstsx) — Mega component for advanced filters, queries, and operators
  * [kanban.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/advanced_and_complex.md#📂-kanbantsx) — Interactive drag-and-drop board built on `@dnd-kit`
  * [map.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/advanced_and_complex.md#📂-maptsx) — Complex interactive maps rendering using Maplibre GL
  * [command.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/advanced_and_complex.md#📂-commandtsx) — Command menu (CMDK) dialog interface
  * [item.tsx](file:///home/allysonsoares/at5-skills/at5-ui-components/references/advanced_and_complex.md#📂-itemtsx) — Individual interactive grid item blocks

### 4.7 Utilities & Hooks
*Utilitários e hooks internos que auxiliam os componentes do design system.*
* **Reference File**: [utils_and_hooks.md](file:///home/allysonsoares/at5-skills/at5-ui-components/references/utils_and_hooks.md)
* **Included Utilities**:
  * [truncate.ts](file:///home/allysonsoares/at5-skills/at5-ui-components/references/utils_and_hooks.md#📂-truncatets) — Text truncation utility
  * [use-mobile.ts](file:///home/allysonsoares/at5-skills/at5-ui-components/references/utils_and_hooks.md#📂-use-mobilets) — Breakpoint hook detecting mobile layout status
