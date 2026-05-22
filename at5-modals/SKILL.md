---
name: at5-modals
description: Declarative modal and form dialog patterns for AT5 projects using a Modal facade over NiceModal + React Hook Form + Zod. Use when creating modals, dialogs, form popups, confirmation dialogs, alert dialogs, or any overlay workflow. Always trigger on keywords like modal, dialog, form dialog, popup, createFormDialog, createAlertDialog, close guard, Modal.Provider, Modal.show, or useModal. Includes sidebar tab layout, dirty form protection, and async close confirmation.
---

# at5-modals — High-Performance Declarative Dialogs and Forms

This autonomous skill documents the **Declarative Dialogs** architecture in the **AT5** ecosystem, eliminating JSX pollution and unnecessary manual wrappers. It presents the complete infrastructure code for **`createFormDialog`** and **`createAlertDialog`**, integrated with **React Hook Form**, **Zod**, and the `Modal` compound component from the design system.

The entire NiceModal API is hidden behind a single facade file (`modal-provider.tsx`). **No project file ever imports `@ebay/nice-modal-react` directly** — only the facade does.

---

## 🛠️ 1. The Declarative Philosophy

Instead of controlling manual `isOpen`, `onClose` states in the parent component and polluting the JSX tree with modals, AT5 adopts a **Modal facade** wrapping NiceModal. Any dialog in the system is dispatched declaratively in a sync/async manner:

```typescript
import { Modal } from '@/providers/modal-provider';
import { UserFormDialog } from './user-form-dialog';

// Dispatch and await form success asynchronously
async function handleEditUser(user) {
  try {
    const result = await Modal.show(UserFormDialog, { mode: 'edit', user });
    console.log('User updated successfully:', result);
  } catch (err) {
    // Silent cancellation or close
  }
}
```

> [!NOTE]
> **Import Aliases**: All code below uses the illustrative aliases `@/components/ui` (or `@/packages/ui`), `@/providers/modal-provider`, and `@/lib/utils` to ensure agnostic compatibility. When implementing in a specific project, adapt the import aliases according to the `tsconfig.json` and local directory structure.

---

## 🏗️ 2. The Modal Facade (`modal-provider.tsx`)

This is the **single file** in the project that imports from `@ebay/nice-modal-react`. Every other file that needs modal functionality imports from here. If the underlying library ever changes, only this file needs to be updated.

Create at `apps/web/src/providers/modal-provider.tsx`:

```tsx
import NiceModal, { useModal as _useModal } from '@ebay/nice-modal-react';

/**
 * Central facade for all modal functionality.
 * This is the ONLY file in the project that imports @ebay/nice-modal-react.
 *
 * - Modal.Provider  → wrap the app root (main.tsx)
 * - Modal.show      → dispatch a dialog imperatively
 * - Modal.hide      → hide a dialog by id or component
 * - Modal.remove    → fully unmount a dialog
 * - Modal.create    → register a component as a managed modal (used in helpers)
 */
export const Modal = {
  Provider: NiceModal.Provider,
  show:     NiceModal.show,
  hide:     NiceModal.hide,
  remove:   NiceModal.remove,
  create:   NiceModal.create,
} as const;

/** Hook for use inside modal components (registered via Modal.create). */
export const useModal = _useModal;
```

### Usage in `main.tsx`

```tsx
import { Modal } from '@/providers/modal-provider';

createRoot(document.getElementById('root')!).render(
  <StrictMode>
    <Modal.Provider>
      <App />
    </Modal.Provider>
  </StrictMode>
);
```

---

## 🏗️ 3. Real `createFormDialog` Helper Code

This is the definitive helper (`apps/web/src/lib/create-form-dialog.tsx` or equivalent) that automates form modal creation. It supports:
1. **Sidebar Layout**: Vertical side menus on desktop and horizontal scrollable tab bar on mobile.
2. **Smart Close Guard**: Blocks accidental modal closure if the form is `isDirty` (modified), dispatching a confirmation dialog.
3. **Pending States**: Automatic loading state management on form submission.

