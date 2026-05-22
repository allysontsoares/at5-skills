---
name: at5-toasts
description: Toast and notification system for AT5 projects using Base UI primitives. Documents the native Base UI Toast Manager (toastManager, anchoredToastManager), the custom Toasts and AnchoredToasts viewport and positioner structures, custom animations, and application bootstrap wrapping in main.tsx. Always trigger on keywords like toast, notification, toastManager, anchoredToastManager, toastProvider, AnchoredToastProvider, Base UI toast, or toast.tsx.
---

# at5-toasts — Notification and Toast Management with Base UI

This autonomous skill documents the **Notification and Toast Management** architecture in the **AT5** ecosystem, integrated with the **Base UI** headless engine (`@base-ui/react/toast`). It teaches the complete production architecture, the composition of providers in `main.tsx`, and how to use the imperative managers (`toastManager`, `anchoredToastManager`) with support for sync alerts, async promises, and contextual anchored positioning.

> [!IMPORTANT]
> **No Sonner or Custom Stores**: Under no circumstances should custom state stores (`toast-store.ts` / Pub-Sub) or external libraries like `sonner` be introduced. The system relies 100% on the native `@base-ui/react/toast` managers and components.

---

## 🛠️ 1. AT5 Notification Philosophy

The AT5 toast system separates concerns into a headless state engine, a visual markup structure, and an imperative dispatch API.

1. **State Management**: Governed natively by `@base-ui/react/toast`'s `Toast.createToastManager()`. This handles queues, limits, swipe-to-dismiss, and timer schedules.
2. **Visual Markups**: Configured in a single file `packages/ui/src/components/toast.tsx` separating standard floating toasts (`Toasts`) from element-anchored tooltips/cards (`AnchoredToasts`).
3. **No JSX in Logic**: Developers dispatch notifications imperatively via `toastManager` or `anchoredToastManager` without wrapping page triggers in local JSX providers.
4. **Coexistence of Contexts**: The application mounts both providers in a nested structure at the root level, offering global floating feedback and context-anchored elements at the same time.

---

## 📂 2. Reference & Codebase Implementation

The absolute source of truth for the Toast component implementation resides in the `references/` folder. AIs and developers must reference this code instead of implementing components ad-hoc:

* **Toast Component Reference**: [toast_implementation.md](file:///home/allysonsoares/at5-skills/at5-toasts/references/toast_implementation.md)
* **Codebase File Location**: `/packages/ui/src/components/toast.tsx`

---

## 🏗️ 3. Initialization & Bootstrap Context

To provide both floating system alerts and contextual anchored tooltips concurrently, providers must be nested together at the root entrypoint of the application.

In the real entrypoint `apps/web/src/main.tsx`, the bootstrap structure is as follows:

```tsx
import { AnchoredToastProvider, ToastProvider } from '@manager/ui';

ReactDOM.createRoot(document.getElementById('root')!).render(
  <React.StrictMode>
    <ToastProvider position="bottom-center">
      <AnchoredToastProvider>
        {/* React Router & Application Content */}
        <App />
      </AnchoredToastProvider>
    </ToastProvider>
  </React.StrictMode>
);
```

### Supported Positions (`ToastProvider`):
* `top-left` | `top-center` | `top-right`
* `bottom-left` | `bottom-center` | `bottom-right` (Default)

---

## 📝 4. Imperative API & Usage Examples

### 4.1 Basic Floating Toasts (`toastManager`)
Import `toastManager` from `@manager/ui` (or path alias `@/components/ui/toast`) and trigger:

```tsx
import { toastManager } from '@manager/ui';

// Simple Alert
toastManager.add({
  title: 'Operação concluída',
});

// Rich Alert
toastManager.add({
  title: 'Laboratório salvo',
  description: 'Os dados foram atualizados com sucesso.',
  type: 'success', // 'success' | 'error' | 'warning' | 'info' | 'loading'
});
```

### 4.2 Async Promise-Based Alerts (Loaders)
Highly recommended for form submissions or API mutations. The toast transitions states automatically from loading to success/error based on the promise resolution:

```tsx
import { toastManager } from '@manager/ui';

const handleSubmit = async (e: React.FormEvent) => {
  e.preventDefault();
  
  const promise = saveMutation.mutateAsync(formData);

  toastManager.promise(promise, {
    loading: { 
      title: 'Salvando...',
      description: 'Aguarde enquanto os dados são enviados.' 
    },
    success: () => ({ 
      title: 'Salvo com sucesso!',
      description: 'Registro criado com sucesso.'
    }),
    error: (error) => ({
      title: 'Erro ao salvar',
      description: error instanceof Error ? error.message : 'Erro inesperado.'
    }),
  });

  try {
    await promise;
    onSuccess();
  } catch {
    // Silent catch (toast already handles the error display)
  }
};
```

### 4.3 Deduplicated Toasts (Upsert)
To avoid screen clutter (e.g. during background polling or status syncing), pass a stable string `id`. This updates the existing toast and restarts its auto-dismiss timer instead of spawning a new one:

```tsx
// Spawns loading status
toastManager.add({
  id: 'sync-status',
  title: 'Sincronizando dados...',
  type: 'loading',
});

// Later, updates the same toast in place
toastManager.add({
  id: 'sync-status',
  title: 'Sincronização concluída!',
  type: 'success',
});
```

### 4.4 Element-Anchored Toasts (`anchoredToastManager`)
Ideal for contextual notifications like copying text to clipboards or quick inline validations. Instead of absolute CSS Anchor Positioning (which lacks broad browser support), this uses the standard **Base UI `Toast.Positioner`** under the hood:

```tsx
import { useRef } from 'react';
import { anchoredToastManager } from '@manager/ui';

export function CopyButton() {
  const buttonRef = useRef<HTMLButtonElement>(null);

  const handleCopy = () => {
    anchoredToastManager.add({
      title: 'Copiado!',
      positionerProps: {
        anchor: buttonRef.current,
        sideOffset: 6,
      },
    });
  };

  return <button ref={buttonRef} onClick={handleCopy}>Copiar Link</button>;
}
```

#### Compact Tooltip Style
To render a compact, border-only small notification without a description (perfect for copy alerts):

```tsx
anchoredToastManager.add({
  title: 'Copiado!',
  positionerProps: {
    anchor: buttonRef.current,
    sideOffset: 6,
  },
  data: {
    tooltipStyle: true, // Triggers compact tooltip styling in toast.tsx
  },
});
```

---

## 🎨 5. Animations, Stacking & Transitions

The AT5 toast system utilizes a state-of-the-art visual stacking and motion model styled with **Tailwind CSS v4.0** tokens:

1. **Stacking Index (`--toast-index`)**: Toasts stack vertically. Active toasts are layered behind the frontmost toast using `z-[calc(9999-var(--toast-index))]`.
2. **Dynamic Height & Transitions**: Utilizes smooth transition transforms `[transition:transform_.5s_cubic-bezier(.22,1,.36,1),opacity_.5s,height_.15s]`.
3. **Update Replay Classes**: In case an active toast gets updated (e.g. `loading` -> `success`), the component recalculates `updateKey` and triggers active vibration animations (`animate-toast-error-even`, `animate-toast-success-odd`, etc.) to grab user attention.
4. **Direction-Aware Swipe**: Leverages Base UI's swipe gestures, applying custom sliding direction variables (`data-[swipe-direction]`) so swipe-to-dismiss moves the card elegantly off-screen in the correct direction.

---

## ⚙️ 6. Validation & Audit Checklist

When working with or reviewing the Toast system, perform the following validation checks:

* [ ] **No Duplicated Providers**: Verify that `ToastProvider` and `AnchoredToastProvider` are loaded **only once** at the root layout/main entrypoint.
* [ ] **Correct Manager Reference**: Never use standard `toastManager` to display anchored toasts. Standard uses screen portaling; `anchoredToastManager` must be used for anchoring to specific elements.
* [ ] **Aria Descriptors**: Verify `Toast.Title` and `Toast.Description` are enclosed inside `Toast.Content` inside the `toast.tsx` component to keep the screen reader mapping intact.
* [ ] **Focus Management**: Standard toasts must have `autoFocus={false}` (configured in the provider or hook) to prevent breaking keyboard navigation flows.
* [ ] **Positioner Refs**: Ensure `anchoredToastManager` calls provide a valid `positionerProps.anchor` pointing to a mounted DOM ref or element, otherwise the toast will not be rendered.
