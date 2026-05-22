# 📦 Overlays & Feedback

Caixas de diálogo, modais flutuantes, popovers, alertas, menus de contexto, folhas deslizantes e dicas de ferramentas (tooltips).

---

## 📂 `dialog.tsx`

Caminho original no repositório: `/packages/ui/src/components/dialog.tsx`

```tsx
import { Dialog as DialogPrimitive } from '@base-ui/react/dialog';
import { cn } from '@manager/lib';
import { XClose } from '@untitled-ui/icons-react';
import type * as React from 'react';
import { Button } from './button';

function Dialog({ ...props }: DialogPrimitive.Root.Props) {
  return <DialogPrimitive.Root data-slot="dialog" {...props} />;
}

function DialogTrigger({ ...props }: DialogPrimitive.Trigger.Props) {
  return <DialogPrimitive.Trigger data-slot="dialog-trigger" {...props} />;
}

function DialogPortal({ ...props }: DialogPrimitive.Portal.Props) {
  return <DialogPrimitive.Portal data-slot="dialog-portal" {...props} />;
}

function DialogClose({ ...props }: DialogPrimitive.Close.Props) {
  return (
    <DialogPrimitive.Close
      data-slot="dialog-close"
      {...props}
      render={<Button type="button" variant="outline" />}
    />
  );
}

function DialogViewport({ className, ...props }: DialogPrimitive.Viewport.Props) {
  return (
    <DialogPrimitive.Viewport
      data-slot="dialog-viewport"
      className={cn(
        'fixed inset-0 z-50 flex items-center justify-center',
        'overflow-hidden py-6', // Previne scroll da página
        '[@media(min-height:600px)]:pb-12 [@media(min-height:600px)]:pt-8', // Padding maior em telas altas
        className,
      )}
      {...props}
    />
  );
}

function DialogOverlay({ className, ...props }: DialogPrimitive.Backdrop.Props) {
  return (
    <DialogPrimitive.Backdrop
      data-slot="dialog-overlay"
      className={cn(
        'fixed inset-0 z-50 bg-black/10',
        'dark:bg-black/70',
        'data-[starting-style]:opacity-0 data-[ending-style]:opacity-0',
        'transition-opacity duration-150',
        'supports-[-webkit-touch-callout:none]:absolute',
        className,
      )}
      {...props}
    />
  );
}

function DialogContent({
  className,
  children,
  showCloseButton = true,
  ...props
}: DialogPrimitive.Popup.Props & {
  showCloseButton?: boolean;
}) {
  return (
    <DialogPortal>
      <DialogOverlay />
      <DialogViewport>
        <DialogPrimitive.Popup
          data-slot="dialog-content"
          className={cn(
            // Base styles
            'bg-white dark:bg-zinc-950',
            'ring-1 ring-zinc-950/10 dark:ring-zinc-50/10',
            'rounded-xl shadow-lg p-6 text-sm',
            'w-full max-w-[calc(100%-2rem)] sm:max-w-md',
            'max-h-[85vh] overflow-hidden', // Altura máxima com overflow-hidden para scroll interno
            'flex flex-col gap-6', // Para layout flexível com espaçamento

            // Nested dialog support
            'fixed left-1/2 -translate-x-1/2',
            'top-[calc(50%+1.25rem*var(--nested-dialogs))]', // Offset para nested
            '-translate-y-1/2',
            'scale-[calc(1-0.1*var(--nested-dialogs))]', // Escala reduzida

            // Overlay quando há dialog filho
            'data-[nested-dialog-open]:after:absolute',
            'data-[nested-dialog-open]:after:inset-0',
            'data-[nested-dialog-open]:after:rounded-[inherit]',
            'data-[nested-dialog-open]:after:bg-black/5',

            // Animations
            'data-[starting-style]:scale-90 data-[starting-style]:opacity-0',
            'data-[ending-style]:scale-90 data-[ending-style]:opacity-0',
            'transition-all duration-150',
            'outline-none',

            className,
          )}
          {...props}
        >
          {children}
          {showCloseButton && (
            <DialogPrimitive.Close
              data-slot="dialog-close"
              render={<Button variant="ghost" className="absolute top-4 right-4" size="icon-sm" />}
            >
              <XClose strokeWidth={2} />
              <span className="sr-only">Close</span>
            </DialogPrimitive.Close>
          )}
        </DialogPrimitive.Popup>
      </DialogViewport>
    </DialogPortal>
  );
}

function DialogBody({
  className,
  disableScrollArea = false,
  ...props
}: React.ComponentProps<'div'> & {
  disableScrollArea?: boolean;
}) {
  return (
    <div className={cn('flex-1 min-h-0', disableScrollArea ? 'flex flex-col' : 'overflow-y-auto')}>
      <div data-slot="dialog-body" className={cn('px-6 py-4', className)} {...props} />
    </div>
  );
}

function DialogHeader({ className, ...props }: React.ComponentProps<'div'>) {
  return (
    <div
      data-slot="dialog-header"
      className={cn('shrink-0 gap-2 flex flex-col', className)}
      {...props}
    />
  );
}

function DialogFooter({
  className,
  showCloseButton = false,
  children,
  ...props
}: React.ComponentProps<'div'> & {
  showCloseButton?: boolean;
}) {
  return (
    <div
      data-slot="dialog-footer"
      className={cn('shrink-0 gap-2 flex flex-col-reverse sm:flex-row sm:justify-end', className)}
      {...props}
    >
      {children}
      {showCloseButton && (
        <DialogPrimitive.Close render={<Button variant="outline" />}>Close</DialogPrimitive.Close>
      )}
    </div>
  );
}

function DialogTitle({ className, ...props }: DialogPrimitive.Title.Props) {
  return (
    <DialogPrimitive.Title
      data-slot="dialog-title"
      className={cn('leading-none font-medium', className)}
      {...props}
    />
  );
}

function DialogDescription({ className, ...props }: DialogPrimitive.Description.Props) {
  return (
    <DialogPrimitive.Description
      data-slot="dialog-description"
      className={cn(
        'text-zinc-500 *:[a]:hover:text-zinc-950 text-sm *:[a]:underline *:[a]:underline-offset-3 dark:text-zinc-400 dark:*:[a]:hover:text-zinc-50',
        className,
      )}
      {...props}
    />
  );
}

export {
  Dialog,
  DialogBody,
  DialogClose,
  DialogContent,
  DialogDescription,
  DialogFooter,
  DialogHeader,
  DialogOverlay,
  DialogPortal,
  DialogTitle,
  DialogTrigger,
  DialogViewport,
};
```

---

## 📂 `alert-dialog.tsx`

Caminho original no repositório: `/packages/ui/src/components/alert-dialog.tsx`

