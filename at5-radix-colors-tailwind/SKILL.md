---
name: at5-radix-colors-tailwind
description: Design token architecture integrating Radix Colors with Tailwind CSS v4 for AT5 projects. Use when setting up color tokens, dark mode toggle, brand color scale, semantic color palettes (error, success, warning), or OKLCH color values. Always trigger on keywords like Radix Colors, @radix-ui/colors, @theme, dark mode, color scale, mauve, slate, brand color, oklch, tailwind colors, or prefers-color-scheme.
---

# at5-radix-colors-tailwind — Color Design Tokens with Radix Colors and Tailwind v4.0

This autonomous skill documents the **Color Design Token** architecture in the **AT5** ecosystem, integrating the **Radix Colors** scale natively with **Tailwind CSS v4.0**. It defines the 12-step scale, dark mode via `.dark` class, and explicit prohibition of classic Tailwind arbitrary color classes (e.g., `bg-blue-600`).

---

## 🎨 1. AT5 Color Philosophy

The **AT5** design system adopts a strict approach based on professional Radix Colors scales. This ensures accessible contrast (WCAG AA/AAA compatible), visual smoothness, chromatic harmony, and perfect dark theme support without duplicating utility classes in HTML (like `dark:bg-slate-900`).

### ⛔ Strict Implementation Rules (Prohibitions)
1. **FORBIDDEN** use of classic Tailwind colors like `bg-blue-500`, `text-slate-800`, `border-gray-200`, `hover:bg-red-600`.
2. **FORBIDDEN** use of raw hex or RGB colors (`#ffffff`, `rgb(0,0,0)`) directly in utility classes or ad-hoc inline styles.
3. **MANDATORY** exclusive use of semantic tokens mapped to Radix scales 1 through 12 (e.g., `bg-slate-1`, `text-slate-12`, `border-slate-6`).
4. **MANDATORY** use of native CSS variables in the Tailwind v4.0 `@theme` block.

---

## 📐 2. The 12-Step Color Scale Semantics

Each Radix color scale has exactly 12 tones (steps) with rigorous design purposes. Understanding and respecting these steps is vital for aesthetic consistency and accessibility.

| Step | Primary Use | Description and Behavior | Example Classes |
| :--- | :--- | :--- | :--- |
| **1** | **App Background** | Main application background. Strictly matte and flat. | `bg-slate-1`, `bg-brand-1` |
| **2** | **Subtle Background** | Subtle backgrounds. Used in sidebars, highlighted cards, or striped tables. | `bg-slate-2`, `bg-brand-2` |
| **3** | **UI Background Normal** | Normal interactive component backgrounds (inputs, select triggers, secondary buttons). | `bg-slate-3`, `bg-brand-3` |
| **4** | **UI Background Hover** | Interactive component backgrounds in hover state. | `hover:bg-slate-4` |
| **5** | **UI Background Active** | Interactive component backgrounds in pressed or selected state. | `active:bg-slate-5`, `data-[state=active]:bg-slate-5` |
| **6** | **Subtle Border** | Subtle borders. Dividers, card borders, or subtle separators. | `border-slate-6`, `border-brand-6` |
| **7** | **Strong Border** | Borders for interactive components at rest or subtle focus ring. | `border-slate-7`, `border-brand-7` |
| **8** | **Hover Border** | Borders for focused or hovered interactive components. | `hover:border-slate-8`, `focus:border-brand-8` |
| **9** | **Solid Background** | Strong solid background. Primary buttons, solid badges, important icons, active status states. | `bg-slate-9`, `bg-brand-9` |
| **10** | **Hover Solid Background** | Strong solid background in hover state. | `hover:bg-slate-10`, `hover:bg-brand-10` |
| **11** | **Low-Contrast Text** | Secondary text, secondary descriptions, or lower-priority labels. | `text-slate-11`, `text-brand-11` |
| **12** | **High-Contrast Text** | Primary text, main titles, and high-emphasis content. | `text-slate-12`, `text-brand-12` |

---

## 🛠️ 3. Configuring `index.css` in Tailwind v4.0

> [!IMPORTANT]
> **Source of truth**: The project's `apps/web/src/index.css` already contains the real, complete configuration. This section documents the **architectural pattern** for new projects. Never recreate color values manually — always use the `@radix-ui/colors` package.

### 3.1 Correct Approach: Import from `@radix-ui/colors` Package

Never define color values manually with approximate OKLCH. Use the official CSS files from the package, which contain exact values and already include automatic light/dark theme support:

```css
@import 'tailwindcss';

/* Neutral scales */
@import '@radix-ui/colors/mauve.css';
@import '@radix-ui/colors/mauve-dark.css';
@import '@radix-ui/colors/mauve-alpha.css';
@import '@radix-ui/colors/mauve-dark-alpha.css';

@import '@radix-ui/colors/slate.css';
@import '@radix-ui/colors/slate-dark.css';

/* Feedback scales */
@import '@radix-ui/colors/red.css';
@import '@radix-ui/colors/red-dark.css';

@import '@radix-ui/colors/green.css';
@import '@radix-ui/colors/green-dark.css';

@import '@radix-ui/colors/amber.css';
@import '@radix-ui/colors/amber-dark.css';

/* Enable dark variant via .dark class (ThemeProvider) */
@custom-variant dark (&:is(.dark *));
```