```tsx
import { type ComponentType, useEffect, useRef, useState } from 'react';
import { Modal, useModal } from '@/providers/modal-provider';
import { Modal as ModalUI } from '@/components/ui/modal';
import { Button } from '@/components/ui/button';
import { ChevronRight } from '@untitled-ui/icons-react';

export interface SidebarTabConfig {
  key: string;
  label: string;
  icon: ComponentType<{ className?: string }>;
}

export interface FormDialogInjectedProps {
  formId: string;
  onSuccess: (data?: any) => void;
  onPendingChange?: (isPending: boolean) => void;
  onSuccessChange?: (isDirty: boolean) => void; // notifies the close guard about form dirty state
  isDirty?: boolean;
  activeTab?: string;
  onTabChange?: (tab: string) => void;
}

export interface FormDialogConfig<TProps extends object> {
  title: string | ((props: TProps) => string);
  submitLabel?: string | ((props: TProps) => string);
  cancelLabel?: string;
  size?: 'xs' | 'sm' | 'md' | 'lg' | 'xl' | '2xl' | '3xl' | '4xl' | '5xl' | 'cover' | 'full' | ((props: TProps) => any);
  isDismissable?: boolean;
  backdropVariant?: 'opaque' | 'blur' | 'transparent';
  placement?: 'auto' | 'center' | 'bottom' | 'top';
  sidebar?: {
    tabs: SidebarTabConfig[] | ((props: TProps) => SidebarTabConfig[]);
    when?: (props: TProps) => boolean;
    defaultTab?: string | ((props: TProps) => string);
  };
}

export function createFormDialog<TProps extends object>(
  FormComponent: ComponentType<TProps & FormDialogInjectedProps>,
  config: FormDialogConfig<TProps>
) {
  const FORM_ID = `form-${Math.random().toString(36).substring(2, 9)}`;

  return Modal.create((props: TProps) => {
    const modal = useModal();
    const [isPending, setIsPending] = useState(false);
    const [activeTab, setActiveTab] = useState<string>(() => {
      if (!config.sidebar) return '';
      const def = config.sidebar.defaultTab;
      return (typeof def === 'function' ? def(props) : def) ??
        (typeof config.sidebar.tabs === 'function' ? config.sidebar.tabs(props)[0]?.key : config.sidebar.tabs[0]?.key) ?? '';
    });

    const [resetKey, setResetKey] = useState(0);
    const isFirstRender = useRef(true);
    const isDirtyRef = useRef(false);

    useEffect(() => {
      if (isFirstRender.current) { isFirstRender.current = false; return; }
      if (modal.visible) {
        if (config.sidebar) {
          const def = config.sidebar.defaultTab;
          setActiveTab((typeof def === 'function' ? def(props) : def) ?? '');
        }
        setResetKey((k) => k + 1);
        isDirtyRef.current = false;
      }
    }, [modal.visible, props]);

    const resolveTitle = typeof config.title === 'function' ? config.title(props) : config.title;
    const resolveSubmitLabel = typeof config.submitLabel === 'function' ? config.submitLabel(props) : (config.submitLabel ?? 'Confirm');
    const resolveSize = typeof config.size === 'function' ? config.size(props) : (config.size ?? 'md');

    // Close Guard: If form is dirty, open confirmation via createAlertDialog (design system)
    const handleCloseAttempt = async () => {
      if (isDirtyRef.current) {
        const { DiscardChangesDialog } = await import('./create-alert-dialog'); // adjust path
        const confirmed = await Modal.show(DiscardChangesDialog);
        if (!confirmed) return;
      }
      modal.hide();
    };

    // Example DiscardChangesDialog to use in close guard:
    // export const DiscardChangesDialog = createAlertDialog({
    //   title: 'Discard changes?',
    //   description: 'You have unsaved changes. Closing will discard all changes.',
    //   confirmLabel: 'Discard',
    //   cancelLabel: 'Keep editing',
    //   variant: 'danger',
    // });

    const handleSuccess = (data?: any) => {
      modal.resolve(data);
      modal.hide();
    };

    const sidebarConfig = config.sidebar;
    const showSidebar = sidebarConfig?.when ? sidebarConfig.when(props) : !!sidebarConfig;
    const tabs = sidebarConfig ? (typeof sidebarConfig.tabs === 'function' ? sidebarConfig.tabs(props) : sidebarConfig.tabs) : [];

    return (
      <ModalUI open={modal.visible} onOpenChange={(open) => !open && handleCloseAttempt()}>
        <ModalUI.Backdrop isDismissable={config.isDismissable ?? true} variant={config.backdropVariant ?? 'opaque'}>
          <ModalUI.Container size={resolveSize} placement={config.placement ?? 'auto'} scroll="inside">
            <ModalUI.Dialog>
              <ModalUI.CloseTrigger onClick={(e) => { e.preventDefault(); handleCloseAttempt(); }} />
              <ModalUI.Header>
                <ModalUI.Heading>{resolveTitle}</ModalUI.Heading>
              </ModalUI.Header>

              {showSidebar && tabs.length > 0 ? (
                /* Sidebar Compound Layout */
                <div className="mt-2 min-h-0 flex-1 flex flex-col sm:flex-row overflow-hidden">
                  {/* Desktop Sidebar */}
                  <nav className="hidden w-48 shrink-0 border-r border-slate-6 sm:flex flex-col py-2 px-2 gap-1 bg-slate-2">
                    {tabs.map(({ key, label, icon: Icon }) => (
                      <button key={key} type="button" onClick={() => setActiveTab(key)}
                        className={`flex items-center gap-2.5 px-3 py-2 rounded-md text-sm transition-colors text-left border-l-2 ${
                          activeTab === key
                            ? 'bg-blue-3 text-blue-11 font-medium border-blue-9'
                            : 'text-slate-11 hover:bg-slate-4 border-transparent'
                        }`}
                      >
                        <Icon className="size-4 shrink-0" />
                        <span className="truncate">{label}</span>
                      </button>
                    ))}
                  </nav>

                  {/* Mobile horizontal scroll tabs */}
                  <div className="flex gap-1 overflow-x-auto border-b border-slate-6 px-4 py-3 sm:hidden shrink-0">
                    {tabs.map(({ key, label, icon: Icon }) => (
                      <button key={key} type="button" onClick={() => setActiveTab(key)}
                        className={`flex items-center gap-1.5 px-3 py-1.5 rounded-md text-xs whitespace-nowrap transition-colors ${
                          activeTab === key
                            ? 'bg-blue-9 text-white font-medium shadow-sm'
                            : 'bg-slate-3 text-slate-11 hover:bg-slate-4'
                        }`}
                      >
                        <Icon className="size-3.5" />
                        <span>{label}</span>
                      </button>
                    ))}
                  </div>

                  {/* Form container with independent scroll */}
                  <div className="flex-1 min-h-0 overflow-y-auto p-6 flex flex-col">
                    <FormComponent
                      key={resetKey}
                      {...props}
                      formId={FORM_ID}
                      onSuccess={handleSuccess}
                      onPendingChange={setIsPending}
                      activeTab={activeTab}
                      onTabChange={setActiveTab}
                      onSuccessChange={(dirty: boolean) => { isDirtyRef.current = dirty; }}
                    />
                  </div>
                </div>
              ) : (
                /* Classic Modal without Sidebar */
                <ModalUI.Body>
                  <FormComponent
                    key={resetKey}
                    {...props}
                    formId={FORM_ID}
                    onSuccess={handleSuccess}
                    onPendingChange={setIsPending}
                    onSuccessChange={(dirty: boolean) => { isDirtyRef.current = dirty; }}
                  />
                </ModalUI.Body>
              )}

              <ModalUI.Footer>
                <Button variant="outline" accent="secondary" type="button" onClick={handleCloseAttempt}>
                  {config.cancelLabel ?? 'Cancel'}
                </Button>
                <Button type="submit" form={FORM_ID} isLoading={isPending}>
                  {resolveSubmitLabel}
                </Button>
              </ModalUI.Footer>
            </ModalUI.Dialog>
          </ModalUI.Container>
        </ModalUI.Backdrop>
      </ModalUI>
    );
  });
}
```