```tsx
'use client';

import { AlertDialog as AlertDialogPrimitive } from '@base-ui/react/alert-dialog';
import { cn } from '@manager/lib';
import {
  AlertCircle,
  AlertTriangle,
  CheckCircle,
  InfoCircle,
  XClose,
} from '@untitled-ui/icons-react';
import {
  type ComponentPropsWithRef,
  type ReactNode,
  type RefObject,
  createContext,
  useCallback,
  useContext,
  useEffect,
  useMemo,
  useRef,
} from 'react';
import { type VariantProps, tv } from 'tailwind-variants';
import { Button } from './button';

export const alertDialogVariants = tv({
  slots: {
    trigger: [
      'cursor-pointer',
      'transition-[transform,background-color,box-shadow] duration-[150ms] ease-out',
      'data-[popup-open]:scale-[0.985]',
      'data-disabled:pointer-events-none data-disabled:opacity-50',
      'active:scale-[0.97]',
      'focus-visible:outline-none',
    ],
    backdrop: [
      'fixed inset-0 z-50 min-h-dvh',
      'bg-black/50',
      'transition-opacity duration-150 ease-out',
      'data-[starting-style]:opacity-0 data-[ending-style]:opacity-0',
      'supports-[-webkit-touch-callout:none]:absolute',
      'dark:bg-black/60',
    ],
    viewport: [
      'fixed inset-0 z-50 flex min-h-dvh w-full min-w-0 flex-col items-center p-4 sm:p-10',
      'pointer-events-none',
      'transition-[opacity,transform] duration-[250ms] ease-[cubic-bezier(0.25,0.46,0.45,0.94)]',
      'data-[ending-style]:duration-100',
      'data-[starting-style]:opacity-0 data-[ending-style]:opacity-0',
      'data-[starting-style]:scale-[1.05] data-[ending-style]:scale-95',
      'supports-[-webkit-touch-callout:none]:absolute',
    ],
    popup: [
      'relative flex w-full flex-col',
      'rounded-3xl bg-slate-1 text-slate-12 ring-1 ring-slate-6/60',
      'shadow-[0_28px_80px_-32px_rgba(15,23,42,0.45),0_22px_44px_-36px_rgba(15,23,42,0.32)]',
      'max-h-[calc(100dvh-2rem)] overflow-hidden p-6',
      'pointer-events-auto outline-none sm:max-h-[calc(100dvh-5rem)]',
      'dark:bg-slate-2 dark:ring-slate-7/60',
    ],
    header: 'mb-0 flex flex-col gap-3',
    title: 'align-middle text-base font-medium text-slate-12',
    description: 'my-0 text-sm leading-[1.43] text-slate-11',
    icon: 'flex size-10 shrink-0 select-none items-center justify-center rounded-full',
    body: 'my-0 min-h-0 flex-1 overflow-y-auto text-sm leading-[1.43] text-slate-11',
    footer: 'mt-4 flex flex-row items-center justify-end gap-2',
    closeTrigger: [
      'absolute top-4 right-4 inline-flex size-8 items-center justify-center rounded-full',
      'text-slate-11 transition-[transform,background-color,color,box-shadow] duration-150 ease-out',
      'hover:bg-slate-4 hover:text-slate-12',
      'active:scale-95',
      'focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-blue-7 focus-visible:ring-offset-2 focus-visible:ring-offset-slate-1',
      'dark:focus-visible:ring-offset-slate-2',
    ],
  },
  variants: {
    backdropVariant: {
      opaque: { backdrop: 'bg-black/50 dark:bg-black/60' },
      blur: { backdrop: 'bg-black/50 backdrop-blur-md dark:bg-black/60' },
      transparent: { backdrop: 'bg-transparent' },
    },
    placement: {
      auto: {
        viewport:
          'justify-end data-[starting-style]:translate-y-1 data-[ending-style]:translate-y-1 sm:justify-center sm:data-[starting-style]:translate-y-0 sm:data-[ending-style]:translate-y-0',
        popup: 'mt-auto sm:my-auto',
      },
      center: {
        viewport: 'justify-center',
        popup: 'my-auto',
      },
      bottom: {
        viewport:
          'justify-end data-[starting-style]:translate-y-1 data-[ending-style]:translate-y-1',
        popup: 'mt-auto',
      },
      top: {
        viewport:
          'justify-start data-[starting-style]:-translate-y-1 data-[ending-style]:-translate-y-1',
        popup: 'mt-0',
      },
    },
    size: {
      xs: { popup: 'max-w-xs' },
      sm: { popup: 'max-w-sm' },
      md: { popup: 'max-w-md' },
      lg: { popup: 'max-w-lg' },
      cover: { popup: 'h-full min-h-full w-full' },
    },
    status: {
      default: { icon: 'bg-slate-4 text-slate-12' },
      accent: { icon: 'bg-blue-3 text-blue-11' },
      success: { icon: 'bg-green-3 text-green-11' },
      warning: { icon: 'bg-amber-3 text-amber-11' },
      danger: { icon: 'bg-red-3 text-red-11' },
    },
  },
  defaultVariants: {
    backdropVariant: 'opaque',
    placement: 'auto',
    size: 'md',
    status: 'danger',
  },
});

export type AlertDialogVariants = VariantProps<typeof alertDialogVariants>;

type AlertDialogBackdropVariant = 'opaque' | 'blur' | 'transparent';
type AlertDialogPlacement = 'auto' | 'top' | 'center' | 'bottom';
type AlertDialogSize = 'xs' | 'sm' | 'md' | 'lg' | 'cover';
export type AlertDialogStatus = 'default' | 'accent' | 'success' | 'warning' | 'danger';

type AlertDialogVisualContextValue = {
  placement: AlertDialogPlacement;
  size: AlertDialogSize;
};

const DEFAULT_VISUAL_CONTEXT: AlertDialogVisualContextValue = {
  placement: 'auto',
  size: 'md',
};

const AlertDialogVisualContext =
  createContext<AlertDialogVisualContextValue>(DEFAULT_VISUAL_CONTEXT);

type AlertDialogRuntimeContextValue = {
  close: () => void;
};

const AlertDialogRuntimeContext = createContext<AlertDialogRuntimeContextValue>({
  close: () => undefined,
});

function setRefValue<T>(ref: { current: T | null } | undefined, value: T | null) {
  if (ref) {
    ref.current = value;
  }
}

function useAlertDialogSlots({
  backdropVariant,
  placement,
  size,
  status,
}: {
  backdropVariant?: AlertDialogBackdropVariant;
  placement?: AlertDialogPlacement;
  size?: AlertDialogSize;
  status?: AlertDialogStatus;
} = {}) {
  const context = useContext(AlertDialogVisualContext);

  return useMemo(
    () =>
      alertDialogVariants({
        backdropVariant,
        placement: placement ?? context.placement,
        size: size ?? context.size,
        status,
      }),
    [backdropVariant, context.placement, context.size, placement, size, status],
  );
}

export interface AlertDialogRootProps<Payload = unknown>
  extends AlertDialogPrimitive.Root.Props<Payload> {}

function AlertDialogRoot<Payload = unknown>({
  actionsRef: externalActionsRef,
  children,
  ...props
}: AlertDialogRootProps<Payload>) {
  const internalActionsRef = useRef<AlertDialogPrimitive.Root.Actions | null>(null);

  useEffect(() => {
    setRefValue(
      externalActionsRef as { current: AlertDialogPrimitive.Root.Actions | null } | undefined,
      internalActionsRef.current,
    );
  });

  const close = useCallback(() => {
    internalActionsRef.current?.close();
  }, []);

  return (
    <AlertDialogRuntimeContext value={{ close }}>
      <AlertDialogVisualContext value={DEFAULT_VISUAL_CONTEXT}>
        <AlertDialogPrimitive.Root
          actionsRef={internalActionsRef as RefObject<AlertDialogPrimitive.Root.Actions>}
          {...props}
        >
          {children}
        </AlertDialogPrimitive.Root>
      </AlertDialogVisualContext>
    </AlertDialogRuntimeContext>
  );
}

export interface AlertDialogTriggerProps extends AlertDialogPrimitive.Trigger.Props {
  className?: string;
}

function AlertDialogTrigger({ className, children, ...props }: AlertDialogTriggerProps) {
  const slots = useAlertDialogSlots();

  return (
    <AlertDialogPrimitive.Trigger
      data-slot="alert-dialog-trigger"
      className={cn(slots.trigger(), className)}
      {...props}
    >
      {children}
    </AlertDialogPrimitive.Trigger>
  );
}

export interface AlertDialogPortalProps extends AlertDialogPrimitive.Portal.Props {}

function AlertDialogPortal({ ...props }: AlertDialogPortalProps) {
  return <AlertDialogPrimitive.Portal data-slot="alert-dialog-portal" {...props} />;
}

export interface AlertDialogBackdropProps extends AlertDialogPrimitive.Backdrop.Props {
  className?: string;
  variant?: AlertDialogBackdropVariant;
}

function AlertDialogBackdrop({
  className,
  variant = 'opaque',
  ...props
}: AlertDialogBackdropProps) {
  const slots = useAlertDialogSlots({ backdropVariant: variant });

  return (
    <AlertDialogPrimitive.Backdrop
      data-slot="alert-dialog-backdrop"
      className={cn(slots.backdrop(), className)}
      {...props}
    />
  );
}

export interface AlertDialogViewportProps
  extends Omit<AlertDialogPrimitive.Viewport.Props, 'children'> {
  children?: ReactNode;
  className?: string;
  placement?: AlertDialogPlacement;
}

function AlertDialogViewport({
  children,
  className,
  placement = 'auto',
  ...props
}: AlertDialogViewportProps) {
  const visualContext = useContext(AlertDialogVisualContext);
  const slots = useAlertDialogSlots({ placement });

  const nextVisualContext = useMemo<AlertDialogVisualContextValue>(
    () => ({ ...visualContext, placement }),
    [placement, visualContext],
  );

  return (
    <AlertDialogVisualContext value={nextVisualContext}>
      <AlertDialogPrimitive.Viewport
        data-slot="alert-dialog-viewport"
        data-placement={placement}
        className={cn(slots.viewport(), className)}
        {...props}
      >
        {children}
      </AlertDialogPrimitive.Viewport>
    </AlertDialogVisualContext>
  );
}

export interface AlertDialogPopupProps extends Omit<AlertDialogPrimitive.Popup.Props, 'children'> {
  children?: ReactNode;
  className?: string;
  size?: AlertDialogSize;
}

function AlertDialogPopup({ children, className, size = 'md', ...props }: AlertDialogPopupProps) {
  const visualContext = useContext(AlertDialogVisualContext);
  const slots = useAlertDialogSlots({ size });

  const nextVisualContext = useMemo<AlertDialogVisualContextValue>(
    () => ({ ...visualContext, size }),
    [size, visualContext],
  );

  return (
    <AlertDialogVisualContext value={nextVisualContext}>
      <AlertDialogPrimitive.Popup
        data-slot="alert-dialog-popup"
        className={cn(slots.popup(), className)}
        {...props}
      >
        {children}
      </AlertDialogPrimitive.Popup>
    </AlertDialogVisualContext>
  );
}

export interface AlertDialogHeaderProps extends ComponentPropsWithRef<'div'> {}

function AlertDialogHeader({ className, children, ...props }: AlertDialogHeaderProps) {
  const slots = useAlertDialogSlots();

  return (
    <div data-slot="alert-dialog-header" className={cn(slots.header(), className)} {...props}>
      {children}
    </div>
  );
}

export interface AlertDialogTitleProps extends AlertDialogPrimitive.Title.Props {
  className?: string;
}

function AlertDialogTitle({ className, children, ...props }: AlertDialogTitleProps) {
  const slots = useAlertDialogSlots();

  return (
    <AlertDialogPrimitive.Title
      data-slot="alert-dialog-title"
      className={cn(slots.title(), className)}
      {...props}
    >
      {children}
    </AlertDialogPrimitive.Title>
  );
}

type AlertDialogHeadingProps = AlertDialogTitleProps;

function AlertDialogHeading(props: AlertDialogHeadingProps) {
  return <AlertDialogTitle {...props} />;
}

export interface AlertDialogDescriptionProps extends AlertDialogPrimitive.Description.Props {
  className?: string;
}

function AlertDialogDescription({ className, children, ...props }: AlertDialogDescriptionProps) {
  const slots = useAlertDialogSlots();

  return (
    <AlertDialogPrimitive.Description
      data-slot="alert-dialog-description"
      className={cn(slots.description(), className)}
      {...props}
    >
      {children}
    </AlertDialogPrimitive.Description>
  );
}

export interface AlertDialogBodyProps extends ComponentPropsWithRef<'div'> {}

function AlertDialogBody({ className, children, ...props }: AlertDialogBodyProps) {
  const slots = useAlertDialogSlots();

  return (
    <div data-slot="alert-dialog-body" className={cn(slots.body(), className)} {...props}>
      {children}
    </div>
  );
}

export interface AlertDialogFooterProps extends ComponentPropsWithRef<'div'> {}

function AlertDialogFooter({ className, children, ...props }: AlertDialogFooterProps) {
  const slots = useAlertDialogSlots();

  return (
    <div data-slot="alert-dialog-footer" className={cn(slots.footer(), className)} {...props}>
      {children}
    </div>
  );
}

export interface AlertDialogIconProps extends ComponentPropsWithRef<'div'> {
  status?: AlertDialogStatus;
}

function AlertDialogIcon({
  children,
  className,
  status = 'danger',
  ...props
}: AlertDialogIconProps) {
  const slots = useAlertDialogSlots({ status });

  const defaultIcon = useMemo(() => {
    switch (status) {
      case 'default':
        return <InfoCircle data-slot="alert-dialog-default-icon" className="size-5" />;
      case 'accent':
        return <InfoCircle data-slot="alert-dialog-default-icon" className="size-5" />;
      case 'success':
        return <CheckCircle data-slot="alert-dialog-default-icon" className="size-5" />;
      case 'warning':
        return <AlertTriangle data-slot="alert-dialog-default-icon" className="size-5" />;
      default:
        return <AlertCircle data-slot="alert-dialog-default-icon" className="size-5" />;
    }
  }, [status]);

  return (
    <div data-slot="alert-dialog-icon" className={cn(slots.icon(), className)} {...props}>
      {children ?? defaultIcon}
    </div>
  );
}

type AlertDialogMediaProps = AlertDialogIconProps;

function AlertDialogMedia(props: AlertDialogMediaProps) {
  return <AlertDialogIcon {...props} />;
}

export interface AlertDialogCloseProps extends AlertDialogPrimitive.Close.Props {
  className?: string;
}

function AlertDialogClose({ children, className, ...props }: AlertDialogCloseProps) {
  return (
    <AlertDialogPrimitive.Close data-slot="alert-dialog-close" className={className} {...props}>
      {children}
    </AlertDialogPrimitive.Close>
  );
}

export interface AlertDialogCloseTriggerProps extends AlertDialogPrimitive.Close.Props {
  className?: string;
}

function AlertDialogCloseTrigger({ children, className, ...props }: AlertDialogCloseTriggerProps) {
  const slots = useAlertDialogSlots();

  return (
    <AlertDialogPrimitive.Close
      data-slot="alert-dialog-close-trigger"
      className={cn(slots.closeTrigger(), className)}
      {...props}
    >
      {children ?? (
        <>
          <XClose className="size-4" strokeWidth={2} />
          <span className="sr-only">Fechar</span>
        </>
      )}
    </AlertDialogPrimitive.Close>
  );
}

export interface AlertDialogActionProps extends ComponentPropsWithRef<typeof Button> {}

function AlertDialogAction({ className, ...props }: AlertDialogActionProps) {
  return <Button data-slot="alert-dialog-action" className={cn(className)} {...props} />;
}

export interface AlertDialogCancelProps
  extends AlertDialogPrimitive.Close.Props,
    Pick<ComponentPropsWithRef<typeof Button>, 'accent' | 'size' | 'variant'> {}

function AlertDialogCancel({
  accent = 'secondary',
  className,
  size = 'default',
  variant = 'outline',
  ...props
}: AlertDialogCancelProps) {
  return (
    <AlertDialogPrimitive.Close
      data-slot="alert-dialog-cancel"
      className={cn(className)}
      render={<Button accent={accent} size={size} variant={variant} />}
      {...props}
    />
  );
}

type LegacyAlertDialogSize = 'default' | 'sm' | AlertDialogSize;

function normalizeLegacySize(size: LegacyAlertDialogSize | undefined): AlertDialogSize {
  if (!size) return 'md';
  if (size === 'default') return 'md';
  if (size === 'sm') return 'sm';
  return size;
}

export interface AlertDialogContentProps extends Omit<AlertDialogPopupProps, 'size'> {
  backdropVariant?: AlertDialogBackdropVariant;
  container?: AlertDialogPrimitive.Portal.Props['container'];
  keepMounted?: AlertDialogPrimitive.Portal.Props['keepMounted'];
  placement?: AlertDialogPlacement;
  size?: LegacyAlertDialogSize;
}

function AlertDialogContent({
  backdropVariant = 'opaque',
  children,
  className,
  container,
  keepMounted,
  placement = 'auto',
  size = 'md',
  ...props
}: AlertDialogContentProps) {
  return (
    <AlertDialogPortal container={container} keepMounted={keepMounted}>
      <AlertDialogBackdrop variant={backdropVariant} />
      <AlertDialogViewport placement={placement}>
        <AlertDialogPopup className={className} size={normalizeLegacySize(size)} {...props}>
          {children}
        </AlertDialogPopup>
      </AlertDialogViewport>
    </AlertDialogPortal>
  );
}

const AlertDialog = Object.assign(AlertDialogRoot, {
  Root: AlertDialogRoot,
  Trigger: AlertDialogTrigger,
  Portal: AlertDialogPortal,
  Backdrop: AlertDialogBackdrop,
  Overlay: AlertDialogBackdrop,
  Viewport: AlertDialogViewport,
  Container: AlertDialogViewport,
  Popup: AlertDialogPopup,
  Dialog: AlertDialogPopup,
  Header: AlertDialogHeader,
  Title: AlertDialogTitle,
  Heading: AlertDialogHeading,
  Description: AlertDialogDescription,
  Body: AlertDialogBody,
  Footer: AlertDialogFooter,
  Icon: AlertDialogIcon,
  Media: AlertDialogMedia,
  Close: AlertDialogClose,
  CloseTrigger: AlertDialogCloseTrigger,
  Action: AlertDialogAction,
  Cancel: AlertDialogCancel,
  Content: AlertDialogContent,
  createHandle: AlertDialogPrimitive.createHandle,
});

const AlertDialogOverlay = AlertDialogBackdrop;
const AlertDialogContainer = AlertDialogViewport;
const AlertDialogDialog = AlertDialogPopup;

export {
  AlertDialog,
  AlertDialogRoot,
  AlertDialogTrigger,
  AlertDialogPortal,
  AlertDialogBackdrop,
  AlertDialogOverlay,
  AlertDialogViewport,
  AlertDialogContainer,
  AlertDialogPopup,
  AlertDialogDialog,
  AlertDialogHeader,
  AlertDialogTitle,
  AlertDialogHeading,
  AlertDialogDescription,
  AlertDialogBody,
  AlertDialogFooter,
  AlertDialogIcon,
  AlertDialogMedia,
  AlertDialogClose,
  AlertDialogCloseTrigger,
  AlertDialogAction,
  AlertDialogCancel,
  AlertDialogContent,
};
```

---

## 📂 `modal.tsx`

Caminho original no repositório: `/packages/ui/src/components/modal.tsx`

