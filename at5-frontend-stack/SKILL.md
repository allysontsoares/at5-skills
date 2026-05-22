---
name: at5-frontend-stack
description: "Master guide and orchestrator for the AT5 frontend stack. Use this skill when setting up or working on any frontend project with Vite 8+, React 19, React Compiler, Tailwind CSS v4, Base UI, ofetch, NiceModal, Zod, or React Hook Form. Always trigger when the user asks about frontend architecture, project structure, responsive layout, accessibility, data fetching patterns, monorepo setup, or iOS/Safari compatibility. Also activates the sub-skills: at5-ui-components, at5-nicemodal, at5-radix-colors-tailwind, and at5-toasts."
---

# at5-frontend-stack — Master Guide and Frontend Orchestrator

This is the definitive architecture, patterns, and software engineering guide for frontend development in the **AT5** ecosystem. It acts as the primary orchestrator and single source of truth to guide AI agents and developers in creating consistent, modern, accessible, and highly optimized code.

---

## 🚀 1. Core Technology Stack

Any new or existing project following the AT5 architecture must strictly adopt the following cutting-edge technologies and configurations:

1. **Vite 8+**: Ultra-fast build engine for local development and production bundling.
2. **React 19**: Stable React version introducing native support for Actions, the `use` API, and significant performance improvements.
3. **React Compiler**: The automatic React compiler. **Requires `@vitejs/plugin-react` (Babel) — incompatible with the SWC variant**. Configure in `vite.config.ts` via `babel-plugin-react-compiler`:
   ```ts
   import react from '@vitejs/plugin-react';
   // vite.config.ts
   plugins: [react({ babel: { plugins: [['babel-plugin-react-compiler']] } })]
   ```
   Eliminates the need for excessive manual memoization with `useMemo` and `useCallback` in most cases.
4. **Tailwind CSS v4.0**: The new era of Tailwind powered by a native Rust engine, with no redundant `tailwind.config.js` required — all setup is done directly in the CSS entry file (`index.css`) via native CSS variables.
5. **Base UI (`@base-ui/react`)**: Unstyled (headless) component library with high accessibility focus, optimized for performance and W3C standards compliance.
6. **Zod**: TypeScript-first schema validation library, used as the single source of truth for API contracts and forms.
7. **React Hook Form**: Lightweight, ref-based form orchestrator, integrated with Zod validation.
8. **ofetch**: Ultra-lightweight, resilient HTTP client compatible with Node/Browser, replacing Axios with better support for timeouts, retry, and native token handling.
9. **NiceModal**: Declarative React state-based modal/dialog manager that allows creating and opening dialogs without polluting the local JSX tree. **Requires** wrapping `<App>` with `<NiceModal.Provider>`:
   ```tsx
   import NiceModal from '@ebay/nice-modal-react';
   // main.tsx or root App.tsx
   <NiceModal.Provider>
     <App />
   </NiceModal.Provider>
   ```

---

## 📦 2. Monorepo Structure and Contracts