> [!NOTE]
> `Modal` (from `@/providers/modal-provider`) is the imperative facade. `ModalUI` (aliased from `@/components/ui/modal`) is the visual compound component. They are distinct — keep the alias to avoid confusion.

---

## 🏗️ 4. Real `createAlertDialog` Helper Code

Perfect for simple confirmation modals for destructive actions (e.g., "Delete Record"), blocks, or security warnings:

```tsx
import { Modal, useModal } from '@/providers/modal-provider';
import { Modal as ModalUI } from '@/components/ui/modal';
import { Button } from '@/components/ui/button';

export interface AlertDialogConfig {
  title: string;
  description: string;
  confirmLabel?: string;
  cancelLabel?: string;
  variant?: 'danger' | 'warning' | 'primary';
}

export function createAlertDialog(config: AlertDialogConfig) {
  return Modal.create(() => {
    const modal = useModal();

    const handleConfirm = () => {
      modal.resolve(true);
      modal.hide();
    };

    const handleCancel = () => {
      modal.resolve(false);
      modal.hide();
    };

    return (
      <ModalUI open={modal.visible} onOpenChange={(open) => !open && handleCancel()}>
        <ModalUI.Backdrop isDismissable={true} variant="blur">
          <ModalUI.Container size="sm" placement="center">
            <ModalUI.Dialog>
              <ModalUI.Header>
                <ModalUI.Heading>{config.title}</ModalUI.Heading>
              </ModalUI.Header>
              <ModalUI.Body>
                <p className="text-sm text-slate-11 leading-relaxed">{config.description}</p>
              </ModalUI.Body>
              <ModalUI.Footer>
                <Button variant="outline" accent="secondary" type="button" onClick={handleCancel}>
                  {config.cancelLabel ?? 'Cancel'}
                </Button>
                <Button
                  type="button"
                  accent={config.variant === 'danger' ? 'danger' : 'primary'}
                  onClick={handleConfirm}
                >
                  {config.confirmLabel ?? 'Confirm'}
                </Button>
              </ModalUI.Footer>
            </ModalUI.Dialog>
          </ModalUI.Container>
        </ModalUI.Backdrop>
      </ModalUI>
    );
  });
}
```

