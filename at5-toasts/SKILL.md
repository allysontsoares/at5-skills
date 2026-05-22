---
name: at5-toasts
description: Toast and notification system for AT5 projects using Base UI primitives. Use when implementing toasts, snackbars, success/error messages, loading indicators, async promise feedback, or contextually anchored alerts. Always trigger on keywords like toast, notification, toastManager, snackbar, loading state, success message, error message, promise feedback, AnchoredToastProvider, or Base UI toast.
---

# at5-toasts — Notification and Toast Management with Base UI

This autonomous skill documents the **Notification and Toast Management** architecture in the **AT5** ecosystem, integrated with the **Base UI** headless engine (`@base-ui/react`). It presents the complete real code for sync, async, and anchored toast providers (`ToastProvider`, `AnchoredToastProvider`), the imperative `toastManager` with Promise/loader support, and premium animations.

---

## 🛠️ 1. AT5 Notification Philosophy

AT5 adopts a clean, user-focused interface with fluid micro-animations. System toasts avoid opinionated heavy libraries, using the accessible headless primitives from **Base UI**, styled with **Radix Colors** tokens under **Tailwind v4.0**.

### Notification UX Principles:
1. **Non-intrusive**: Normal toasts appear in screen corners and disappear after `4000ms`.
2. **Clear Transient States**: Native support for `loading` status, smoothly transitioning to `success` or `error` via Promises.
3. **Contextual Anchoring**: The `anchoredToastManager` allows dispatching contextual alerts anchored to specific form elements or buttons using CSS Anchor Positioning.
4. **No JSX in Business Logic**: Developers dispatch notifications 100% imperatively from actions, queries, or services.

---

## 🏗️ 2. Imperative Infrastructure Code (`toast-store.ts`)

To allow imperative calls from any TypeScript file in the project (without needing local React Contexts), we create a subscription-based micro-store.

Create this file at `apps/web/src/components/ui/toast/toast-store.ts` (or equivalent):

```typescript
export type ToastType = 'success' | 'error' | 'warning' | 'info' | 'loading';

export interface ToastData {
  id: string;
  message: string;
  type: ToastType;
  duration?: number;
}

type ToastListener = (toasts: ToastData[]) => void;

class ToastStore {
  private toasts: ToastData[] = [];
  private listeners: Set<ToastListener> = new Set();

  subscribe(listener: ToastListener) {
    this.listeners.add(listener);
    return () => {
      this.listeners.delete(listener);
    };
  }

  private emit() {
    this.listeners.forEach((listener) => listener([...this.toasts]));
  }

  show(message: string, type: ToastType = 'info', duration = 4000): string {
    const id = Math.random().toString(36).substring(2, 9);
    this.toasts.push({ id, message, type, duration });
    this.emit();
    return id;
  }

  update(id: string, updates: Partial<Omit<ToastData, 'id'>>) {
    this.toasts = this.toasts.map((t) => (t.id === id ? { ...t, ...updates } : t));
    this.emit();
  }

  dismiss(id: string) {
    this.toasts = this.toasts.filter((t) => t.id !== id);
    this.emit();
  }

  clear() {
    this.toasts = [];
    this.emit();
  }
}

export const toastStore = new ToastStore();

/**
 * toastManager - Imperative API exposed to the entire application
 */
export const toastManager = {
  success: (msg: string, duration?: number) => toastStore.show(msg, 'success', duration),
  error: (msg: string, duration?: number) => toastStore.show(msg, 'error', duration),
  warning: (msg: string, duration?: number) => toastStore.show(msg, 'warning', duration),
  info: (msg: string, duration?: number) => toastStore.show(msg, 'info', duration),
  loading: (msg: string) => toastStore.show(msg, 'loading', 999999),
  dismiss: (id: string) => toastStore.dismiss(id),

  // Automatically resolves promises by transitioning the toast from Loading to Success or Error
  promise: <T>(
    promise: Promise<T>,
    messages: { loading: string; success: string; error: string },
    duration = 4000
  ): Promise<T> => {
    const id = toastStore.show(messages.loading, 'loading', 999999);
    return promise
      .then((res) => {
        toastStore.update(id, { message: messages.success, type: 'success', duration });
        setTimeout(() => toastStore.dismiss(id), duration);
        return res;
      })
      .catch((err) => {
        toastStore.update(id, { message: messages.error, type: 'error', duration });
        setTimeout(() => toastStore.dismiss(id), duration);
        throw err;
      });
  }
};
```