```tsx
'use client';

import { Dialog as DialogPrimitive } from '@base-ui/react/dialog';
import { cn } from '@manager/lib';
import { XClose } from '@untitled-ui/icons-react';
import {
  type ComponentPropsWithRef,
  type CSSProperties,
  type MouseEvent as ReactMouseEvent,
  type PointerEvent as ReactPointerEvent,
  type ReactNode,
  type RefObject,
  createContext,
  useCallback,
  useContext,
  useEffect,
  useMemo,
  useRef,
  useState,
} from 'react';
import { type VariantProps, tv } from 'tailwind-variants';

export const modalVariants = tv({
  slots: {
    trigger: [
      'cursor-pointer',
      'transition-[transform,background-color,box-shadow] duration-[150ms] ease-out',
      'data-[popup-open]:scale-[0.985]',
      'data-disabled:pointer-events-none data-disabled:opacity-50',
      'active:scale-[0.97]',
      'focus-visible:outline-none',
    ],
    backdrop: [
      'fixed inset-0 z-50 min-h-dvh',
      'bg-black/50',
      'transition-opacity duration-150 ease-out',
      'data-[starting-style]:opacity-0 data-[ending-style]:opacity-0',
      'supports-[-webkit-touch-callout:none]:absolute',
      'dark:bg-black/60',
    ],
    container: [
      'fixed inset-0 z-50 flex min-h-dvh w-full min-w-0 flex-col items-center p-4 sm:p-10',
      'pointer-events-none',
      'transition-[opacity,transform] duration-[250ms] ease-[cubic-bezier(0.25,0.46,0.45,0.94)]',
      'data-[ending-style]:duration-100',
      'data-[starting-style]:opacity-0 data-[ending-style]:opacity-0',
      'data-[starting-style]:scale-[1.05] data-[ending-style]:scale-95',
      'supports-[-webkit-touch-callout:none]:absolute',
    ],
    dialog: [
      'relative flex w-full flex-col',
      'rounded-3xl bg-slate-1 text-slate-12 ring-1 ring-slate-6/60',
      'shadow-[0_28px_80px_-32px_rgba(15,23,42,0.45),0_22px_44px_-36px_rgba(15,23,42,0.32)]',
      'p-6',
      'pointer-events-auto outline-none',
      ' dark:ring-slate-7/60',
    ],
    header:
      '-mx-6 -mt-6 mb-5 flex flex-col gap-3 rounded-t-3xl border-b border-slate-5 bg-slate-2 px-6 pt-6 pb-5',
    heading: 'align-middle text-base font-medium text-slate-12',
    description: 'text-sm text-slate-11',
    icon: 'flex size-10 shrink-0 select-none items-center justify-center rounded-full',
    body: 'my-0 min-h-0 flex-1 text-sm leading-[1.43] text-slate-11',
    footer:
      '-mx-6 -mb-6 mt-5 flex flex-row items-center justify-end gap-2 rounded-b-3xl border-t border-slate-5 bg-slate-2 px-6 pt-5 pb-6',
    closeTrigger: [
      'absolute top-4 right-4 inline-flex size-8 items-center justify-center rounded-full',
      'text-slate-11 transition-[transform,background-color,color,box-shadow] duration-150 ease-out',
      'hover:bg-slate-4 hover:text-slate-12',
      'active:scale-95',
      'focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-blue-7 focus-visible:ring-offset-2 focus-visible:ring-offset-slate-1',
      'dark:focus-visible:ring-offset-slate-2',
    ],
  },
  variants: {
    backdropVariant: {
      opaque: { backdrop: 'bg-black/50 dark:bg-black/60' },
      blur: { backdrop: 'bg-black/50 backdrop-blur-md dark:bg-black/60' },
      transparent: { backdrop: 'bg-transparent' },
    },
    size: {
      xs: { dialog: 'max-w-xs' },
      sm: { dialog: 'max-w-sm' },
      md: { dialog: 'max-w-md' },
      lg: { dialog: 'max-w-lg' },
      xl: { dialog: 'max-w-xl' },
      '2xl': { dialog: 'max-w-2xl' },
      '3xl': { dialog: 'max-w-3xl' },
      '4xl': { dialog: 'max-w-4xl' },
      '5xl': { dialog: 'max-w-5xl' },
      cover: { dialog: 'h-full min-h-full w-full' },
      full: {
        container: 'p-0 sm:p-0',
        dialog: 'h-full min-h-full w-full rounded-none shadow-none ring-0',
      },
    },
    placement: {
      auto: {
        container:
          'justify-end data-[starting-style]:translate-y-1 data-[ending-style]:translate-y-1 sm:justify-center sm:data-[starting-style]:translate-y-0 sm:data-[ending-style]:translate-y-0',
        dialog: 'mt-auto sm:my-auto',
      },
      center: {
        container: 'justify-center',
        dialog: 'my-auto',
      },
      bottom: {
        container:
          'justify-end data-[starting-style]:translate-y-1 data-[ending-style]:translate-y-1',
        dialog: 'mt-auto',
      },
      top: {
        container:
          'justify-start data-[starting-style]:-translate-y-1 data-[ending-style]:-translate-y-1',
        dialog: 'mt-0',
      },
    },
    scroll: {
      inside: {
        dialog: 'max-h-[calc(100dvh-2rem)] overflow-hidden sm:max-h-[calc(100dvh-5rem)]',
        body: 'overflow-y-auto p-4',
      },
      outside: {
        container: 'overflow-y-auto pointer-events-auto',
        dialog: 'h-auto min-h-0 shrink-0',
        body: 'overflow-y-visible',
      },
    },
    /**
     * Aparência do header. `brand` (default) pinta o cabeçalho com a cor
     * primária e o texto em branco — coerente com o resto da UI. `subtle`
     * mantém o cinza neutro de antes (use só quando o brand for inadequado,
     * ex.: modal sobre fundo já colorido).
     */
    tone: {
      brand: {
        header: 'bg-brand-9 border-brand-10 text-white',
        heading: 'text-white',
        description: 'text-white/85',
        closeTrigger:
          'text-white hover:bg-white/15 hover:text-white focus-visible:ring-white/50',
      },
      subtle: {},
    },
  },
  defaultVariants: {
    backdropVariant: 'opaque',
    size: 'md',
    placement: 'auto',
    scroll: 'inside',
    tone: 'brand',
  },
});

export type ModalVariants = VariantProps<typeof modalVariants>;

type ModalPlacement = 'auto' | 'top' | 'center' | 'bottom';
type ModalScroll = 'inside' | 'outside';
type ModalSize =
  | 'xs'
  | 'sm'
  | 'md'
  | 'lg'
  | 'xl'
  | '2xl'
  | '3xl'
  | '4xl'
  | '5xl'
  | 'cover'
  | 'full';
type ModalTone = 'brand' | 'subtle';

/* -------------------------------------------------------------------------------------------------
 * useDraggable — comportamento de arrastar pelo header
 *
 * - Aplica um translate(x, y) ao Modal.Dialog enquanto o usuário arrasta o header.
 * - Ignora cliques em elementos interativos dentro do header (botões, inputs, links).
 * - Desabilita em mobile (< 640px) — não faz sentido arrastar um bottom-sheet.
 * - Duplo clique no header reseta a posição (centro original).
 * - O estado se perde naturalmente quando o modal desmonta — abrir de novo recentra.
 * -----------------------------------------------------------------------------------------------*/

const DRAG_MIN_WIDTH = 640; // breakpoint sm do Tailwind
const DRAG_IGNORE_SELECTOR = 'button, a, input, textarea, select, [role="button"]';

interface DragHandlers {
  onPointerDown: (event: ReactPointerEvent<HTMLElement>) => void;
  onPointerMove: (event: ReactPointerEvent<HTMLElement>) => void;
  onPointerUp: (event: ReactPointerEvent<HTMLElement>) => void;
  onPointerCancel: (event: ReactPointerEvent<HTMLElement>) => void;
  onDoubleClick: (event: ReactMouseEvent<HTMLElement>) => void;
}

interface UseDraggableReturn {
  offset: { x: number; y: number };
  isDragging: boolean;
  handlers: DragHandlers;
  enabled: boolean;
}

const NOOP_HANDLERS: DragHandlers = {
  onPointerDown: () => undefined,
  onPointerMove: () => undefined,
  onPointerUp: () => undefined,
  onPointerCancel: () => undefined,
  onDoubleClick: () => undefined,
};

function useDraggable(enabled: boolean): UseDraggableReturn {
  const [offset, setOffset] = useState({ x: 0, y: 0 });
  const [isDragging, setIsDragging] = useState(false);
  const dragStateRef = useRef<{
    startX: number;
    startY: number;
    baseX: number;
    baseY: number;
  } | null>(null);

  // Garante que recarregar (ex.: prop muda para enabled=false) limpa estado.
  useEffect(() => {
    if (!enabled) {
      setOffset({ x: 0, y: 0 });
      setIsDragging(false);
      dragStateRef.current = null;
    }
  }, [enabled]);

  const onPointerDown = useCallback(
    (event: ReactPointerEvent<HTMLElement>) => {
      if (!enabled) return;
      if (typeof window !== 'undefined' && window.innerWidth < DRAG_MIN_WIDTH) return;
      // Botão direito ou middle: ignora
      if (event.button !== 0) return;
      const target = event.target as HTMLElement | null;
      if (target?.closest(DRAG_IGNORE_SELECTOR)) return;

      event.preventDefault();
      dragStateRef.current = {
        startX: event.clientX,
        startY: event.clientY,
        baseX: offset.x,
        baseY: offset.y,
      };
      setIsDragging(true);
      try {
        event.currentTarget.setPointerCapture(event.pointerId);
      } catch {
        // ignora ambientes sem suporte
      }
    },
    [enabled, offset.x, offset.y],
  );

  const onPointerMove = useCallback((event: ReactPointerEvent<HTMLElement>) => {
    const state = dragStateRef.current;
    if (!state) return;
    setOffset({
      x: state.baseX + (event.clientX - state.startX),
      y: state.baseY + (event.clientY - state.startY),
    });
  }, []);

  const finishDrag = useCallback((event: ReactPointerEvent<HTMLElement>) => {
    if (!dragStateRef.current) return;
    dragStateRef.current = null;
    setIsDragging(false);
    try {
      event.currentTarget.releasePointerCapture?.(event.pointerId);
    } catch {
      // ignora
    }
  }, []);

  const onDoubleClick = useCallback(
    (event: ReactMouseEvent<HTMLElement>) => {
      if (!enabled) return;
      const target = event.target as HTMLElement | null;
      if (target?.closest(DRAG_IGNORE_SELECTOR)) return;
      setOffset({ x: 0, y: 0 });
    },
    [enabled],
  );

  const handlers = useMemo<DragHandlers>(
    () =>
      enabled
        ? {
            onPointerDown,
            onPointerMove,
            onPointerUp: finishDrag,
            onPointerCancel: finishDrag,
            onDoubleClick,
          }
        : NOOP_HANDLERS,
    [enabled, onPointerDown, onPointerMove, finishDrag, onDoubleClick],
  );

  return { offset, isDragging, handlers, enabled };
}

type ModalRootContextValue = {
  open?: boolean;
  defaultOpen?: boolean;
  onOpenChange?: (open: boolean, eventDetails: DialogPrimitive.Root.ChangeEventDetails) => void;
  onOpenChangeComplete?: (open: boolean) => void;
};

const ModalRootContext = createContext<ModalRootContextValue>({});

type ModalRuntimeContextValue = {
  close: () => void;
};

const ModalRuntimeContext = createContext<ModalRuntimeContextValue>({
  close: () => undefined,
});

type ModalContextValue = {
  slots: ReturnType<typeof modalVariants>;
  placement: ModalPlacement;
  scroll: ModalScroll;
  tone: ModalTone;
  drag: UseDraggableReturn;
};

const DEFAULT_MODAL_SLOTS = modalVariants();
const DEFAULT_DRAG: UseDraggableReturn = {
  offset: { x: 0, y: 0 },
  isDragging: false,
  handlers: NOOP_HANDLERS,
  enabled: false,
};

const ModalContext = createContext<ModalContextValue>({
  slots: DEFAULT_MODAL_SLOTS,
  placement: 'auto',
  scroll: 'inside',
  tone: 'brand',
  drag: DEFAULT_DRAG,
});

export interface UseOverlayStateProps {
  isOpen?: boolean;
  defaultOpen?: boolean;
  onOpenChange?: (isOpen: boolean) => void;
}

export interface UseOverlayStateReturn {
  readonly isOpen: boolean;
  setOpen(isOpen: boolean): void;
  open(): void;
  close(): void;
  toggle(): void;
}

export function useOverlayState(props: UseOverlayStateProps = {}): UseOverlayStateReturn {
  const { defaultOpen = false, isOpen: controlledIsOpen, onOpenChange } = props;
  const [uncontrolledIsOpen, setUncontrolledIsOpen] = useState<boolean>(defaultOpen);

  const isControlled = controlledIsOpen !== undefined;
  const isOpen = isControlled ? controlledIsOpen : uncontrolledIsOpen;

  const setOpen = useCallback(
    (nextIsOpen: boolean) => {
      onOpenChange?.(nextIsOpen);
      if (!isControlled) {
        setUncontrolledIsOpen(nextIsOpen);
      }
    },
    [isControlled, onOpenChange],
  );

  const open = useCallback(() => setOpen(true), [setOpen]);
  const close = useCallback(() => setOpen(false), [setOpen]);
  const toggle = useCallback(() => setOpen(!isOpen), [setOpen, isOpen]);

  return { close, isOpen, open, setOpen, toggle };
}

export interface ModalRootProps {
  children?: ReactNode;
  open?: boolean;
  defaultOpen?: boolean;
  onOpenChange?: (open: boolean, eventDetails: DialogPrimitive.Root.ChangeEventDetails) => void;
  onOpenChangeComplete?: (open: boolean) => void;
}

function ModalRoot({
  children,
  defaultOpen,
  onOpenChange,
  onOpenChangeComplete,
  open,
}: ModalRootProps) {
  const rootContext = useMemo<ModalRootContextValue>(
    () => ({
      defaultOpen,
      onOpenChange,
      onOpenChangeComplete,
      open,
    }),
    [defaultOpen, onOpenChange, onOpenChangeComplete, open],
  );

  return <ModalRootContext value={rootContext}>{children}</ModalRootContext>;
}

export interface ModalTriggerProps extends DialogPrimitive.Trigger.Props {
  className?: string;
}

function ModalTrigger({ className, children, ...props }: ModalTriggerProps) {
  const { slots } = useContext(ModalContext);

  return (
    <DialogPrimitive.Trigger
      data-slot="modal-trigger"
      className={cn(slots.trigger(), className)}
      {...props}
    >
      {children}
    </DialogPrimitive.Trigger>
  );
}

export interface ModalBackdropProps {
  children?: ReactNode;
  className?: string;
  variant?: 'opaque' | 'blur' | 'transparent';
  isDismissable?: boolean;
  isKeyboardDismissDisabled?: boolean;
  isOpen?: boolean;
  defaultOpen?: boolean;
  onOpenChange?: (open: boolean, eventDetails: DialogPrimitive.Root.ChangeEventDetails) => void;
  container?: DialogPrimitive.Portal.Props['container'];
  keepMounted?: DialogPrimitive.Portal.Props['keepMounted'];
  forceRender?: DialogPrimitive.Backdrop.Props['forceRender'];
}

function ModalBackdrop({
  children,
  className,
  container,
  defaultOpen: defaultOpenProp,
  forceRender,
  isDismissable = true,
  isKeyboardDismissDisabled = false,
  isOpen: isOpenProp,
  keepMounted,
  onOpenChange: onOpenChangeProp,
  variant = 'opaque',
}: ModalBackdropProps) {
  const rootContext = useContext(ModalRootContext);
  const controlledOpen = isOpenProp ?? rootContext.open;
  const defaultOpen = defaultOpenProp ?? rootContext.defaultOpen ?? false;
  const [uncontrolledOpen, setUncontrolledOpen] = useState(defaultOpen);
  const isControlled = controlledOpen !== undefined;
  const open = controlledOpen ?? uncontrolledOpen;
  const actionsRef = useRef<DialogPrimitive.Root.Actions | null>(null);

  const slots = useMemo(() => modalVariants({ backdropVariant: variant }), [variant]);

  const close = useCallback(() => {
    actionsRef.current?.close();
  }, []);

  const handleOpenChange = useCallback(
    (nextOpen: boolean, eventDetails: DialogPrimitive.Root.ChangeEventDetails) => {
      const reason = eventDetails.reason;

      if (!nextOpen && !isDismissable && reason === 'outside-press') {
        return;
      }

      if (!nextOpen && isKeyboardDismissDisabled && reason === 'escape-key') {
        return;
      }

      if (!isControlled) {
        setUncontrolledOpen(nextOpen);
      }

      (onOpenChangeProp ?? rootContext.onOpenChange)?.(nextOpen, eventDetails);
    },
    [
      isControlled,
      isDismissable,
      isKeyboardDismissDisabled,
      onOpenChangeProp,
      rootContext.onOpenChange,
    ],
  );

  return (
    <ModalRuntimeContext value={{ close }}>
      <DialogPrimitive.Root
        actionsRef={actionsRef as RefObject<DialogPrimitive.Root.Actions>}
        disablePointerDismissal={!isDismissable}
        onOpenChange={handleOpenChange}
        onOpenChangeComplete={rootContext.onOpenChangeComplete}
        open={open}
      >
        <DialogPrimitive.Portal container={container} keepMounted={keepMounted}>
          <DialogPrimitive.Backdrop
            data-slot="modal-backdrop"
            forceRender={forceRender}
            className={cn(slots.backdrop(), className)}
          />
          {children}
        </DialogPrimitive.Portal>
      </DialogPrimitive.Root>
    </ModalRuntimeContext>
  );
}

export interface ModalContainerProps extends Omit<DialogPrimitive.Viewport.Props, 'children'> {
  children?: ReactNode;
  className?: string;
  placement?: ModalPlacement;
  scroll?: ModalScroll;
  size?: ModalSize;
  /** Aparência do header. @default 'brand' */
  tone?: ModalTone;
  /** Permite arrastar o modal pelo header (desabilitado em mobile). @default true */
  draggable?: boolean;
}

function ModalContainer({
  children,
  className,
  placement = 'auto',
  scroll = 'inside',
  size = 'md',
  tone = 'brand',
  draggable = true,
  ...props
}: ModalContainerProps) {
  const slots = useMemo(
    () => modalVariants({ placement, scroll, size, tone }),
    [placement, scroll, size, tone],
  );

  const drag = useDraggable(draggable);

  const contextValue = useMemo<ModalContextValue>(
    () => ({ placement, scroll, slots, tone, drag }),
    [placement, scroll, slots, tone, drag],
  );

  return (
    <ModalContext value={contextValue}>
      <DialogPrimitive.Viewport
        data-slot="modal-container"
        data-placement={placement}
        className={cn(slots.container(), className)}
        {...props}
      >
        {children}
      </DialogPrimitive.Viewport>
    </ModalContext>
  );
}

export interface ModalDialogProps extends Omit<DialogPrimitive.Popup.Props, 'children'> {
  children?: ReactNode | ((renderProps: { close: () => void }) => ReactNode);
  className?: string;
}

function ModalDialog({ children, className, style, ...props }: ModalDialogProps) {
  const { close } = useContext(ModalRuntimeContext);
  const { placement, slots, drag } = useContext(ModalContext);

  // Aplica o transform do drag em cima de qualquer style já vindo do consumidor.
  // Quando há offset, suprime a transition baseline (que afetaria a sensação
  // de "siga o cursor"). Re-habilita só no idle.
  const dragStyle: CSSProperties =
    drag.enabled && (drag.offset.x !== 0 || drag.offset.y !== 0 || drag.isDragging)
      ? {
          transform: `translate3d(${drag.offset.x}px, ${drag.offset.y}px, 0)`,
          transition: drag.isDragging ? 'none' : undefined,
          willChange: 'transform',
        }
      : {};

  return (
    <DialogPrimitive.Popup
      data-slot="modal-dialog"
      data-placement={placement}
      data-dragging={drag.isDragging || undefined}
      style={{ ...dragStyle, ...style }}
      className={cn(slots.dialog(), className)}
      {...props}
    >
      {typeof children === 'function' ? children({ close }) : children}
    </DialogPrimitive.Popup>
  );
}

export interface ModalHeaderProps extends ComponentPropsWithRef<'div'> {}

function ModalHeader({ className, children, ...props }: ModalHeaderProps) {
  const { slots, drag } = useContext(ModalContext);

  return (
    <div
      data-slot="modal-header"
      data-draggable={drag.enabled || undefined}
      className={cn(
        slots.header(),
        // touch-none evita scroll vertical interferir no drag, e select-none
        // impede que arrastar selecione o texto do título.
        drag.enabled && 'sm:cursor-move sm:touch-none sm:select-none',
        className,
      )}
      {...drag.handlers}
      {...props}
    >
      {children}
    </div>
  );
}

export interface ModalHeadingProps extends DialogPrimitive.Title.Props {
  className?: string;
}

function ModalHeading({ className, children, ...props }: ModalHeadingProps) {
  const { slots } = useContext(ModalContext);

  return (
    <DialogPrimitive.Title
      data-slot="modal-heading"
      className={cn(slots.heading(), className)}
      {...props}
    >
      {children}
    </DialogPrimitive.Title>
  );
}

export interface ModalDescriptionProps extends DialogPrimitive.Description.Props {
  className?: string;
}

/**
 * Subtítulo/descrição embaixo do `Modal.Heading`. Usa a slot `description` do
 * `modalVariants`, então a cor se ajusta automaticamente ao `tone` do modal
 * (branco no header brand, slate-11 no subtle).
 */
function ModalDescription({ className, children, ...props }: ModalDescriptionProps) {
  const { slots } = useContext(ModalContext);

  return (
    <DialogPrimitive.Description
      data-slot="modal-description"
      className={cn(slots.description(), className)}
      {...props}
    >
      {children}
    </DialogPrimitive.Description>
  );
}

export interface ModalIconProps extends ComponentPropsWithRef<'div'> {}

function ModalIcon({ className, children, ...props }: ModalIconProps) {
  const { slots } = useContext(ModalContext);

  return (
    <div data-slot="modal-icon" className={cn(slots.icon(), className)} {...props}>
      {children}
    </div>
  );
}

export interface ModalBodyProps extends ComponentPropsWithRef<'div'> {}

function ModalBody({ className, children, ...props }: ModalBodyProps) {
  const { slots } = useContext(ModalContext);

  return (
    <div data-slot="modal-body" className={cn(slots.body(), className)} {...props}>
      {children}
    </div>
  );
}

export interface ModalFooterProps extends ComponentPropsWithRef<'div'> {}

function ModalFooter({ className, children, ...props }: ModalFooterProps) {
  const { slots } = useContext(ModalContext);

  return (
    <div data-slot="modal-footer" className={cn(slots.footer(), className)} {...props}>
      {children}
    </div>
  );
}

export interface ModalCloseTriggerProps extends DialogPrimitive.Close.Props {
  className?: string;
}

function ModalCloseTrigger({ className, children, ...props }: ModalCloseTriggerProps) {
  const { slots } = useContext(ModalContext);

  return (
    <DialogPrimitive.Close
      data-slot="modal-close-trigger"
      className={cn(slots.closeTrigger(), className)}
      {...props}
    >
      {children ?? (
        <>
          <XClose className="size-4" strokeWidth={2} />
          <span className="sr-only">Fechar</span>
        </>
      )}
    </DialogPrimitive.Close>
  );
}

export interface ModalCloseProps extends DialogPrimitive.Close.Props {
  className?: string;
}

function ModalClose({ children, className, ...props }: ModalCloseProps) {
  return (
    <DialogPrimitive.Close data-slot="modal-close" className={className} {...props}>
      {children}
    </DialogPrimitive.Close>
  );
}

const Modal = Object.assign(ModalRoot, {
  Trigger: ModalTrigger,
  Backdrop: ModalBackdrop,
  Container: ModalContainer,
  Viewport: ModalContainer,
  Dialog: ModalDialog,
  Popup: ModalDialog,
  Header: ModalHeader,
  Icon: ModalIcon,
  Heading: ModalHeading,
  Title: ModalHeading,
  Description: ModalDescription,
  Body: ModalBody,
  Footer: ModalFooter,
  CloseTrigger: ModalCloseTrigger,
  Close: ModalClose,
});

export {
  Modal,
  ModalRoot,
  ModalTrigger,
  ModalBackdrop,
  ModalContainer,
  ModalDialog,
  ModalHeader,
  ModalIcon,
  ModalHeading,
  ModalDescription,
  ModalBody,
  ModalFooter,
  ModalCloseTrigger,
  ModalClose,
};
```