---

## 📝 5. Complete Real Example: `UserDialog` Form Dialog

Here is how a developer or agent should implement a declarative form screen according to AT5 standards:

### File `UserForm.tsx` (Form Component)
```tsx
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { z } from 'zod';
import { Input, Textarea } from '@/components/ui';
import { FormDialogInjectedProps } from './create-form-dialog';
import { useEffect } from 'react';

const userSchema = z.object({
  name: z.string().min(3, 'Name must be at least 3 characters'),
  email: z.string().email('Invalid email address'),
  bio: z.string().optional(),
});

type UserFormData = z.infer<typeof userSchema>;

interface UserFormProps extends FormDialogInjectedProps {
  mode: 'create' | 'edit';
  initialData?: UserFormData;
}

export function UserForm({ formId, mode, initialData, onSuccess, onPendingChange, onSuccessChange }: UserFormProps) {
  const { register, handleSubmit, formState: { errors, isSubmitting, isDirty } } = useForm<UserFormData>({
    resolver: zodResolver(userSchema),
    defaultValues: initialData,
  });

  // Notify close guard about form changes
  useEffect(() => {
    onSuccessChange?.(isDirty);
  }, [isDirty, onSuccessChange]);

  // Notify loading button about pending submission
  useEffect(() => {
    onPendingChange?.(isSubmitting);
  }, [isSubmitting, onPendingChange]);

  const onSubmit = async (data: UserFormData) => {
    await new Promise((resolve) => setTimeout(resolve, 1500));
    console.log('Payload sent:', data);
    onSuccess(data);
  };

  return (
    <form id={formId} onSubmit={handleSubmit(onSubmit)} className="space-y-4">
      <div>
        <label className="block text-xs font-semibold text-slate-12 mb-1.5">Full Name</label>
        <Input {...register('name')} placeholder="Enter user name" aria-invalid={!!errors.name} />
        {errors.name && <p className="mt-1 text-xs text-red-11">{errors.name.message}</p>}
      </div>

      <div>
        <label className="block text-xs font-semibold text-slate-12 mb-1.5">Email Address</label>
        <Input {...register('email')} type="email" placeholder="user@example.com" aria-invalid={!!errors.email} />
        {errors.email && <p className="mt-1 text-xs text-red-11">{errors.email.message}</p>}
      </div>

      <div>
        <label className="block text-xs font-semibold text-slate-12 mb-1.5">Bio</label>
        <Textarea {...register('bio')} placeholder="Tell us about the user..." />
      </div>
    </form>
  );
}
```

### File `UserDialog.tsx` (Declarative Configuration)
```tsx
import { createFormDialog } from './create-form-dialog';
import { UserForm } from './UserForm';

export const UserDialog = createFormDialog(UserForm, {
  title: (props) => props.mode === 'edit' ? 'Edit User Profile' : 'Create New User',
  submitLabel: (props) => props.mode === 'edit' ? 'Save Changes' : 'Create Account',
  size: 'lg',
});
```

### Dispatching the Dialog
```tsx
import { Modal } from '@/providers/modal-provider';
import { UserDialog } from './UserDialog';

async function handleEditUser(user) {
  try {
    const result = await Modal.show(UserDialog, { mode: 'edit', user });
    console.log('User updated:', result);
  } catch {
    // User cancelled
  }
}
```

---

## ⚙️ 6. Import Rule Summary

| What you need | Import from |
|---|---|
| Wrap the app root | `import { Modal } from '@/providers/modal-provider'` → `<Modal.Provider>` |
| Dispatch a dialog | `import { Modal } from '@/providers/modal-provider'` → `Modal.show(...)` |
| Register a modal component | `import { Modal } from '@/providers/modal-provider'` → `Modal.create(...)` |
| Hook inside a modal | `import { useModal } from '@/providers/modal-provider'` |
| Visual modal shell | `import { Modal as ModalUI } from '@/components/ui/modal'` |
| **Never** | `import ... from '@ebay/nice-modal-react'` (only `modal-provider.tsx` may do this) |