---

## 🏗️ 3. Real `ToastProvider` Component Code

This is the visual provider (`apps/web/src/components/ui/toast/toast-provider.tsx` or equivalent) that consumes the imperative store and renders toasts using the accessible **Base UI** primitives.

```tsx
import { useEffect, useState } from 'react';
import * as Toast from '@base-ui/react/toast';
import { toastStore, type ToastData } from './toast-store';
import {
  CheckCircle,
  XCircle,
  AlertTriangle,
  InfoCircle,
  Loading02,
  XClose,
} from '@untitled-ui/icons-react';

export function ToastProvider() {
  const [toasts, setToasts] = useState<ToastData[]>([]);

  useEffect(() => {
    return toastStore.subscribe((updatedToasts) => {
      setToasts(updatedToasts);
    });
  }, []);

  return (
    <Toast.Provider swipeDirection="right">
      {/* Toast display container (bottom-right corner) */}
      <div className="fixed bottom-4 right-4 z-50 flex flex-col gap-2 w-full max-w-sm pointer-events-none">
        {toasts.map((toast) => (
          <ToastRoot key={toast.id} toast={toast} />
        ))}
      </div>
    </Toast.Provider>
  );
}

interface ToastRootProps {
  toast: ToastData;
}

function ToastRoot({ toast }: ToastRootProps) {
  const { id, message, type, duration } = toast;

  const handleOpenChange = (open: boolean) => {
    if (!open) {
      toastStore.dismiss(id);
    }
  };

  const icons = {
    success: <CheckCircle className="size-5 text-green-9" />,
    error: <XCircle className="size-5 text-red-9" />,
    warning: <AlertTriangle className="size-5 text-amber-9" />,
    info: <InfoCircle className="size-5 text-brand-9" />,
    loading: <Loading02 className="size-5 text-slate-11 animate-spin" />,
  };

  const bgClasses = {
    success: 'bg-slate-2 border-green-6 text-slate-12',
    error: 'bg-slate-2 border-red-6 text-slate-12',
    warning: 'bg-slate-2 border-amber-6 text-slate-12',
    info: 'bg-slate-2 border-brand-6 text-slate-12',
    loading: 'bg-slate-2 border-slate-6 text-slate-12',
  };

  return (
    <Toast.Root
      open={true}
      onOpenChange={handleOpenChange}
      autoFocus={false}
      className={`pointer-events-auto flex items-start gap-3 w-full p-4 rounded-lg border shadow-lg transition-all duration-300 transform animate-in slide-in-from-right-10 fade-in-20 ${bgClasses[type]}`}
    >
      <div className="shrink-0 mt-0.5">{icons[type]}</div>

      <div className="flex-1 text-sm font-medium leading-tight">
        <Toast.Description>{message}</Toast.Description>
      </div>

      {type !== 'loading' && (
        <Toast.Close className="shrink-0 text-slate-11 hover:text-slate-12 transition-colors p-0.5 rounded hover:bg-slate-4">
          <XClose className="size-4" />
        </Toast.Close>
      )}
    </Toast.Root>
  );
}
```

---

## 🏗️ 4. Real `AnchoredToastProvider` Code (Anchored Notifications)

Unlike traditional floating toasts, anchored toasts are ideal for persistent alerts focused on screen elements (e.g., validation warning near a failed button or input). The provider below dynamically manages virtual or real anchors.

Create the file at `apps/web/src/components/ui/toast/anchored-toast.tsx` or equivalent:

> [!WARNING]
> **Browser Compatibility**: The `AnchoredToastProvider` uses the [CSS Anchor Positioning API](https://caniuse.com/css-anchor-positioning) (`anchor-name`, `position-anchor`). Available only in **Chrome 125+ / Edge 125+**. Firefox and Safari do not yet support it. Use as **progressive enhancement** — the toast still appears, but may not position correctly on unsupported browsers.

```tsx
import { type ReactNode, createContext, useContext, useState } from 'react';
import * as Toast from '@base-ui/react/toast';

interface AnchoredToastContextProps {
  showAnchored: (anchorId: string, message: string, duration?: number) => void;
  hideAnchored: (anchorId: string) => void;
}

const AnchoredToastContext = createContext<AnchoredToastContextProps | undefined>(undefined);

export function AnchoredToastProvider({ children }: { children: ReactNode }) {
  const [activeToasts, setActiveToasts] = useState<Record<string, { message: string }>>({});

  const showAnchored = (anchorId: string, message: string, duration = 3000) => {
    setActiveToasts((prev) => ({ ...prev, [anchorId]: { message } }));
    if (duration !== Infinity) {
      setTimeout(() => {
        hideAnchored(anchorId);
      }, duration);
    }
  };

  const hideAnchored = (anchorId: string) => {
    setActiveToasts((prev) => {
      const copy = { ...prev };
      delete copy[anchorId];
      return copy;
    });
  };

  return (
    <AnchoredToastContext.Provider value={{ showAnchored, hideAnchored }}>
      <Toast.Provider>
        {children}

        {/* Dynamically renders anchored portals */}
        {Object.entries(activeToasts).map(([anchorId, { message }]) => (
          <Toast.Root
            key={anchorId}
            open={true}
            onOpenChange={(open) => !open && hideAnchored(anchorId)}
            className="z-50 bg-slate-12 text-slate-1 border border-slate-11 px-3 py-1.5 rounded-md text-xs font-semibold shadow-md max-w-xs animate-in zoom-in-95 fade-in-10"
            // CSS Anchor Positioning — Chrome 125+ / Edge 125+
            style={{
              position: 'absolute',
              // @ts-expect-error — experimental CSS property not yet typed in TS
              positionAnchor: `--${anchorId}`,
              top: 'anchor(bottom)',
              left: 'anchor(center)',
              transform: 'translateX(-50%) translateY(4px)',
            }}
          >
            <Toast.Description>{message}</Toast.Description>
          </Toast.Root>
        ))}
      </Toast.Provider>
    </AnchoredToastContext.Provider>
  );
}

export function useAnchoredToast() {
  const context = useContext(AnchoredToastContext);
  if (!context) {
    throw new Error('useAnchoredToast must be used inside an AnchoredToastProvider');
  }
  return context;
}
```

---

## 📝 5. Practical Usage Examples

### 5.1 Simple Sync Toasts
```tsx
import { toastManager } from '@/components/ui/toast/toast-store';

function handleSaveSettings() {
  try {
    // Execute action...
    toastManager.success('Settings saved successfully!');
  } catch (error) {
    toastManager.error('Failed to save settings.');
  }
}
```

### 5.2 Async Promise-Based Toasts (Loaders)
```tsx
import { toastManager } from '@/components/ui/toast/toast-store';
import { apiClient } from '@/lib/api-client';

async function handleImportBilling() {
  const importPromise = apiClient.post('/billing/import', { year: 2026 });

  // Manages loading, success and error in a single line
  await toastManager.promise(importPromise, {
    loading: 'Processing billing file...',
    success: 'Billing imported and processed successfully!',
    error: 'Critical error processing the file. Check the logs.',
  });
}
```

### 5.3 Anchored Notifications
For anchoring to work, the button or input must receive a custom CSS `anchor-name` property.

```tsx
import { useAnchoredToast } from '@/components/ui/toast/anchored-toast';

export function ExportButton() {
  const { showAnchored } = useAnchoredToast();

  const handleExport = () => {
    showAnchored('export-btn', 'Export started in the background!', 3000);
  };

  return (
    <button
      id="export-btn"
      onClick={handleExport}
      className="bg-brand-9 text-white px-4 py-2 rounded-md hover:bg-brand-10"
      // Defines the anchor for CSS to position the anchored toast — Chrome 125+
      // @ts-expect-error — experimental CSS property
      style={{ anchorName: '--export-btn' }}
    >
      Export Data
    </button>
  );
}
```

---

## ⚙️ 6. How to Validate and Audit

Whenever the AI or developer makes changes to the Toast system, audit the following:
1. **ARIA Accessibility**: Ensure toasts have correct accessibility roles (using `Toast.Root` and `Toast.Description` primitives from Base UI).
2. **Keyboard Focus**: Ensure toast appearance does **not** steal keyboard focus from the user (`autoFocus={false}` property active).
3. **Responsive Behavior**: Verify the Toast panel adjusts to screen size and stacks correctly on mobile devices.