---

## 📂 `popover.tsx`

Caminho original no repositório: `/packages/ui/src/components/popover.tsx`

```tsx
import { Popover as PopoverPrimitive } from '@base-ui/react/popover';
import type * as React from 'react';

import { cn } from '@manager/lib';

function Popover({ ...props }: PopoverPrimitive.Root.Props) {
  return <PopoverPrimitive.Root data-slot="popover" {...props} />;
}

function PopoverTrigger({ ...props }: PopoverPrimitive.Trigger.Props) {
  return <PopoverPrimitive.Trigger data-slot="popover-trigger" {...props} />;
}

function PopoverContent({
  className,
  align = 'center',
  alignOffset = 0,
  side = 'bottom',
  sideOffset = 4,
  ...props
}: PopoverPrimitive.Popup.Props &
  Pick<PopoverPrimitive.Positioner.Props, 'align' | 'alignOffset' | 'side' | 'sideOffset'>) {
  return (
    <PopoverPrimitive.Portal>
      <PopoverPrimitive.Positioner
        align={align}
        alignOffset={alignOffset}
        side={side}
        sideOffset={sideOffset}
        className="isolate z-50"
      >
        <PopoverPrimitive.Popup
          data-slot="popover-content"
          className={cn(
            'bg-white text-zinc-950 data-open:animate-in data-closed:animate-out data-closed:fade-out-0 data-open:fade-in-0 data-closed:zoom-out-95 data-open:zoom-in-95 data-[side=bottom]:slide-in-from-top-2 data-[side=left]:slide-in-from-right-2 data-[side=right]:slide-in-from-left-2 data-[side=top]:slide-in-from-bottom-2 ring-zinc-950/10 flex flex-col gap-4 rounded-md p-4 text-sm shadow-md ring-1 duration-100 z-50 w-72 origin-(--transform-origin) outline-hidden dark:bg-zinc-950 dark:text-zinc-50 dark:ring-zinc-50/10',
            className,
          )}
          {...props}
        />
      </PopoverPrimitive.Positioner>
    </PopoverPrimitive.Portal>
  );
}

function PopoverHeader({ className, ...props }: React.ComponentProps<'div'>) {
  return (
    <div
      data-slot="popover-header"
      className={cn('flex flex-col gap-1 text-sm', className)}
      {...props}
    />
  );
}

function PopoverTitle({ className, ...props }: PopoverPrimitive.Title.Props) {
  return (
    <PopoverPrimitive.Title
      data-slot="popover-title"
      className={cn('font-medium', className)}
      {...props}
    />
  );
}

function PopoverDescription({ className, ...props }: PopoverPrimitive.Description.Props) {
  return (
    <PopoverPrimitive.Description
      data-slot="popover-description"
      className={cn('text-zinc-500 dark:text-zinc-400', className)}
      {...props}
    />
  );
}

export { Popover, PopoverContent, PopoverDescription, PopoverHeader, PopoverTitle, PopoverTrigger };
```

---

## 📂 `hover-card.tsx`

Caminho original no repositório: `/packages/ui/src/components/hover-card.tsx`

```tsx
'use client';

import { PreviewCard as PreviewCardPrimitive } from '@base-ui/react/preview-card';

import { cn } from '@manager/lib';

function HoverCard({ ...props }: PreviewCardPrimitive.Root.Props) {
  return <PreviewCardPrimitive.Root data-slot="hover-card" {...props} />;
}

function HoverCardTrigger({ ...props }: PreviewCardPrimitive.Trigger.Props) {
  return <PreviewCardPrimitive.Trigger data-slot="hover-card-trigger" {...props} />;
}

function HoverCardContent({
  className,
  side = 'bottom',
  sideOffset = 4,
  align = 'center',
  alignOffset = 4,
  ...props
}: PreviewCardPrimitive.Popup.Props &
  Pick<PreviewCardPrimitive.Positioner.Props, 'align' | 'alignOffset' | 'side' | 'sideOffset'>) {
  return (
    <PreviewCardPrimitive.Portal data-slot="hover-card-portal">
      <PreviewCardPrimitive.Positioner
        align={align}
        alignOffset={alignOffset}
        side={side}
        sideOffset={sideOffset}
        className="isolate z-50"
      >
        <PreviewCardPrimitive.Popup
          data-slot="hover-card-content"
          className={cn(
            'data-open:animate-in data-closed:animate-out data-closed:fade-out-0 data-open:fade-in-0 data-closed:zoom-out-95 data-open:zoom-in-95 data-[side=bottom]:slide-in-from-top-2 data-[side=left]:slide-in-from-right-2 data-[side=right]:slide-in-from-left-2 data-[side=top]:slide-in-from-bottom-2 ring-zinc-950/10 bg-white text-zinc-950 w-64 rounded-lg p-4 text-sm shadow-md ring-1 duration-100 z-50 origin-(--transform-origin) outline-hidden dark:ring-zinc-50/10 dark:bg-zinc-950 dark:text-zinc-50',
            className,
          )}
          {...props}
        />
      </PreviewCardPrimitive.Positioner>
    </PreviewCardPrimitive.Portal>
  );
}

export { HoverCard, HoverCardTrigger, HoverCardContent };
```

---

## 📂 `sheet.tsx`

Caminho original no repositório: `/packages/ui/src/components/sheet.tsx`