### 3.2 Dark Mode: `.dark` Class (not `prefers-color-scheme`)

AT5 uses **manual class-based toggle** for dark mode, controlled by `ThemeProvider` + `useThemeStore`. Radix Colors dark is automatically applied by `@radix-ui/colors/*-dark.css` under the `.dark` class:

```css
/* ✅ CORRECT — class-based toggle (ThemeProvider) */
@custom-variant dark (&:is(.dark *));

/* ❌ DO NOT USE for AT5 — automatic by operating system */
/* @media (prefers-color-scheme: dark) { ... } */
```

### 3.3 Custom Color Mapping in `@theme`

Only colors **outside of standard Radix** (e.g., custom brand) need to be defined in `@theme`. Radix scales (mauve, slate, etc.) are mapped via `tailwind.config.js` pointing to the package's CSS variables (`--mauve-N`, `--slate-N`):

```css
@theme {
  /* Custom brand scale (e.g., project teal) */
  --color-brand-1:  var(--brand-1);
  --color-brand-2:  var(--brand-2);
  /* ... through --color-brand-12 */
}

/* Brand values in light theme */
:root, .light, .light-theme {
  --brand-9: oklch(66% 0.1217 221.7); /* primary brand color */
  /* ... other steps */
}

/* Brand values in dark theme */
.dark, .dark-theme {
  --brand-9: oklch(66% 0.1217 221.7); /* usually same as light */
  /* ... other steps */
}
```

### 3.4 Mapping in `tailwind.config.js`

For classes like `bg-mauve-6`, `text-slate-12`, `border-red-7` to work, map the Radix scales in the config:

```js
function getColorScale(name) {
  const scale = {};
  for (let i = 1; i <= 12; i++) {
    scale[i] = `var(--${name}-${i})`;
    scale[`a${i}`] = `var(--${name}-a${i})`;
  }
  return scale;
}

module.exports = {
  theme: {
    extend: {
      colors: {
        mauve: getColorScale('mauve'),
        slate: getColorScale('slate'),
        red:   getColorScale('red'),
        green: getColorScale('green'),
        amber: getColorScale('amber'),
        blue:  getColorScale('blue'),
        // brand is mapped via @theme in index.css
      },
    },
  },
};
```

---

## 🛠️ 4. Practical Color Usage Guide

Below are the semantic styling patterns to keep components consistent and elegant across any theme.

### 4.1 General Backgrounds and Cards
```tsx
// Full page with main app background
<div className="min-h-screen bg-slate-1 text-slate-12">

  {/* Highlighted card using secondary background and subtle borders */}
  <div className="bg-slate-2 border border-slate-6 rounded-lg p-6 shadow-sm">
    <h2 className="text-lg font-semibold text-slate-12">General Settings</h2>
    <p className="text-sm text-slate-11 mt-1">Manage global preferences.</p>
  </div>

</div>
```

### 4.2 Form Components (Inputs, Selects)
```tsx
// Inputs with elegant focus transitions and adaptive borders
<input
  className="w-full h-10 px-3 rounded-md bg-slate-3 border border-slate-7 text-slate-12 placeholder-slate-11 focus:outline-none focus:border-brand-8 focus:ring-1 focus:ring-brand-8 transition-colors"
  placeholder="Enter your name..."
/>
```

### 4.3 Action Buttons (Primary, Secondary, Destructive)
```tsx
// Primary Button (Strong)
<button className="h-10 px-4 rounded-md bg-brand-9 hover:bg-brand-10 text-white font-medium shadow-sm active:scale-[0.98] transition-all">
  Confirm Action
</button>

// Secondary Button (Subtle)
<button className="h-10 px-4 rounded-md bg-slate-3 hover:bg-slate-4 text-slate-12 border border-slate-6 active:scale-[0.98] transition-all">
  Cancel
</button>

// Destructive Alert Button (Danger)
<button className="h-10 px-4 rounded-md bg-red-9 hover:bg-red-10 text-white font-medium shadow-sm active:scale-[0.98] transition-all">
  Delete Record
</button>
```

---

## ⚙️ 5. How to Validate and Audit

Whenever the AI or developer makes changes to the color design, execute and audit the following:
1. **Contrast Check**: Ensure any text on background achieves at least `4.5:1` contrast ratio (scale 11 or 12 on backgrounds 1, 2, or 3).
2. **Variable Audit**: Ensure there are no CSS declarations in the project scope containing literal `#` or `rgb` for interface colors.
3. **Dark Theme Validation**: Disable system colors and test the smooth transition via the `.dark` class.