AT5 operates under a monorepo organized with `pnpm` workspaces. Maintaining Separation of Concerns and the DRY (Don't Repeat Yourself) philosophy is mandatory:

```text
/
├── apps/
│   ├── api/            # Express-based backend API
│   └── web/            # Main web application (Vite + React)
├── packages/
│   ├── types/          # Source of truth for shared types and Zod schemas
│   ├── ui/             # Design System (Base UI + Tailwind)
│   ├── hooks/          # Custom hooks and state/media-query utilities
│   └── config/         # Global configurations (Biome, tsconfig, etc.)
```

### DRY Contracts with `/packages/types`
Never duplicate contracts or interfaces.
- **Zod Schemas**: All API request/response contracts must be declared as Zod schemas in `/packages/types`.
- **Type Inference**: The frontend must infer TypeScript types directly from these schemas using `z.infer<typeof Schema>` to ensure end-to-end type safety and zero type divergence.

---

## 🌐 3. Resilient Data Fetching (`apiClient`)

We replaced Axios with **`ofetch`** for being more modern, lightweight, and offering native async interceptors. The `apiClient` is configured to handle timeouts, dynamic JWT injection (from Zustand store), and automatic logout on authorization errors (401).

### Real `apiClient.ts` Code
Implement or maintain the HTTP client structured as follows in the frontend app (`apps/web/src/services/apiClient.ts`):

```typescript
import { ofetch } from 'ofetch';
import { useAuthStore } from '@/store/auth-store'; // Zustand auth store

const API_BASE_URL = import.meta.env.VITE_API_BASE_URL || 'http://localhost:4100';

export const apiClient = ofetch.create({
  baseURL: API_BASE_URL,
  timeout: 15000, // 15 second default timeout
  headers: {
    'Content-Type': 'application/json',
    Accept: 'application/json',
  },
  async onRequest({ options }) {
    // Dynamically fetch current JWT from Zustand store
    const token = useAuthStore.getState().token;
    if (token) {
      options.headers = {
        ...options.headers,
        Authorization: `Bearer ${token}`,
      };
    }
  },
  async onResponseError({ response }) {
    // Unified authentication error handling (expired or invalid JWT)
    if (response.status === 401) {
      console.warn('Session expired (401). Logging out automatically...');
      useAuthStore.getState().logout();
      window.location.href = '/login';
    }
  },
});
```

---

## 📱 4. Advanced Responsiveness and Web Standards

### 🍎 Required Meta Viewport (iOS / Safari / macOS)

Every AT5 project **must** include the following meta tag in `index.html`. Without it, Safari on iOS applies automatic zoom on inputs and the layout breaks on small screens:

```html
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1" />
```

> [!TIP]
> `maximum-scale=1` prevents automatic input zoom on iOS Safari (default behavior when input `font-size` is smaller than 16px). If your app needs accessible zoom, use `minimum-scale=1` without `maximum-scale`.

---

AT5 strictly prohibits the use of classic HTML tables (`<table>`, `<tr>`, `<td>`) for complex layouts or listings on mobile devices, as they break visualization and prevent intuitive navigation.

### 📐 Responsive Grid vs Tables
Always prefer structures based on **CSS Grid** and **Flexbox** with Tailwind v4 utility classes to adapt dense lists and data:

```tsx
// ❌ DON'T: Rigid tables that break on mobile
// ✅ DO: Adaptive Mobile-First Grid
export function ResponsiveList({ items }) {
  return (
    <div className="grid grid-cols-1 gap-4 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4">
      {items.map((item) => (
        <div key={item.id} className="rounded-xl border border-slate-6 bg-slate-3 p-4">
          <div className="flex items-center justify-between">
            <span className="font-semibold text-slate-12">{item.name}</span>
            <span className="text-xs text-slate-11">{item.code}</span>
          </div>
          <p className="mt-2 text-sm text-slate-11">{item.description}</p>
        </div>
      ))}
    </div>
  );
}
```

### ♿ Accessibility (a11y) as an Engineering Requirement
All components and screens must be fully accessible by default:
- **ARIA**: Use appropriate `aria-*` attributes to describe interactive states (e.g., `aria-expanded`, `aria-invalid`, `aria-describedby`).
- **Keyboard Support**: Ensure all interactive elements are focusable (`tabIndex={0}`) and can be triggered via `Enter` or `Space`.
- **Visible Focus**: Never remove focus styles without providing a clear alternative. Use `focus-visible:ring-2 focus-visible:ring-blue-7`.
- **Color Contrast**: Follow Radix Colors guidelines to ensure adequate APCA contrast for reading in any theme (Light/Dark).

---

## 🎛️ 5. Invoking the Autonomous Sub-Skills

To implement any functionality in AT5 with precision, the agent or developer **MUST activate and use** the following autonomous and independent skills located in **`~/.agents/skills/`** based on the scope of work:

1. 📂 **`at5-ui-components`**: Contains the complete catalog of pre-styled React components and the real `/packages/ui` code for rapid creation of accessible interfaces using the Base UI **Slot Pattern** (`render` prop instead of `asChild`).
2. 📂 **`at5-nicemodal`**: Teaches how to orchestrate complex forms and dialogs using the robust `createFormDialog` helper (with sidebar and mobile tab support) and `createAlertDialog` without manual wrappers.
3. 📂 **`at5-radix-colors-tailwind`**: Guide for strict manipulation of color tokens based on Radix Colors, dark mode via `.dark` class, and exact step mapping from 1 to 12 in `index.css`.
4. 📂 **`at5-toasts`**: Configuration and dispatch of async/sync notifications and native Promise handling with `toastManager` and `anchoredToastManager`.

> [!TIP]
> If you need to build UI components or manage dialogs right now, immediately call `~/.agents/skills/at5-ui-components/SKILL.md` or `~/.agents/skills/at5-nicemodal/SKILL.md`.