```tsx
'use client';

import { Dialog as SheetPrimitive } from '@base-ui/react/dialog';
import type * as React from 'react';

import { cn } from '@manager/lib';
import { XClose } from '@untitled-ui/icons-react';
import { Button } from './button';

function Sheet({ ...props }: SheetPrimitive.Root.Props) {
  return <SheetPrimitive.Root data-slot="sheet" {...props} />;
}

function SheetTrigger({ ...props }: SheetPrimitive.Trigger.Props) {
  return <SheetPrimitive.Trigger data-slot="sheet-trigger" {...props} />;
}

function SheetClose({ ...props }: SheetPrimitive.Close.Props) {
  return <SheetPrimitive.Close data-slot="sheet-close" {...props} />;
}

function SheetPortal({ ...props }: SheetPrimitive.Portal.Props) {
  return <SheetPrimitive.Portal data-slot="sheet-portal" {...props} />;
}

function SheetOverlay({ className, ...props }: SheetPrimitive.Backdrop.Props) {
  return (
    <SheetPrimitive.Backdrop
      data-slot="sheet-overlay"
      className={cn(
        'data-open:animate-in data-closed:animate-out data-closed:fade-out-0 data-open:fade-in-0 bg-black/10 duration-100 data-ending-style:opacity-0 data-starting-style:opacity-0 supports-backdrop-filter:backdrop-blur-xs fixed inset-0 z-50',
        className,
      )}
      {...props}
    />
  );
}

function SheetContent({
  className,
  children,
  side = 'right',
  showCloseButton = true,
  ...props
}: SheetPrimitive.Popup.Props & {
  side?: 'top' | 'right' | 'bottom' | 'left';
  showCloseButton?: boolean;
}) {
  return (
    <SheetPortal>
      <SheetOverlay />
      <SheetPrimitive.Popup
        data-slot="sheet-content"
        data-side={side}
        className={cn(
          'bg-white data-open:animate-in data-closed:animate-out data-[side=right]:data-closed:slide-out-to-right-10 data-[side=right]:data-open:slide-in-from-right-10 data-[side=left]:data-closed:slide-out-to-left-10 data-[side=left]:data-open:slide-in-from-left-10 data-[side=top]:data-closed:slide-out-to-top-10 data-[side=top]:data-open:slide-in-from-top-10 data-closed:fade-out-0 data-open:fade-in-0 data-[side=bottom]:data-closed:slide-out-to-bottom-10 data-[side=bottom]:data-open:slide-in-from-bottom-10 fixed z-50 flex flex-col gap-4 bg-clip-padding text-sm shadow-lg transition duration-200 ease-in-out data-[side=bottom]:inset-x-0 data-[side=bottom]:bottom-0 data-[side=bottom]:h-auto data-[side=bottom]:border-t data-[side=left]:inset-y-0 data-[side=left]:left-0 data-[side=left]:h-full data-[side=left]:w-3/4 data-[side=left]:border-r data-[side=right]:inset-y-0 data-[side=right]:right-0 data-[side=right]:h-full data-[side=right]:w-3/4 data-[side=right]:border-l data-[side=top]:inset-x-0 data-[side=top]:top-0 data-[side=top]:h-auto data-[side=top]:border-b data-[side=left]:sm:max-w-sm data-[side=right]:sm:max-w-sm dark:bg-zinc-950',
          className,
        )}
        {...props}
      >
        {children}
        {showCloseButton && (
          <SheetPrimitive.Close
            data-slot="sheet-close"
            render={<Button variant="ghost" className="absolute top-4 right-4" size="icon-sm" />}
          >
            <XClose />
            <span className="sr-only">Close</span>
          </SheetPrimitive.Close>
        )}
      </SheetPrimitive.Popup>
    </SheetPortal>
  );
}

function SheetHeader({ className, ...props }: React.ComponentProps<'div'>) {
  return (
    <div
      data-slot="sheet-header"
      className={cn('gap-1.5 p-4 flex flex-col', className)}
      {...props}
    />
  );
}

function SheetFooter({ className, ...props }: React.ComponentProps<'div'>) {
  return (
    <div
      data-slot="sheet-footer"
      className={cn('gap-2 p-4 mt-auto flex flex-col', className)}
      {...props}
    />
  );
}

function SheetTitle({ className, ...props }: SheetPrimitive.Title.Props) {
  return (
    <SheetPrimitive.Title
      data-slot="sheet-title"
      className={cn('text-zinc-950 font-medium dark:text-zinc-50', className)}
      {...props}
    />
  );
}

function SheetDescription({ className, ...props }: SheetPrimitive.Description.Props) {
  return (
    <SheetPrimitive.Description
      data-slot="sheet-description"
      className={cn('text-zinc-500 text-sm dark:text-zinc-400', className)}
      {...props}
    />
  );
}

export {
  Sheet,
  SheetTrigger,
  SheetClose,
  SheetContent,
  SheetHeader,
  SheetFooter,
  SheetTitle,
  SheetDescription,
};
```

---

## 📂 `tooltip.tsx`

Caminho original no repositório: `/packages/ui/src/components/tooltip.tsx`

```tsx
import { Tooltip as TooltipPrimitive } from '@base-ui/react/tooltip';

import { cn } from '@manager/lib';

function TooltipProvider({ delay = 0, ...props }: TooltipPrimitive.Provider.Props) {
  return <TooltipPrimitive.Provider data-slot="tooltip-provider" delay={delay} {...props} />;
}

function Tooltip({ ...props }: TooltipPrimitive.Root.Props) {
  return (
    <TooltipProvider>
      <TooltipPrimitive.Root data-slot="tooltip" {...props} />
    </TooltipProvider>
  );
}

function TooltipTrigger({ ...props }: TooltipPrimitive.Trigger.Props) {
  return <TooltipPrimitive.Trigger data-slot="tooltip-trigger" {...props} />;
}

function TooltipContent({
  className,
  side = 'top',
  sideOffset = 4,
  align = 'center',
  alignOffset = 0,
  children,
  ...props
}: TooltipPrimitive.Popup.Props &
  Pick<TooltipPrimitive.Positioner.Props, 'align' | 'alignOffset' | 'side' | 'sideOffset'>) {
  return (
    <TooltipPrimitive.Portal>
      <TooltipPrimitive.Positioner
        align={align}
        alignOffset={alignOffset}
        side={side}
        sideOffset={sideOffset}
        className="isolate z-50"
      >
        <TooltipPrimitive.Popup
          data-slot="tooltip-content"
          className={cn(
            'data-open:animate-in data-open:fade-in-0 data-open:zoom-in-95 data-[state=delayed-open]:animate-in data-[state=delayed-open]:fade-in-0 data-[state=delayed-open]:zoom-in-95 data-closed:animate-out data-closed:fade-out-0 data-closed:zoom-out-95 data-[side=bottom]:slide-in-from-top-2 data-[side=left]:slide-in-from-right-2 data-[side=right]:slide-in-from-left-2 data-[side=top]:slide-in-from-bottom-2 rounded-md px-3 py-1.5 text-xs bg-zinc-950 text-white z-50 w-fit max-w-xs origin-(--transform-origin) dark:bg-zinc-50 dark:text-zinc-950',
            className,
          )}
          {...props}
        >
          {children}
          <TooltipPrimitive.Arrow className="size-2.5 translate-y-[calc(-50%_-_2px)] rotate-45 rounded-[2px] bg-zinc-950 fill-foreground z-50 data-[side=bottom]:top-1 data-[side=left]:top-1/2! data-[side=left]:-right-1 data-[side=left]:-translate-y-1/2 data-[side=right]:top-1/2! data-[side=right]:-left-1 data-[side=right]:-translate-y-1/2 data-[side=top]:-bottom-2.5 dark:bg-zinc-50" />
        </TooltipPrimitive.Popup>
      </TooltipPrimitive.Positioner>
    </TooltipPrimitive.Portal>
  );
}

export { Tooltip, TooltipTrigger, TooltipContent, TooltipProvider };
```

---

## 📂 `toast.tsx`

Caminho original no repositório: `/packages/ui/src/components/toast.tsx`

```tsx
'use client';

import { Toast } from '@base-ui/react/toast';
import { cn } from '@manager/lib';
import { XClose } from '@untitled-ui/icons-react';
import {
  CircleAlertIcon,
  CircleCheckIcon,
  InfoIcon,
  LoaderCircleIcon,
  TriangleAlertIcon,
} from 'lucide-react';
import type React from 'react';
import { buttonVariants } from './button';

const TOAST_ICONS = {
  error: CircleAlertIcon,
  info: InfoIcon,
  loading: LoaderCircleIcon,
  success: CircleCheckIcon,
  warning: TriangleAlertIcon,
} as const;

type SwipeDirection = 'up' | 'down' | 'left' | 'right';

function getSwipeDirection(position: ToastPosition): SwipeDirection[] {
  const verticalDirection: SwipeDirection = position.startsWith('top') ? 'up' : 'down';

  if (position.includes('center')) {
    return [verticalDirection];
  }

  if (position.includes('left')) {
    return ['left', verticalDirection];
  }

  return ['right', verticalDirection];
}

function upsertReplayClassName(toast: {
  type?: string;
  updateKey?: number;
}): string | undefined {
  const k = toast.updateKey ?? 0;
  if (k <= 0) return undefined;
  const isEven = k % 2 === 0;
  if (toast.type === 'error') {
    return isEven ? 'animate-toast-error-even' : 'animate-toast-error-odd';
  }
  return isEven ? 'animate-toast-success-even' : 'animate-toast-success-odd';
}

function Toasts({
  position,
  portalProps,
}: {
  position: ToastPosition;
  portalProps?: React.ComponentProps<typeof Toast.Portal>;
}): React.ReactElement {
  const { toasts } = Toast.useToastManager();
  const swipeDirection = getSwipeDirection(position);

  return (
    <Toast.Portal data-slot="toast-portal" {...portalProps}>
      <Toast.Viewport
        className={cn(
          'fixed z-60 mx-auto flex w-[calc(100%-var(--toast-inset)*2)] max-w-90 [--toast-inset:--spacing(4)] sm:[--toast-inset:--spacing(8)]',
          // Vertical positioning
          'data-[position*=top]:top-(--toast-inset)',
          'data-[position*=bottom]:bottom-(--toast-inset)',
          // Horizontal positioning
          'data-[position*=left]:left-(--toast-inset)',
          'data-[position*=right]:right-(--toast-inset)',
          'data-[position*=center]:left-1/2 data-[position*=center]:-translate-x-1/2',
        )}
        data-position={position}
        data-slot="toast-viewport"
      >
        {toasts.map((toast) => {
          const Icon = toast.type ? TOAST_ICONS[toast.type as keyof typeof TOAST_ICONS] : null;

          return (
            <Toast.Root
              key={toast.id}
              className={cn(
                'absolute z-[calc(9999-var(--toast-index))] h-(--toast-calc-height) w-full select-none rounded-lg border border-slate-6 bg-slate-2 text-slate-12 shadow-lg/5 [transition:transform_.5s_cubic-bezier(.22,1,.36,1),opacity_.5s,height_.15s] before:pointer-events-none before:absolute before:inset-0 before:rounded-[calc(var(--radius-lg)-1px)] before:shadow-[0_1px_--theme(--color-black/4%)]',
                // Base positioning using data-position
                'data-[position*=right]:right-0 data-[position*=right]:left-auto',
                'data-[position*=left]:right-auto data-[position*=left]:left-0',
                'data-[position*=center]:right-0 data-[position*=center]:left-0',
                'data-[position*=top]:top-0 data-[position*=top]:bottom-auto data-[position*=top]:origin-[50%_calc(50%-50%*min(var(--toast-index,0),1))]',
                'data-[position*=bottom]:top-auto data-[position*=bottom]:bottom-0 data-[position*=bottom]:origin-[50%_calc(50%+50%*min(var(--toast-index,0),1))]',
                // Gap fill for hover
                'after:absolute after:left-0 after:h-[calc(var(--toast-gap)+1px)] after:w-full',
                'data-[position*=top]:after:top-full',
                'data-[position*=bottom]:after:bottom-full',
                // Define some variables
                '[--toast-calc-height:var(--toast-frontmost-height,var(--toast-height))] [--toast-gap:--spacing(3)] [--toast-peek:--spacing(3)] [--toast-scale:calc(max(0,1-(var(--toast-index)*.1)))] [--toast-shrink:calc(1-var(--toast-scale))]',
                // Define offset-y variable
                'data-[position*=top]:[--toast-calc-offset-y:calc(var(--toast-offset-y)+var(--toast-index)*var(--toast-gap)+var(--toast-swipe-movement-y))]',
                'data-[position*=bottom]:[--toast-calc-offset-y:calc(var(--toast-offset-y)*-1+var(--toast-index)*var(--toast-gap)*-1+var(--toast-swipe-movement-y))]',
                // Default state transform
                'data-[position*=top]:transform-[translateX(var(--toast-swipe-movement-x))_translateY(calc(var(--toast-swipe-movement-y)+(var(--toast-index)*var(--toast-peek))+(var(--toast-shrink)*var(--toast-calc-height))))_scale(var(--toast-scale))]',
                'data-[position*=bottom]:transform-[translateX(var(--toast-swipe-movement-x))_translateY(calc(var(--toast-swipe-movement-y)-(var(--toast-index)*var(--toast-peek))-(var(--toast-shrink)*var(--toast-calc-height))))_scale(var(--toast-scale))]',
                // Limited state
                'data-limited:opacity-0',
                // Expanded state
                'data-expanded:h-(--toast-height)',
                'data-position:data-expanded:transform-[translateX(var(--toast-swipe-movement-x))_translateY(var(--toast-calc-offset-y))]',
                // Starting and ending animations
                'data-[position*=top]:data-starting-style:transform-[translateY(calc(-100%-var(--toast-inset)))]',
                'data-[position*=bottom]:data-starting-style:transform-[translateY(calc(100%+var(--toast-inset)))]',
                'data-ending-style:opacity-0',
                // Ending animations (direction-aware)
                'data-ending-style:not-data-limited:not-data-swipe-direction:transform-[translateY(calc(100%+var(--toast-inset)))]',
                'data-ending-style:data-[swipe-direction=left]:transform-[translateX(calc(var(--toast-swipe-movement-x)-100%-var(--toast-inset)))_translateY(var(--toast-calc-offset-y))]',
                'data-ending-style:data-[swipe-direction=right]:transform-[translateX(calc(var(--toast-swipe-movement-x)+100%+var(--toast-inset)))_translateY(var(--toast-calc-offset-y))]',
                'data-ending-style:data-[swipe-direction=up]:transform-[translateY(calc(var(--toast-swipe-movement-y)-100%-var(--toast-inset)))]',
                'data-ending-style:data-[swipe-direction=down]:transform-[translateY(calc(var(--toast-swipe-movement-y)+100%+var(--toast-inset)))]',
                // Ending animations (expanded)
                'data-expanded:data-ending-style:data-[swipe-direction=left]:transform-[translateX(calc(var(--toast-swipe-movement-x)-100%-var(--toast-inset)))_translateY(var(--toast-calc-offset-y))]',
                'data-expanded:data-ending-style:data-[swipe-direction=right]:transform-[translateX(calc(var(--toast-swipe-movement-x)+100%+var(--toast-inset)))_translateY(var(--toast-calc-offset-y))]',
                'data-expanded:data-ending-style:data-[swipe-direction=up]:transform-[translateY(calc(var(--toast-swipe-movement-y)-100%-var(--toast-inset)))]',
                'data-expanded:data-ending-style:data-[swipe-direction=down]:transform-[translateY(calc(var(--toast-swipe-movement-y)+100%+var(--toast-inset)))]',
                upsertReplayClassName(toast),
              )}
              data-position={position}
              swipeDirection={swipeDirection}
              toast={toast}
            >
              <Toast.Close
                aria-label="Fechar"
                className="pointer-events-auto absolute right-1.5 top-1.5 z-10 flex size-5 items-center justify-center rounded-full bg-slate-4 text-slate-11 transition-colors hover:bg-slate-5 hover:text-slate-12 focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-slate-7"
              >
                <XClose className="size-3" />
              </Toast.Close>
              <Toast.Content className="pointer-events-auto flex items-center justify-between gap-1.5 overflow-hidden px-3.5 py-3 pr-7 text-sm transition-opacity duration-250 data-behind:not-data-expanded:pointer-events-none data-behind:opacity-0 data-expanded:opacity-100">
                <div className="flex gap-2">
                  {Icon && (
                    <div
                      className="[&>svg]:h-lh [&>svg]:w-4 [&_svg]:pointer-events-none [&_svg]:shrink-0"
                      data-slot="toast-icon"
                    >
                      <Icon className="in-data-[type=loading]:animate-spin in-data-[type=error]:text-red-9 in-data-[type=info]:text-blue-9 in-data-[type=success]:text-green-9 in-data-[type=warning]:text-amber-9 in-data-[type=loading]:opacity-80" />
                    </div>
                  )}

                  <div className="flex flex-col gap-0.5">
                    <Toast.Title className="font-medium" data-slot="toast-title" />
                    <Toast.Description
                      className="text-slate-11"
                      data-slot="toast-description"
                    />
                  </div>
                </div>
                {toast.actionProps && (
                  <Toast.Action className={buttonVariants({ size: 'xs' })} data-slot="toast-action">
                    {toast.actionProps.children}
                  </Toast.Action>
                )}
              </Toast.Content>
            </Toast.Root>
          );
        })}
      </Toast.Viewport>
    </Toast.Portal>
  );
}

function AnchoredToasts({
  portalProps,
}: {
  portalProps?: React.ComponentProps<typeof Toast.Portal>;
}): React.ReactElement {
  const { toasts } = Toast.useToastManager();

  return (
    <Toast.Portal data-slot="toast-portal-anchored" {...portalProps}>
      <Toast.Viewport className="outline-none" data-slot="toast-viewport-anchored">
        {toasts.map((toast) => {
          const Icon = toast.type ? TOAST_ICONS[toast.type as keyof typeof TOAST_ICONS] : null;
          const tooltipStyle = (toast.data as { tooltipStyle?: boolean })?.tooltipStyle ?? false;
          const positionerProps = toast.positionerProps;

          if (!positionerProps?.anchor) {
            return null;
          }

          return (
            <Toast.Positioner
              key={toast.id}
              className="z-50 max-w-[min(--spacing(64),var(--available-width))]"
              data-slot="toast-positioner"
              sideOffset={positionerProps.sideOffset ?? 4}
              toast={toast}
            >
              <Toast.Root
                className={cn(
                  'relative text-balance border border-slate-6 bg-slate-2 text-slate-12 text-xs transition-[scale,opacity] before:pointer-events-none before:absolute before:inset-0 before:shadow-[0_1px_--theme(--color-black/4%)] data-ending-style:scale-98 data-starting-style:scale-98 data-ending-style:opacity-0 data-starting-style:opacity-0',
                  tooltipStyle
                    ? 'rounded-md shadow-md/5 before:rounded-[calc(var(--radius-md)-1px)]'
                    : 'rounded-lg shadow-lg/5 before:rounded-[calc(var(--radius-lg)-1px)]',
                  upsertReplayClassName(toast),
                )}
                data-slot="toast-popup"
                toast={toast}
              >
                {tooltipStyle ? (
                  <Toast.Content className="pointer-events-auto px-2 py-1">
                    <Toast.Title data-slot="toast-title" />
                  </Toast.Content>
                ) : (
                  <>
                    <Toast.Close
                      aria-label="Fechar"
                      className="pointer-events-auto absolute right-1.5 top-1.5 z-10 flex size-5 items-center justify-center rounded-full bg-slate-4 text-slate-11 transition-colors hover:bg-slate-5 hover:text-slate-12 focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-slate-7"
                    >
                      <XClose className="size-3" />
                    </Toast.Close>
                  <Toast.Content className="pointer-events-auto flex items-center justify-between gap-1.5 overflow-hidden px-3.5 py-3 pr-7 text-sm">
                    <div className="flex gap-2">
                      {Icon && (
                        <div
                          className="[&>svg]:h-lh [&>svg]:w-4 [&_svg]:pointer-events-none [&_svg]:shrink-0"
                          data-slot="toast-icon"
                        >
                          <Icon className="in-data-[type=loading]:animate-spin in-data-[type=error]:text-red-9 in-data-[type=info]:text-blue-9 in-data-[type=success]:text-green-9 in-data-[type=warning]:text-amber-9 in-data-[type=loading]:opacity-80" />
                        </div>
                      )}

                      <div className="flex flex-col gap-0.5">
                        <Toast.Title className="font-medium" data-slot="toast-title" />
                        <Toast.Description
                          className="text-slate-11"
                          data-slot="toast-description"
                        />
                      </div>
                    </div>
                    {toast.actionProps && (
                      <Toast.Action
                        className={buttonVariants({ size: 'xs' })}
                        data-slot="toast-action"
                      >
                        {toast.actionProps.children}
                      </Toast.Action>
                    )}
                  </Toast.Content>
                  </>
                )}
              </Toast.Root>
            </Toast.Positioner>
          );
        })}
      </Toast.Viewport>
    </Toast.Portal>
  );
}

export const toastManager: ReturnType<typeof Toast.createToastManager> = Toast.createToastManager();

export const anchoredToastManager: ReturnType<typeof Toast.createToastManager> =
  Toast.createToastManager();

export type ToastPosition =
  | 'top-left'
  | 'top-center'
  | 'top-right'
  | 'bottom-left'
  | 'bottom-center'
  | 'bottom-right';

export interface ToastProviderProps extends Toast.Provider.Props {
  position?: ToastPosition;
  portalProps?: React.ComponentProps<typeof Toast.Portal>;
}

export function ToastProvider({
  children,
  position = 'bottom-right',
  portalProps,
  ...props
}: ToastProviderProps): React.ReactElement {
  return (
    <Toast.Provider toastManager={toastManager} {...props}>
      {children}
      <Toasts portalProps={portalProps} position={position} />
    </Toast.Provider>
  );
}

export interface AnchoredToastProviderProps extends Toast.Provider.Props {
  portalProps?: React.ComponentProps<typeof Toast.Portal>;
}

export function AnchoredToastProvider({
  children,
  portalProps,
  ...props
}: AnchoredToastProviderProps): React.ReactElement {
  return (
    <Toast.Provider toastManager={anchoredToastManager} {...props}>
      {children}
      <AnchoredToasts portalProps={portalProps} />
    </Toast.Provider>
  );
}

export { Toast as ToastPrimitive };
```

---

## 📂 `context-menu.tsx`

Caminho original no repositório: `/packages/ui/src/components/context-menu.tsx`

```tsx
import { ContextMenu as ContextMenuPrimitive } from '@base-ui/react/context-menu';
import type * as React from 'react';

import { cn } from '@manager/lib';
import { ArrowRight, Check } from '@untitled-ui/icons-react';

function ContextMenu({ ...props }: ContextMenuPrimitive.Root.Props) {
  return <ContextMenuPrimitive.Root data-slot="context-menu" {...props} />;
}

function ContextMenuPortal({ ...props }: ContextMenuPrimitive.Portal.Props) {
  return <ContextMenuPrimitive.Portal data-slot="context-menu-portal" {...props} />;
}

function ContextMenuTrigger({ className, ...props }: ContextMenuPrimitive.Trigger.Props) {
  return (
    <ContextMenuPrimitive.Trigger
      data-slot="context-menu-trigger"
      className={cn('select-none', className)}
      {...props}
    />
  );
}

function ContextMenuContent({
  className,
  align = 'start',
  alignOffset = 4,
  side = 'right',
  sideOffset = 0,
  ...props
}: ContextMenuPrimitive.Popup.Props &
  Pick<ContextMenuPrimitive.Positioner.Props, 'align' | 'alignOffset' | 'side' | 'sideOffset'>) {
  return (
    <ContextMenuPrimitive.Portal>
      <ContextMenuPrimitive.Positioner
        className="isolate z-50 outline-none"
        align={align}
        alignOffset={alignOffset}
        side={side}
        sideOffset={sideOffset}
      >
        <ContextMenuPrimitive.Popup
          data-slot="context-menu-content"
          className={cn(
            'data-open:animate-in data-closed:animate-out data-closed:fade-out-0 data-open:fade-in-0 data-closed:zoom-out-95 data-open:zoom-in-95 data-[side=bottom]:slide-in-from-top-2 data-[side=left]:slide-in-from-right-2 data-[side=right]:slide-in-from-left-2 data-[side=top]:slide-in-from-bottom-2 ring-zinc-950/10 bg-white text-zinc-950 min-w-36 rounded-md p-1 shadow-md ring-1 duration-100 z-50 max-h-(--available-height) origin-(--transform-origin) overflow-x-hidden overflow-y-auto outline-none dark:ring-zinc-50/10 dark:bg-zinc-950 dark:text-zinc-50',
            className,
          )}
          {...props}
        />
      </ContextMenuPrimitive.Positioner>
    </ContextMenuPrimitive.Portal>
  );
}

function ContextMenuGroup({ ...props }: ContextMenuPrimitive.Group.Props) {
  return <ContextMenuPrimitive.Group data-slot="context-menu-group" {...props} />;
}

function ContextMenuLabel({
  className,
  inset,
  ...props
}: ContextMenuPrimitive.GroupLabel.Props & {
  inset?: boolean;
}) {
  return (
    <ContextMenuPrimitive.GroupLabel
      data-slot="context-menu-label"
      data-inset={inset}
      className={cn(
        'text-zinc-500 px-2 py-1.5 text-xs font-medium data-[inset]:pl-8 dark:text-zinc-400',
        className,
      )}
      {...props}
    />
  );
}

function ContextMenuItem({
  className,
  inset,
  variant = 'default',
  ...props
}: ContextMenuPrimitive.Item.Props & {
  inset?: boolean;
  variant?: 'default' | 'destructive';
}) {
  return (
    <ContextMenuPrimitive.Item
      data-slot="context-menu-item"
      data-inset={inset}
      data-variant={variant}
      className={cn(
        "focus:bg-zinc-100 focus:text-zinc-900 data-[variant=destructive]:text-red-500 data-[variant=destructive]:focus:bg-red-500/10 dark:data-[variant=destructive]:focus:bg-red-500/20 data-[variant=destructive]:focus:text-red-500 data-[variant=destructive]:*:[svg]:text-red-500 focus:*:[svg]:text-zinc-900 gap-2 rounded-sm px-2 py-1.5 text-sm [&_svg:not([class*='size-'])]:size-4 group/context-menu-item relative flex cursor-default items-center outline-hidden select-none data-disabled:pointer-events-none data-disabled:opacity-50 data-[inset]:pl-8 [&_svg]:pointer-events-none [&_svg]:shrink-0 dark:focus:bg-zinc-800 dark:focus:text-zinc-50 dark:data-[variant=destructive]:text-red-900 dark:data-[variant=destructive]:focus:bg-red-900/10 dark:dark:data-[variant=destructive]:focus:bg-red-900/20 dark:data-[variant=destructive]:focus:text-red-900 dark:data-[variant=destructive]:*:[svg]:text-red-900 dark:focus:*:[svg]:text-zinc-50",
        className,
      )}
      {...props}
    />
  );
}

function ContextMenuSub({ ...props }: ContextMenuPrimitive.SubmenuRoot.Props) {
  return <ContextMenuPrimitive.SubmenuRoot data-slot="context-menu-sub" {...props} />;
}

function ContextMenuSubTrigger({
  className,
  inset,
  children,
  ...props
}: ContextMenuPrimitive.SubmenuTrigger.Props & {
  inset?: boolean;
}) {
  return (
    <ContextMenuPrimitive.SubmenuTrigger
      data-slot="context-menu-sub-trigger"
      data-inset={inset}
      className={cn(
        "focus:bg-zinc-100 focus:text-zinc-900 data-open:bg-zinc-100 data-open:text-zinc-900 rounded-sm px-2 py-1.5 text-sm [&_svg:not([class*='size-'])]:size-4 flex cursor-default items-center outline-hidden select-none data-[inset]:pl-8 [&_svg]:pointer-events-none [&_svg]:shrink-0 dark:focus:bg-zinc-800 dark:focus:text-zinc-50 dark:data-open:bg-zinc-800 dark:data-open:text-zinc-50",
        className,
      )}
      {...props}
    >
      {children}
      <ArrowRight className="ml-auto" />
    </ContextMenuPrimitive.SubmenuTrigger>
  );
}

function ContextMenuSubContent({ ...props }: React.ComponentProps<typeof ContextMenuContent>) {
  return (
    <ContextMenuContent
      data-slot="context-menu-sub-content"
      className="shadow-lg"
      side="right"
      {...props}
    />
  );
}

function ContextMenuCheckboxItem({
  className,
  children,
  checked,
  ...props
}: ContextMenuPrimitive.CheckboxItem.Props) {
  return (
    <ContextMenuPrimitive.CheckboxItem
      data-slot="context-menu-checkbox-item"
      className={cn(
        "focus:bg-zinc-100 focus:text-zinc-900 gap-2 rounded-sm py-1.5 pr-8 pl-2 text-sm [&_svg:not([class*='size-'])]:size-4 relative flex cursor-default items-center outline-hidden select-none data-[disabled]:pointer-events-none data-[disabled]:opacity-50 [&_svg]:pointer-events-none [&_svg]:shrink-0 dark:focus:bg-zinc-800 dark:focus:text-zinc-50",
        className,
      )}
      checked={checked}
      {...props}
    >
      <span className="absolute right-2 pointer-events-none">
        <ContextMenuPrimitive.CheckboxItemIndicator>
          <Check />
        </ContextMenuPrimitive.CheckboxItemIndicator>
      </span>
      {children}
    </ContextMenuPrimitive.CheckboxItem>
  );
}

function ContextMenuRadioGroup({ ...props }: ContextMenuPrimitive.RadioGroup.Props) {
  return <ContextMenuPrimitive.RadioGroup data-slot="context-menu-radio-group" {...props} />;
}

function ContextMenuRadioItem({
  className,
  children,
  ...props
}: ContextMenuPrimitive.RadioItem.Props) {
  return (
    <ContextMenuPrimitive.RadioItem
      data-slot="context-menu-radio-item"
      className={cn(
        "focus:bg-zinc-100 focus:text-zinc-900 gap-2 rounded-sm py-1.5 pr-8 pl-2 text-sm [&_svg:not([class*='size-'])]:size-4 relative flex cursor-default items-center outline-hidden select-none data-[disabled]:pointer-events-none data-[disabled]:opacity-50 [&_svg]:pointer-events-none [&_svg]:shrink-0 dark:focus:bg-zinc-800 dark:focus:text-zinc-50",
        className,
      )}
      {...props}
    >
      <span className="absolute right-2 pointer-events-none">
        <ContextMenuPrimitive.RadioItemIndicator>
          <Check />
        </ContextMenuPrimitive.RadioItemIndicator>
      </span>
      {children}
    </ContextMenuPrimitive.RadioItem>
  );
}

function ContextMenuSeparator({ className, ...props }: ContextMenuPrimitive.Separator.Props) {
  return (
    <ContextMenuPrimitive.Separator
      data-slot="context-menu-separator"
      className={cn('bg-zinc-200 -mx-1 my-1 h-px dark:bg-zinc-800', className)}
      {...props}
    />
  );
}

function ContextMenuShortcut({ className, ...props }: React.ComponentProps<'span'>) {
  return (
    <span
      data-slot="context-menu-shortcut"
      className={cn(
        'text-zinc-500 group-focus/context-menu-item:text-accent-foreground ml-auto text-xs tracking-widest dark:text-zinc-400',
        className,
      )}
      {...props}
    />
  );
}

export {
  ContextMenu,
  ContextMenuTrigger,
  ContextMenuContent,
  ContextMenuItem,
  ContextMenuCheckboxItem,
  ContextMenuRadioItem,
  ContextMenuLabel,
  ContextMenuSeparator,
  ContextMenuShortcut,
  ContextMenuGroup,
  ContextMenuPortal,
  ContextMenuSub,
  ContextMenuSubContent,
  ContextMenuSubTrigger,
  ContextMenuRadioGroup,
};
```

---

## 📂 `dropdown-menu.tsx`

Caminho original no repositório: `/packages/ui/src/components/dropdown-menu.tsx`

```tsx
import { Menu as MenuPrimitive } from '@base-ui/react/menu';
import type * as React from 'react';

import { cn } from '@manager/lib';
import { Check, ChevronRight } from '@untitled-ui/icons-react';

function DropdownMenu({ ...props }: MenuPrimitive.Root.Props) {
  return <MenuPrimitive.Root data-slot="dropdown-menu" {...props} />;
}

function DropdownMenuPortal({ ...props }: MenuPrimitive.Portal.Props) {
  return <MenuPrimitive.Portal data-slot="dropdown-menu-portal" {...props} />;
}

function DropdownMenuTrigger({ ...props }: MenuPrimitive.Trigger.Props) {
  return <MenuPrimitive.Trigger data-slot="dropdown-menu-trigger" {...props} />;
}

function DropdownMenuContent({
  align = 'start',
  alignOffset = 0,
  side = 'bottom',
  sideOffset = 4,
  className,
  ...props
}: MenuPrimitive.Popup.Props &
  Pick<MenuPrimitive.Positioner.Props, 'align' | 'alignOffset' | 'side' | 'sideOffset'>) {
  return (
    <MenuPrimitive.Portal>
      <MenuPrimitive.Positioner
        className="isolate z-50 outline-none"
        align={align}
        alignOffset={alignOffset}
        side={side}
        sideOffset={sideOffset}
      >
        <MenuPrimitive.Popup
          data-slot="dropdown-menu-content"
          className={cn(
            'data-open:animate-in data-closed:animate-out data-closed:fade-out-0 data-open:fade-in-0 data-closed:zoom-out-95 data-open:zoom-in-95 data-[side=bottom]:slide-in-from-top-2 data-[side=left]:slide-in-from-right-2 data-[side=right]:slide-in-from-left-2 data-[side=top]:slide-in-from-bottom-2 ring-zinc-950/10 bg-white text-zinc-950 min-w-32 rounded-md p-1 shadow-md ring-1 duration-100 z-50 max-h-(--available-height) w-(--anchor-width) origin-(--transform-origin) overflow-x-hidden overflow-y-auto outline-none data-closed:overflow-hidden dark:ring-zinc-50/10 dark:bg-zinc-950 dark:text-zinc-50',
            className,
          )}
          {...props}
        />
      </MenuPrimitive.Positioner>
    </MenuPrimitive.Portal>
  );
}

function DropdownMenuGroup({ ...props }: MenuPrimitive.Group.Props) {
  return <MenuPrimitive.Group data-slot="dropdown-menu-group" {...props} />;
}

function DropdownMenuLabel({
  className,
  inset,
  ...props
}: MenuPrimitive.GroupLabel.Props & {
  inset?: boolean;
}) {
  return (
    <MenuPrimitive.GroupLabel
      data-slot="dropdown-menu-label"
      data-inset={inset}
      className={cn(
        'text-zinc-500 px-2 py-1.5 text-xs font-medium data-[inset]:pl-8 dark:text-zinc-400',
        className,
      )}
      {...props}
    />
  );
}

function DropdownMenuItem({
  className,
  inset,
  variant = 'default',
  ...props
}: MenuPrimitive.Item.Props & {
  inset?: boolean;
  variant?: 'default' | 'destructive';
}) {
  return (
    <MenuPrimitive.Item
      data-slot="dropdown-menu-item"
      data-inset={inset}
      data-variant={variant}
      className={cn(
        "focus:bg-zinc-100 focus:text-zinc-900 data-[variant=destructive]:text-red-500 data-[variant=destructive]:focus:bg-red-500/10 dark:data-[variant=destructive]:focus:bg-red-500/20 data-[variant=destructive]:focus:text-red-500 data-[variant=destructive]:*:[svg]:text-red-500 not-data-[variant=destructive]:focus:**:text-zinc-900 gap-2 rounded-sm px-2 py-1.5 text-sm [&_svg:not([class*='size-'])]:size-4 group/dropdown-menu-item relative flex cursor-default items-center outline-hidden select-none data-disabled:pointer-events-none data-disabled:opacity-50 data-[inset]:pl-8 [&_svg]:pointer-events-none [&_svg]:shrink-0 dark:focus:bg-zinc-800 dark:focus:text-zinc-50 dark:data-[variant=destructive]:text-red-900 dark:data-[variant=destructive]:focus:bg-red-900/10 dark:dark:data-[variant=destructive]:focus:bg-red-900/20 dark:data-[variant=destructive]:focus:text-red-900 dark:data-[variant=destructive]:*:[svg]:text-red-900 dark:not-data-[variant=destructive]:focus:**:text-zinc-50",
        className,
      )}
      {...props}
    />
  );
}

function DropdownMenuSub({ ...props }: MenuPrimitive.SubmenuRoot.Props) {
  return <MenuPrimitive.SubmenuRoot data-slot="dropdown-menu-sub" {...props} />;
}

function DropdownMenuSubTrigger({
  className,
  inset,
  children,
  ...props
}: MenuPrimitive.SubmenuTrigger.Props & {
  inset?: boolean;
}) {
  return (
    <MenuPrimitive.SubmenuTrigger
      data-slot="dropdown-menu-sub-trigger"
      data-inset={inset}
      className={cn(
        "focus:bg-zinc-100 focus:text-zinc-900 data-open:bg-zinc-100 data-open:text-zinc-900 not-data-[variant=destructive]:focus:**:text-zinc-900 gap-2 rounded-sm px-2 py-1.5 text-sm [&_svg:not([class*='size-'])]:size-4 flex cursor-default items-center outline-hidden select-none data-[inset]:pl-8 [&_svg]:pointer-events-none [&_svg]:shrink-0 dark:focus:bg-zinc-800 dark:focus:text-zinc-50 dark:data-open:bg-zinc-800 dark:data-open:text-zinc-50 dark:not-data-[variant=destructive]:focus:**:text-zinc-50",
        className,
      )}
      {...props}
    >
      {children}
      <ChevronRight className="w-4 h-4 ml-auto" />
    </MenuPrimitive.SubmenuTrigger>
  );
}

function DropdownMenuSubContent({
  align = 'start',
  alignOffset = -3,
  side = 'right',
  sideOffset = 0,
  className,
  ...props
}: React.ComponentProps<typeof DropdownMenuContent>) {
  return (
    <DropdownMenuContent
      data-slot="dropdown-menu-sub-content"
      className={cn(
        'data-open:animate-in data-closed:animate-out data-closed:fade-out-0 data-open:fade-in-0 data-closed:zoom-out-95 data-open:zoom-in-95 data-[side=bottom]:slide-in-from-top-2 data-[side=left]:slide-in-from-right-2 data-[side=right]:slide-in-from-left-2 data-[side=top]:slide-in-from-bottom-2 ring-zinc-950/10 bg-white text-zinc-950 min-w-[96px] rounded-md p-1 shadow-lg ring-1 duration-100 w-auto dark:ring-zinc-50/10 dark:bg-zinc-950 dark:text-zinc-50',
        className,
      )}
      align={align}
      alignOffset={alignOffset}
      side={side}
      sideOffset={sideOffset}
      {...props}
    />
  );
}

function DropdownMenuCheckboxItem({
  className,
  children,
  checked,
  ...props
}: MenuPrimitive.CheckboxItem.Props) {
  return (
    <MenuPrimitive.CheckboxItem
      data-slot="dropdown-menu-checkbox-item"
      className={cn(
        "focus:bg-zinc-100 focus:text-zinc-900 focus:**:text-zinc-900 gap-2 rounded-sm py-1.5 pr-8 pl-2 text-sm [&_svg:not([class*='size-'])]:size-4 relative flex cursor-default items-center outline-hidden select-none data-[disabled]:pointer-events-none data-[disabled]:opacity-50 [&_svg]:pointer-events-none [&_svg]:shrink-0 dark:focus:bg-zinc-800 dark:focus:text-zinc-50 dark:focus:**:text-zinc-50",
        className,
      )}
      checked={checked}
      {...props}
    >
      <span
        className="pointer-events-none absolute right-2 flex items-center justify-center"
        data-slot="dropdown-menu-checkbox-item-indicator"
      >
        <MenuPrimitive.CheckboxItemIndicator>
          <Check className="w-4 h-4" />
        </MenuPrimitive.CheckboxItemIndicator>
      </span>
      {children}
    </MenuPrimitive.CheckboxItem>
  );
}

function DropdownMenuRadioGroup({ ...props }: MenuPrimitive.RadioGroup.Props) {
  return <MenuPrimitive.RadioGroup data-slot="dropdown-menu-radio-group" {...props} />;
}

function DropdownMenuRadioItem({ className, children, ...props }: MenuPrimitive.RadioItem.Props) {
  return (
    <MenuPrimitive.RadioItem
      data-slot="dropdown-menu-radio-item"
      className={cn(
        "focus:bg-zinc-100 focus:text-zinc-900 focus:**:text-zinc-900 gap-2 rounded-sm py-1.5 pr-8 pl-2 text-sm [&_svg:not([class*='size-'])]:size-4 relative flex cursor-default items-center outline-hidden select-none data-[disabled]:pointer-events-none data-[disabled]:opacity-50 [&_svg]:pointer-events-none [&_svg]:shrink-0 dark:focus:bg-zinc-800 dark:focus:text-zinc-50 dark:focus:**:text-zinc-50",
        className,
      )}
      {...props}
    >
      <span
        className="pointer-events-none absolute right-2 flex items-center justify-center"
        data-slot="dropdown-menu-radio-item-indicator"
      >
        <MenuPrimitive.RadioItemIndicator>
          <Check className="w-4 h-4" />
        </MenuPrimitive.RadioItemIndicator>
      </span>
      {children}
    </MenuPrimitive.RadioItem>
  );
}

function DropdownMenuSeparator({ className, ...props }: MenuPrimitive.Separator.Props) {
  return (
    <MenuPrimitive.Separator
      data-slot="dropdown-menu-separator"
      className={cn('bg-zinc-200 -mx-1 my-1 h-px dark:bg-zinc-800', className)}
      {...props}
    />
  );
}

function DropdownMenuShortcut({ className, ...props }: React.ComponentProps<'span'>) {
  return (
    <span
      data-slot="dropdown-menu-shortcut"
      className={cn(
        'text-zinc-500 group-focus/dropdown-menu-item:text-accent-foreground ml-auto text-xs tracking-widest dark:text-zinc-400',
        className,
      )}
      {...props}
    />
  );
}

export {
  DropdownMenu,
  DropdownMenuPortal,
  DropdownMenuTrigger,
  DropdownMenuContent,
  DropdownMenuGroup,
  DropdownMenuLabel,
  DropdownMenuItem,
  DropdownMenuCheckboxItem,
  DropdownMenuRadioGroup,
  DropdownMenuRadioItem,
  DropdownMenuSeparator,
  DropdownMenuShortcut,
  DropdownMenuSub,
  DropdownMenuSubTrigger,
  DropdownMenuSubContent,
};
```

---

## 📂 `menubar.tsx`

Caminho original no repositório: `/packages/ui/src/components/menubar.tsx`

```tsx
'use client';

import { Menu as MenuPrimitive } from '@base-ui/react/menu';
import { Menubar as MenubarPrimitive } from '@base-ui/react/menubar';
import type * as React from 'react';

import { cn } from '@manager/lib';
import { Check } from '@untitled-ui/icons-react';
import {
  DropdownMenu,
  DropdownMenuContent,
  DropdownMenuGroup,
  DropdownMenuItem,
  DropdownMenuLabel,
  DropdownMenuPortal,
  DropdownMenuRadioGroup,
  DropdownMenuSeparator,
  DropdownMenuShortcut,
  DropdownMenuSub,
  DropdownMenuSubContent,
  DropdownMenuSubTrigger,
  DropdownMenuTrigger,
} from './dropdown-menu';

function Menubar({ className, ...props }: MenubarPrimitive.Props) {
  return (
    <MenubarPrimitive
      data-slot="menubar"
      className={cn(
        'bg-white h-9 gap-1 rounded-md border border-zinc-200 p-1 shadow-xs flex items-center dark:bg-zinc-950 dark:border-zinc-800',
        className,
      )}
      {...props}
    />
  );
}

function MenubarMenu({ ...props }: React.ComponentProps<typeof DropdownMenu>) {
  return <DropdownMenu data-slot="menubar-menu" {...props} />;
}

function MenubarGroup({ ...props }: React.ComponentProps<typeof DropdownMenuGroup>) {
  return <DropdownMenuGroup data-slot="menubar-group" {...props} />;
}

function MenubarPortal({ ...props }: React.ComponentProps<typeof DropdownMenuPortal>) {
  return <DropdownMenuPortal data-slot="menubar-portal" {...props} />;
}

function MenubarTrigger({ className, ...props }: React.ComponentProps<typeof DropdownMenuTrigger>) {
  return (
    <DropdownMenuTrigger
      data-slot="menubar-trigger"
      className={cn(
        'hover:bg-zinc-100 aria-expanded:bg-zinc-100 rounded-sm px-2 py-1 text-sm font-medium flex items-center outline-hidden select-none dark:hover:bg-zinc-800 dark:aria-expanded:bg-zinc-800',
        className,
      )}
      {...props}
    />
  );
}

function MenubarContent({
  className,
  align = 'start',
  alignOffset = -4,
  sideOffset = 8,
  ...props
}: React.ComponentProps<typeof DropdownMenuContent>) {
  return (
    <DropdownMenuContent
      data-slot="menubar-content"
      align={align}
      alignOffset={alignOffset}
      sideOffset={sideOffset}
      className={cn(
        'bg-white text-zinc-950 data-open:animate-in data-open:fade-in-0 data-open:zoom-in-95 data-[side=bottom]:slide-in-from-top-2 data-[side=left]:slide-in-from-right-2 data-[side=right]:slide-in-from-left-2 data-[side=top]:slide-in-from-bottom-2 ring-zinc-950/10 min-w-36 rounded-md p-1 shadow-md ring-1 duration-100 dark:bg-zinc-950 dark:text-zinc-50 dark:ring-zinc-50/10',
        className,
      )}
      {...props}
    />
  );
}

function MenubarItem({
  className,
  inset,
  variant = 'default',
  ...props
}: React.ComponentProps<typeof DropdownMenuItem>) {
  return (
    <DropdownMenuItem
      data-slot="menubar-item"
      data-inset={inset}
      data-variant={variant}
      className={cn(
        "focus:bg-zinc-100 focus:text-zinc-900 data-[variant=destructive]:text-red-500 data-[variant=destructive]:focus:bg-red-500/10 dark:data-[variant=destructive]:focus:bg-red-500/20 data-[variant=destructive]:focus:text-red-500 data-[variant=destructive]:*:[svg]:!text-destructive not-data-[variant=destructive]:focus:**:text-zinc-900 gap-2 rounded-sm px-2 py-1.5 text-sm data-[disabled]:opacity-50 data-[inset]:pl-8 [&_svg:not([class*='size-'])]:size-4 group/menubar-item dark:focus:bg-zinc-800 dark:focus:text-zinc-50 dark:data-[variant=destructive]:text-red-900 dark:data-[variant=destructive]:focus:bg-red-900/10 dark:dark:data-[variant=destructive]:focus:bg-red-900/20 dark:data-[variant=destructive]:focus:text-red-900 dark:not-data-[variant=destructive]:focus:**:text-zinc-50",
        className,
      )}
      {...props}
    />
  );
}

function MenubarCheckboxItem({
  className,
  children,
  checked,
  ...props
}: MenuPrimitive.CheckboxItem.Props) {
  return (
    <MenuPrimitive.CheckboxItem
      data-slot="menubar-checkbox-item"
      className={cn(
        'focus:bg-zinc-100 focus:text-zinc-900 focus:**:text-zinc-900 gap-2 rounded-md py-1.5 pr-2 pl-8 text-sm data-disabled:opacity-50 relative flex cursor-default items-center outline-hidden select-none data-disabled:pointer-events-none [&_svg]:pointer-events-none [&_svg]:shrink-0 dark:focus:bg-zinc-800 dark:focus:text-zinc-50 dark:focus:**:text-zinc-50',
        className,
      )}
      checked={checked}
      {...props}
    >
      <span className="left-2 size-4 [&_svg:not([class*='size-'])]:size-4 pointer-events-none absolute flex items-center justify-center">
        <MenuPrimitive.CheckboxItemIndicator>
          <Check />
        </MenuPrimitive.CheckboxItemIndicator>
      </span>
      {children}
    </MenuPrimitive.CheckboxItem>
  );
}

function MenubarRadioGroup({ ...props }: React.ComponentProps<typeof DropdownMenuRadioGroup>) {
  return <DropdownMenuRadioGroup data-slot="menubar-radio-group" {...props} />;
}

function MenubarRadioItem({ className, children, ...props }: MenuPrimitive.RadioItem.Props) {
  return (
    <MenuPrimitive.RadioItem
      data-slot="menubar-radio-item"
      className={cn(
        "focus:bg-zinc-100 focus:text-zinc-900 focus:**:text-zinc-900 gap-2 rounded-md py-1.5 pr-2 pl-8 text-sm data-disabled:opacity-50 [&_svg:not([class*='size-'])]:size-4 relative flex cursor-default items-center outline-hidden select-none data-disabled:pointer-events-none [&_svg]:pointer-events-none [&_svg]:shrink-0 dark:focus:bg-zinc-800 dark:focus:text-zinc-50 dark:focus:**:text-zinc-50",
        className,
      )}
      {...props}
    >
      <span className="left-2 size-4 [&_svg:not([class*='size-'])]:size-4 pointer-events-none absolute flex items-center justify-center">
        <MenuPrimitive.RadioItemIndicator>
          <Check />
        </MenuPrimitive.RadioItemIndicator>
      </span>
      {children}
    </MenuPrimitive.RadioItem>
  );
}

function MenubarLabel({
  className,
  inset,
  ...props
}: React.ComponentProps<typeof DropdownMenuLabel>) {
  return (
    <DropdownMenuLabel
      data-slot="menubar-label"
      data-inset={inset}
      className={cn('px-2 py-1.5 text-sm font-medium data-[inset]:pl-8', className)}
      {...props}
    />
  );
}

function MenubarSeparator({
  className,
  ...props
}: React.ComponentProps<typeof DropdownMenuSeparator>) {
  return (
    <DropdownMenuSeparator
      data-slot="menubar-separator"
      className={cn('bg-zinc-200 -mx-1 my-1 h-px dark:bg-zinc-800', className)}
      {...props}
    />
  );
}

function MenubarShortcut({
  className,
  ...props
}: React.ComponentProps<typeof DropdownMenuShortcut>) {
  return (
    <DropdownMenuShortcut
      data-slot="menubar-shortcut"
      className={cn(
        'text-zinc-500 group-focus/menubar-item:text-accent-foreground text-xs tracking-widest ml-auto dark:text-zinc-400',
        className,
      )}
      {...props}
    />
  );
}

function MenubarSub({ ...props }: React.ComponentProps<typeof DropdownMenuSub>) {
  return <DropdownMenuSub data-slot="menubar-sub" {...props} />;
}

function MenubarSubTrigger({
  className,
  inset,
  ...props
}: React.ComponentProps<typeof DropdownMenuSubTrigger> & {
  inset?: boolean;
}) {
  return (
    <DropdownMenuSubTrigger
      data-slot="menubar-sub-trigger"
      data-inset={inset}
      className={cn(
        "focus:bg-zinc-100 focus:text-zinc-900 data-open:bg-zinc-100 data-open:text-zinc-900 gap-2 rounded-sm px-2 py-1.5 text-sm data-[inset]:pl-8 [&_svg:not([class*='size-'])]:size-4 dark:focus:bg-zinc-800 dark:focus:text-zinc-50 dark:data-open:bg-zinc-800 dark:data-open:text-zinc-50",
        className,
      )}
      {...props}
    />
  );
}

function MenubarSubContent({
  className,
  ...props
}: React.ComponentProps<typeof DropdownMenuSubContent>) {
  return (
    <DropdownMenuSubContent
      data-slot="menubar-sub-content"
      className={cn(
        'bg-white text-zinc-950 data-open:animate-in data-closed:animate-out data-closed:fade-out-0 data-open:fade-in-0 data-closed:zoom-out-95 data-open:zoom-in-95 data-[side=bottom]:slide-in-from-top-2 data-[side=left]:slide-in-from-right-2 data-[side=right]:slide-in-from-left-2 data-[side=top]:slide-in-from-bottom-2 ring-zinc-950/10 min-w-32 rounded-md p-1 shadow-lg ring-1 duration-100 dark:bg-zinc-950 dark:text-zinc-50 dark:ring-zinc-50/10',
        className,
      )}
      {...props}
    />
  );
}

export {
  Menubar,
  MenubarPortal,
  MenubarMenu,
  MenubarTrigger,
  MenubarContent,
  MenubarGroup,
  MenubarSeparator,
  MenubarLabel,
  MenubarItem,
  MenubarShortcut,
  MenubarCheckboxItem,
  MenubarRadioGroup,
  MenubarRadioItem,
  MenubarSub,
  MenubarSubTrigger,
  MenubarSubContent,
};
```

---

## 📂 `alert.tsx`

Caminho original no repositório: `/packages/ui/src/components/alert.tsx`

```tsx
import { type VariantProps, cva } from 'class-variance-authority';
import type * as React from 'react';

import { cn } from '@manager/lib';

const alertVariants = cva(
  "grid gap-0.5 rounded-lg border border-zinc-200 px-4 py-3 text-left text-sm has-data-[slot=alert-action]:relative has-data-[slot=alert-action]:pr-18 has-[>svg]:grid-cols-[auto_1fr] has-[>svg]:gap-x-2.5 *:[svg]:row-span-2 *:[svg]:translate-y-0.5 *:[svg]:text-current *:[svg:not([class*='size-'])]:size-4 w-full relative group/alert dark:border-zinc-800",
  {
    variants: {
      variant: {
        default: 'bg-white text-zinc-950 dark:bg-zinc-950 dark:text-zinc-50',
        destructive:
          'text-red-500 bg-white *:data-[slot=alert-description]:text-red-500/90 *:[svg]:text-current dark:text-red-900 dark:bg-zinc-950 dark:*:data-[slot=alert-description]:text-red-900/90',
      },
    },
    defaultVariants: {
      variant: 'default',
    },
  },
);

function Alert({
  className,
  variant,
  ...props
}: React.ComponentProps<'div'> & VariantProps<typeof alertVariants>) {
  return (
    <div
      data-slot="alert"
      role="alert"
      className={cn(alertVariants({ variant }), className)}
      {...props}
    />
  );
}

function AlertTitle({ className, ...props }: React.ComponentProps<'div'>) {
  return (
    <div
      data-slot="alert-title"
      className={cn(
        'font-medium group-has-[>svg]/alert:col-start-2 [&_a]:hover:text-zinc-950 [&_a]:underline [&_a]:underline-offset-3 dark:[&_a]:hover:text-zinc-50',
        className,
      )}
      {...props}
    />
  );
}

function AlertDescription({ className, ...props }: React.ComponentProps<'div'>) {
  return (
    <div
      data-slot="alert-description"
      className={cn(
        'text-zinc-500 text-sm text-balance md:text-pretty [&_p:not(:last-child)]:mb-4 [&_a]:hover:text-zinc-950 [&_a]:underline [&_a]:underline-offset-3 dark:text-zinc-400 dark:[&_a]:hover:text-zinc-50',
        className,
      )}
      {...props}
    />
  );
}

function AlertAction({ className, ...props }: React.ComponentProps<'div'>) {
  return (
    <div
      data-slot="alert-action"
      className={cn('absolute top-2.5 right-3', className)}
      {...props}
    />
  );
}

export { Alert, AlertAction, AlertDescription, AlertTitle };
```

---

