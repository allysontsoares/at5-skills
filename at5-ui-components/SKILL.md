---
name: at5-ui-components
description: Complete AT5 design system component catalog built with Base UI + Tailwind CSS v4. Includes production-ready Button, Modal, Select, Input, Textarea, Card, Badge, Accordion, and FormField components with full dark mode support and accessibility. Use when creating, extending, or styling UI components. Always trigger on keywords like component, button, modal, select, input, card, badge, accordion, design system, UI components, Base UI, or render prop pattern.
---

# at5-ui-components — Catálogo Completo de Componentes de Design System e Padrões Base UI

Esta skill autônoma documenta as diretrizes do **Design System AT5**, fornecendo o **código real completo** dos componentes de `/packages/ui/src/components`. Isso capacita agentes de IA e desenvolvedores a recriarem, estenderem ou instanciar os componentes exatos do zero em qualquer novo projeto ou ambiente.

---

## 🔑 1. Padrão de Composição: Slot Pattern (Prop `render` vs `asChild`)

A AT5 adota o **Base UI (`@base-ui/react`)** como biblioteca headless padrão. Diferente de outras bibliotecas como o Radix UI clássico, que usam a prop `asChild` para delegar a renderização para componentes filhos, o **Base UI utiliza o Slot Pattern baseado na prop `render`**.

> [!CRITICAL]
> **REGRA DE OURO**: **Nunca** use a propriedade `asChild` em triggers, close triggers ou seletores ao construir componentes Base UI **voltados ao consumidor**. Use sempre a prop `render` passando a chamada do componente interno ou uma função de renderização.
>
> O componente `Button` do design system aceita `asChild` internamente como helper de compatibilidade, mas **não deve ser exposto ou incentivado para uso externo**. Prefira sempre `render={<button />}` ou `render={<a />}`.

## 🎨 Nota sobre Escalas de Cores (`mauve` vs `slate`)

Os componentes deste catálogo usam duas escalas neutras:
- **`mauve`**: Para elementos de formulário (inputs, selects, badges) — levemente arroxeado, ideal para neutralidade com temperatura.
- **`slate`**: Para estrutura e containers (cards, modais, backgrounds) — levemente azulado, ideal para profundidade.

Ambas as escalas estão mapeadas no `tailwind.config.js` via `@radix-ui/colors` e funcionam com dark mode automático pela classe `.dark`. **Não substitua `mauve` por `slate` nem vice-versa** — cada uma tem seu papel semântico no design system.

### Exemplo Incorreto (Radix Style):
```tsx
// ❌ NÃO FAZER!
<DialogTrigger asChild>
  <Button variant="solid">Abrir</Button>
</DialogTrigger>
```

### Exemplo Correto (Base UI Style):
```tsx
// ✅ FAZER!
<DialogTrigger render={<Button variant="solid" />}>
  Abrir
</DialogTrigger>
```

---

## 📦 2. Códigos de Implementação Reais Completos

Aqui está o catálogo de componentes reais do diretório `/packages/ui/src/components`. Cada arquivo está pronto para ser dropado no repositório de qualquer projeto.

---

### 📂 2.1 Componente `Button` (`/packages/ui/src/components/button.tsx`)

Um botão robusto baseado no `useRender` do Base UI e `tailwind-variants` para o gerenciamento dinâmico de dezenas de variantes estéticas, tamanhos e suporte completo a loading states.

```tsx
'use client';

import { mergeProps, useRender } from '@base-ui/react';
import { cn } from '@manager/lib'; // Utilitário de classes cn
import * as React from 'react';
import { forwardRef } from 'react';
import { type VariantProps, tv } from 'tailwind-variants';

export const buttonStyles = tv({
  base: 'inline-flex items-center justify-center whitespace-nowrap rounded-md font-medium transition-colors duration-200 outline-none focus-visible:ring-1 focus-visible:ring-ring disabled:pointer-events-none disabled:opacity-50 [&_svg]:shrink-0 active:scale-95',
  variants: {
    variant: {
      solid: '',
      soft: '',
      surface: 'border-2',
      outline: 'border bg-transparent',
      ghost: 'bg-transparent',
      dashed: 'border border-dashed bg-transparent',
      dim: 'bg-transparent',
    },
    accent: {
      primary: 'bg-brand-9 text-white hover:bg-brand-10 focus-visible:outline-brand-9',
      secondary: 'bg-mauve-5 text-mauve-12 border border-mauve-5 hover:bg-mauve-6 focus-visible:outline-mauve-9',
      success: 'bg-green-9 text-white hover:bg-green-10 focus-visible:outline-green-9',
      warning: 'bg-amber-9 text-amber-1 hover:bg-amber-10 focus-visible:outline-amber-9',
      danger: 'bg-red-9 text-white hover:bg-red-10 focus-visible:outline-red-9',
      gray: 'bg-gray-9 text-white hover:bg-gray-10 focus-visible:outline-gray-9',
      slate: 'bg-slate-9 text-white hover:bg-slate-10 focus-visible:outline-slate-9',
    },
    size: {
      xs: 'gap-x-1 px-2.5 py-1.5 text-xs min-h-7 *:data-[slot=icon]:size-3.5',
      sm: 'gap-x-1.5 px-3 py-1.5 text-sm min-h-8 *:data-[slot=icon]:size-4',
      md: 'gap-x-2 px-3.5 py-1.5 text-sm min-h-9 *:data-[slot=icon]:size-4.5',
      lg: 'gap-x-2 px-4 py-2 text-base min-h-10 *:data-[slot=icon]:size-5',
      xl: 'gap-x-2 px-5 py-2 text-base min-h-11 *:data-[slot=icon]:size-5',
      default: 'gap-x-2 px-3.5 py-1.5 text-sm min-h-9 *:data-[slot=icon]:size-4.5',
      icon: 'size-9 p-0',
      'icon-sm': 'size-8 p-0',
    },
    radius: {
      none: 'rounded-none',
      sm: 'rounded-sm',
      md: 'rounded-md',
      lg: 'rounded-lg',
      xl: 'rounded-xl',
      full: 'rounded-full',
    },
    mode: {
      default: '',
      icon: '',
      link: 'h-auto p-0 bg-transparent rounded-none hover:bg-transparent data-[state=open]:bg-transparent',
      input: 'justify-start font-normal hover:bg-background focus-visible:border-ring focus-visible:outline-hidden focus-visible:ring-[3px] focus-visible:ring-ring/30',
    },
    appearance: {
      default: '',
      ghost: '',
    },
    isLoading: {
      true: 'cursor-wait opacity-70',
      false: '',
    },
  },
  compoundVariants: [
    { mode: 'icon', size: 'xs', className: 'size-7 p-0' },
    { mode: 'icon', size: 'sm', className: 'size-8 p-0' },
    { mode: 'icon', size: 'md', className: 'size-9 p-0' },
    { variant: 'solid', mode: 'default', className: 'shadow-xs shadow-black/5' },
    { variant: 'outline', mode: 'default', className: 'shadow-xs shadow-black/5' },
  ],
  defaultVariants: {
    variant: 'solid',
    accent: 'primary',
    size: 'default',
    radius: 'md',
    mode: 'default',
    appearance: 'default',
    isLoading: false,
  },
});

export type ButtonProps = useRender.ComponentProps<'button'> &
  VariantProps<typeof buttonStyles> & {
    pendingText?: string;
    asChild?: boolean;
  };

export const Button = forwardRef<HTMLButtonElement, ButtonProps>(
  (
    {
      children,
      variant,
      accent,
      size,
      radius,
      mode,
      appearance,
      isLoading = false,
      pendingText,
      render,
      asChild = false,
      className,
      ...otherProps
    },
    forwardedRef,
  ) => {
    const defaultProps = {
      className: cn(
        buttonStyles({
          variant,
          accent,
          size,
          radius,
          mode,
          appearance,
          isLoading,
        }),
        className,
      ),
    };

    const buttonChildren = (
      <>
        {isLoading && (
          <span className="size-4 animate-spin border-2 border-current border-t-transparent rounded-full me-2 shrink-0" />
        )}
        {isLoading && pendingText ? pendingText : children}
      </>
    );

    const renderElement =
      asChild && React.isValidElement(children)
        ? (children as React.ReactElement)
        : render || <button />;

    const finalProps =
      asChild && React.isValidElement(children)
        ? mergeProps(defaultProps, otherProps)
        : mergeProps(defaultProps, { ...otherProps, children: buttonChildren });

    return useRender({
      render: renderElement,
      props: finalProps,
      ref: forwardedRef,
    });
  },
);

Button.displayName = 'Button';
```

---

### 📂 2.2 Componente `Modal` (`/packages/ui/src/components/modal.tsx`)

Mecanismo completo de diálogos compound, com suporte a **Pointer Draggable** no desktop e tamanhos customizados (`xs` a `5xl` para acomodar painéis de formulários com sidebars).

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
      'fixed inset-0 z-50 min-h-dvh bg-black/50 transition-opacity duration-150 ease-out data-[starting-style]:opacity-0 data-[ending-style]:opacity-0 dark:bg-black/60',
    ],
    container: [
      'fixed inset-0 z-50 flex min-h-dvh w-full min-w-0 flex-col items-center p-4 sm:p-10 pointer-events-none transition-[opacity,transform] duration-[250ms] ease-[cubic-bezier(0.25,0.46,0.45,0.94)] data-[ending-style]:duration-100 data-[starting-style]:opacity-0 data-[ending-style]:opacity-0 data-[starting-style]:scale-[1.05] data-[ending-style]:scale-95',
    ],
    dialog: [
      'relative flex w-full flex-col rounded-3xl bg-slate-1 text-slate-12 ring-1 ring-slate-6/60 shadow-[0_28px_80px_-32px_rgba(15,23,42,0.45),0_22px_44px_-36px_rgba(15,23,42,0.32)] p-6 pointer-events-auto outline-none dark:ring-slate-7/60',
    ],
    header: '-mx-6 -mt-6 mb-5 flex flex-col gap-3 rounded-t-3xl border-b border-slate-5 bg-slate-2 px-6 pt-6 pb-5',
    heading: 'align-middle text-base font-medium text-slate-12',
    description: 'text-sm text-slate-11',
    icon: 'flex size-10 shrink-0 select-none items-center justify-center rounded-full',
    body: 'my-0 min-h-0 flex-1 text-sm leading-[1.43] text-slate-11',
    footer: '-mx-6 -mb-6 mt-5 flex flex-row items-center justify-end gap-2 rounded-b-3xl border-t border-slate-5 bg-slate-2 px-6 pt-5 pb-6',
    closeTrigger: 'absolute top-4 right-4 inline-flex size-8 items-center justify-center rounded-full text-slate-11 transition-[transform,background-color,color,box-shadow] duration-150 ease-out hover:bg-slate-4 hover:text-slate-12 active:scale-95 focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-blue-7 focus-visible:ring-offset-2 focus-visible:ring-offset-slate-1 dark:focus-visible:ring-offset-slate-2',
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
      auto: { container: 'justify-end sm:justify-center', dialog: 'mt-auto sm:my-auto' },
      center: { container: 'justify-center', dialog: 'my-auto' },
      bottom: { container: 'justify-end', dialog: 'mt-auto' },
      top: { container: 'justify-start', dialog: 'mt-0' },
    },
    scroll: {
      inside: { dialog: 'max-h-[calc(100dvh-2rem)] overflow-hidden sm:max-h-[calc(100dvh-5rem)]', body: 'overflow-y-auto p-4' },
      outside: { container: 'overflow-y-auto pointer-events-auto', dialog: 'h-auto min-h-0 shrink-0', body: 'overflow-y-visible' },
    },
    tone: {
      brand: {
        header: 'bg-brand-9 border-brand-10 text-white',
        heading: 'text-white',
        description: 'text-white/85',
        closeTrigger: 'text-white hover:bg-white/15 hover:text-white focus-visible:ring-white/50',
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

const DRAG_MIN_WIDTH = 640;
const DRAG_IGNORE_SELECTOR = 'button, a, input, textarea, select, [role="button"]';

interface DragHandlers {
  onPointerDown: (event: ReactPointerEvent<HTMLElement>) => void;
  onPointerMove: (event: ReactPointerEvent<HTMLElement>) => void;
  onPointerUp: (event: ReactPointerEvent<HTMLElement>) => void;
  onPointerCancel: (event: ReactPointerEvent<HTMLElement>) => void;
  onDoubleClick: (event: ReactMouseEvent<HTMLElement>) => void;
}

const NOOP_HANDLERS: DragHandlers = {
  onPointerDown: () => undefined,
  onPointerMove: () => undefined,
  onPointerUp: () => undefined,
  onPointerCancel: () => undefined,
  onDoubleClick: () => undefined,
};

function useDraggable(enabled: boolean) {
  const [offset, setOffset] = useState({ x: 0, y: 0 });
  const [isDragging, setIsDragging] = useState(false);
  const dragStateRef = useRef<{ startX: number; startY: number; baseX: number; baseY: number } | null>(null);

  useEffect(() => {
    if (!enabled) {
      setOffset({ x: 0, y: 0 });
      setIsDragging(false);
      dragStateRef.current = null;
    }
  }, [enabled]);

  const onPointerDown = useCallback((event: ReactPointerEvent<HTMLElement>) => {
    if (!enabled) return;
    if (typeof window !== 'undefined' && window.innerWidth < DRAG_MIN_WIDTH) return;
    if (event.button !== 0) return;
    const target = event.target as HTMLElement;
    if (target?.closest(DRAG_IGNORE_SELECTOR)) return;

    event.preventDefault();
    dragStateRef.current = {
      startX: event.clientX,
      startY: event.clientY,
      baseX: offset.x,
      baseY: offset.y,
    };
    setIsDragging(true);
    event.currentTarget.setPointerCapture(event.pointerId);
  }, [enabled, offset]);

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
    event.currentTarget.releasePointerCapture?.(event.pointerId);
  }, []);

  const onDoubleClick = useCallback((event: ReactMouseEvent<HTMLElement>) => {
    if (!enabled) return;
    const target = event.target as HTMLElement;
    if (target?.closest(DRAG_IGNORE_SELECTOR)) return;
    setOffset({ x: 0, y: 0 });
  }, [enabled]);

  const handlers = useMemo<DragHandlers>(() =>
    enabled ? { onPointerDown, onPointerMove, onPointerUp: finishDrag, onPointerCancel: finishDrag, onDoubleClick } : NOOP_HANDLERS,
    [enabled, onPointerDown, onPointerMove, finishDrag, onDoubleClick]
  );

  return { offset, isDragging, handlers, enabled };
}

const ModalRootContext = createContext<any>({});
const ModalRuntimeContext = createContext<any>({ close: () => undefined });
const ModalContext = createContext<any>({ slots: modalVariants(), placement: 'auto', scroll: 'inside', tone: 'brand', drag: null });

function ModalRoot({ children, defaultOpen, onOpenChange, onOpenChangeComplete, open }: any) {
  const rootContext = useMemo(() => ({ defaultOpen, onOpenChange, onOpenChangeComplete, open }), [defaultOpen, onOpenChange, onOpenChangeComplete, open]);
  return <ModalRootContext.Provider value={rootContext}>{children}</ModalRootContext.Provider>;
}

function ModalTrigger({ className, children, ...props }: any) {
  const { slots } = useContext(ModalContext);
  return <DialogPrimitive.Trigger className={cn(slots.trigger(), className)} {...props}>{children}</DialogPrimitive.Trigger>;
}

function ModalBackdrop({ children, className, variant = 'opaque', isDismissable = true, isKeyboardDismissDisabled = false, isOpen: isOpenProp, onOpenChange: onOpenChangeProp }: any) {
  const rootContext = useContext(ModalRootContext);
  const open = isOpenProp ?? rootContext.open;
  const actionsRef = useRef<DialogPrimitive.Root.Actions | null>(null);
  const slots = useMemo(() => modalVariants({ backdropVariant: variant }), [variant]);

  const close = useCallback(() => { actionsRef.current?.close(); }, []);

  const handleOpenChange = useCallback((nextOpen: boolean, eventDetails: any) => {
    if (!nextOpen && !isDismissable && eventDetails.reason === 'outside-press') return;
    if (!nextOpen && isKeyboardDismissDisabled && eventDetails.reason === 'escape-key') return;
    (onOpenChangeProp ?? rootContext.onOpenChange)?.(nextOpen, eventDetails);
  }, [isDismissable, isKeyboardDismissDisabled, onOpenChangeProp, rootContext.onOpenChange]);

  return (
    <ModalRuntimeContext.Provider value={{ close }}>
      <DialogPrimitive.Root actionsRef={actionsRef} disablePointerDismissal={!isDismissable} onOpenChange={handleOpenChange} open={open}>
        <DialogPrimitive.Portal>
          <DialogPrimitive.Backdrop className={cn(slots.backdrop(), className)} />
          {children}
        </DialogPrimitive.Portal>
      </DialogPrimitive.Root>
    </ModalRuntimeContext.Provider>
  );
}

function ModalContainer({ children, className, placement = 'auto', scroll = 'inside', size = 'md', tone = 'brand', draggable = true, ...props }: any) {
  const slots = useMemo(() => modalVariants({ placement, scroll, size, tone }), [placement, scroll, size, tone]);
  const drag = useDraggable(draggable);
  const contextValue = useMemo(() => ({ placement, scroll, slots, tone, drag }), [placement, scroll, slots, tone, drag]);

  return (
    <ModalContext.Provider value={contextValue}>
      <DialogPrimitive.Viewport className={cn(slots.container(), className)} {...props}>
        {children}
      </DialogPrimitive.Viewport>
    </ModalContext.Provider>
  );
}

function ModalDialog({ children, className, style, ...props }: any) {
  const { close } = useContext(ModalRuntimeContext);
  const { placement, slots, drag } = useContext(ModalContext);

  const dragStyle: CSSProperties = drag.enabled && (drag.offset.x !== 0 || drag.offset.y !== 0 || drag.isDragging)
    ? { transform: `translate3d(${drag.offset.x}px, ${drag.offset.y}px, 0)`, transition: drag.isDragging ? 'none' : undefined, willChange: 'transform' }
    : {};

  return (
    <DialogPrimitive.Popup style={{ ...dragStyle, ...style }} className={cn(slots.dialog(), className)} {...props}>
      {typeof children === 'function' ? children({ close }) : children}
    </DialogPrimitive.Popup>
  );
}

function ModalHeader({ className, children, ...props }: any) {
  const { slots, drag } = useContext(ModalContext);
  return (
    <div className={cn(slots.header(), drag.enabled && 'sm:cursor-move sm:touch-none sm:select-none', className)} {...drag.handlers} {...props}>
      {children}
    </div>
  );
}

function ModalHeading({ className, children, ...props }: any) {
  const { slots } = useContext(ModalContext);
  return <DialogPrimitive.Title className={cn(slots.heading(), className)} {...props}>{children}</DialogPrimitive.Title>;
}

function ModalDescription({ className, children, ...props }: any) {
  const { slots } = useContext(ModalContext);
  return <DialogPrimitive.Description className={cn(slots.description(), className)} {...props}>{children}</DialogPrimitive.Description>;
}

function ModalBody({ className, children, ...props }: any) {
  const { slots } = useContext(ModalContext);
  return <div className={cn(slots.body(), className)} {...props}>{children}</div>;
}

function ModalFooter({ className, children, ...props }: any) {
  const { slots } = useContext(ModalContext);
  return <div className={cn(slots.footer(), className)} {...props}>{children}</div>;
}

function ModalCloseTrigger({ className, children, ...props }: any) {
  const { slots } = useContext(ModalContext);
  return (
    <DialogPrimitive.Close className={cn(slots.closeTrigger(), className)} {...props}>
      {children ?? <><XClose className="size-4" /><span className="sr-only">Fechar</span></>}
    </DialogPrimitive.Close>
  );
}

function ModalClose({ children, className, ...props }: any) {
  return <DialogPrimitive.Close className={className} {...props}>{children}</DialogPrimitive.Close>;
}

export const Modal = Object.assign(ModalRoot, {
  Trigger: ModalTrigger,
  Backdrop: ModalBackdrop,
  Container: ModalContainer,
  Viewport: ModalContainer,
  Dialog: ModalDialog,
  Popup: ModalDialog,
  Header: ModalHeader,
  Heading: ModalHeading,
  Title: ModalHeading,
  Description: ModalDescription,
  Body: ModalBody,
  Footer: ModalFooter,
  CloseTrigger: ModalCloseTrigger,
  Close: ModalClose,
});
```

---

### 📂 2.3 Componente `Select` (`/packages/ui/src/components/select.tsx`)

Implementação altamente customizável baseada em `@base-ui/react/select` que suporta posicionamento inteligente dos itens, indicador de item ativo na esquerda ou direita e suporte a botão "Limpar Seleção".

```tsx
import { Select as SelectPrimitive } from '@base-ui/react/select';
import { cn } from '@manager/lib';
import { Check, ChevronDown, ChevronUp, X } from '@untitled-ui/icons-react';
import { type VariantProps, cva } from 'class-variance-authority';
import * as React from 'react';
import { type ReactNode, isValidElement } from 'react';

const SelectContext = React.createContext<{
  indicatorPosition: 'left' | 'right';
  indicatorVisibility: boolean;
  indicator: ReactNode;
  icon: ReactNode;
}>({ indicatorPosition: 'left', indicator: null, indicatorVisibility: true, icon: null });

export function Select<Value = string, Multiple extends boolean = false>({
  indicatorPosition = 'left',
  indicatorVisibility = true,
  indicator,
  icon,
  ...props
}: {
  indicatorPosition?: 'left' | 'right';
  indicatorVisibility?: boolean;
  indicator?: ReactNode;
  icon?: ReactNode;
} & SelectPrimitive.Root.Props<Value, Multiple>) {
  return (
    <SelectContext.Provider value={{ indicatorPosition, indicatorVisibility, indicator, icon }}>
      <SelectPrimitive.Root data-slot="select" {...props} />
    </SelectContext.Provider>
  );
}

export function SelectGroup({ ...props }: React.ComponentProps<typeof SelectPrimitive.Group>) {
  return <SelectPrimitive.Group data-slot="select-group" {...props} />;
}

export function SelectPortal({ ...props }: React.ComponentProps<typeof SelectPrimitive.Portal>) {
  return <SelectPrimitive.Portal data-slot="select-portal" {...props} />;
}

export function SelectPositioner({ className, ...props }: React.ComponentProps<typeof SelectPrimitive.Positioner>) {
  return <SelectPrimitive.Positioner data-slot="select-positioner" className={cn('z-[60]', className)} {...props} />;
}

export function SelectValue({ placeholder, ...props }: any) {
  if (!placeholder) {
    return <SelectPrimitive.Value data-slot="select-value" {...props} />;
  }
  return (
    <SelectPrimitive.Value
      render={(_, { value }) => {
        if (value) return <SelectPrimitive.Value data-slot="select-value" {...props} />;
        return <span data-slot="select-value" className="text-mauve-11 text-sm">{placeholder}</span>;
      }}
      {...props}
    />
  );
}

export function SelectClear({ className, children, onClick, ...props }: React.ComponentProps<'button'>) {
  const handleClick = (event: React.MouseEvent<HTMLButtonElement>) => {
    event.preventDefault();
    event.stopPropagation();
    onClick?.(event);
  };
  return (
    <button data-slot="select-clear" className={cn('cursor-pointer absolute top-1/2 -translate-y-1/2 end-2 rounded-sm opacity-60 hover:opacity-100 transition-opacity', className)} onClick={handleClick} {...props}>
      {children ? children : <X className="size-4" />}
    </button>
  );
}

const selectTriggerVariants = cva(
  'group relative flex w-full items-center justify-between gap-2 rounded-md border border-mauve-6 bg-mauve-1 text-mauve-12 shadow-xs transition-[color,box-shadow,border-color] outline-none focus-visible:border-mauve-8 focus-visible:ring-[3px] focus-visible:ring-mauve-4 aria-invalid:border-red-8 aria-invalid:ring-[3px] aria-invalid:ring-red-4 data-[disabled]:pointer-events-none data-[disabled]:opacity-60 *:data-[slot=select-value]:line-clamp-1',
  {
    variants: {
      size: {
        xs: 'h-7 px-2 text-xs gap-1 rounded-md [&_[data-slot=select-icon]]:size-3.5',
        sm: 'h-8 px-2.5 text-xs gap-1 rounded-md [&_[data-slot=select-icon]]:size-3.5',
        md: 'h-9 px-3 text-sm gap-1 rounded-md [&_[data-slot=select-icon]]:size-4',
        lg: 'h-10 px-4 text-sm gap-1.5 rounded-md [&_[data-slot=select-icon]]:size-4',
      },
    },
    defaultVariants: { size: 'md' },
  }
);

export function SelectTrigger({ className, children, size, ...props }: any) {
  const { icon } = React.useContext(SelectContext);
  return (
    <SelectPrimitive.Trigger className={cn(selectTriggerVariants({ size }), className)} {...props}>
      {children}
      <SelectPrimitive.Icon>
        {icon && isValidElement(icon) ? icon : <ChevronDown data-slot="select-icon" className="opacity-60 transition-transform duration-200" />}
      </SelectPrimitive.Icon>
    </SelectPrimitive.Trigger>
  );
}

export function SelectContent({ className, children, side = 'bottom', sideOffset = 2, align = 'start', alignOffset = 0, position = 'popper', ...props }: any) {
  return (
    <SelectPortal>
      <SelectPositioner sideOffset={sideOffset} alignItemWithTrigger={position === 'item-aligned'} side={side} align={align} alignOffset={alignOffset}>
        <SelectPrimitive.Popup className={cn('relative max-h-(--available-height) min-w-(--anchor-width) overflow-y-auto bg-mauve-2 text-mauve-12 rounded-md border border-mauve-6 p-1 shadow-md data-[open]:animate-in data-[closed]:animate-out', className)} {...props}>
          {children}
        </SelectPrimitive.Popup>
      </SelectPositioner>
    </SelectPortal>
  );
}

export function SelectItem({ className, children, ...props }: any) {
  const { indicatorPosition, indicatorVisibility, indicator } = React.useContext(SelectContext);
  return (
    <SelectPrimitive.Item className={cn('relative flex w-full items-center cursor-default select-none rounded-sm py-1.5 text-sm outline-hidden text-mauve-12 data-highlighted:bg-mauve-4', indicatorPosition === 'left' ? 'ps-7 pe-2' : 'pe-7 ps-2', className)} {...props}>
      {indicatorVisibility && (
        <span className={cn('absolute flex h-3.5 w-3.5 items-center justify-center', indicatorPosition === 'left' ? 'start-2' : 'end-2')}>
          <SelectPrimitive.ItemIndicator>
            {indicator && isValidElement(indicator) ? indicator : <Check className="h-4 w-4 text-blue-9" />}
          </SelectPrimitive.ItemIndicator>
        </span>
      )}
      <SelectPrimitive.ItemText>{children}</SelectPrimitive.ItemText>
    </SelectPrimitive.Item>
  );
}
```

---

### 📂 2.4 Componente `Accordion` (`/packages/ui/src/components/accordion.tsx`)

Animações de entrada e saída automáticas para altura de blocos utilizando o motor CSS do Tailwind e Base UI.

```tsx
import { Accordion as AccordionPrimitive } from '@base-ui/react/accordion';
import { cn } from '@manager/lib';
import { ChevronDown, ChevronUp } from '@untitled-ui/icons-react';

export function Accordion({ className, ...props }: AccordionPrimitive.Root.Props) {
  return <AccordionPrimitive.Root className={cn('flex w-full flex-col', className)} {...props} />;
}

export function AccordionItem({ className, ...props }: AccordionPrimitive.Item.Props) {
  return <AccordionPrimitive.Item className={cn('not-last:border-b border-slate-6', className)} {...props} />;
}

export function AccordionTrigger({ className, children, ...props }: AccordionPrimitive.Trigger.Props) {
  return (
    <AccordionPrimitive.Header className="flex">
      <AccordionPrimitive.Trigger className={cn('focus-visible:ring-ring rounded-md py-4 text-left text-sm font-medium hover:underline group/accordion-trigger relative flex flex-1 items-start justify-between transition-all outline-none', className)} {...props}>
        {children}
        <ChevronDown className="pointer-events-none shrink-0 size-4 group-aria-expanded/accordion-trigger:hidden text-slate-11" />
        <ChevronUp className="pointer-events-none shrink-0 size-4 hidden group-aria-expanded/accordion-trigger:inline text-slate-11" />
      </AccordionPrimitive.Trigger>
    </AccordionPrimitive.Header>
  );
}

export function AccordionContent({ className, children, ...props }: AccordionPrimitive.Panel.Props) {
  return (
    <AccordionPrimitive.Panel className="data-open:animate-accordion-down data-closed:animate-accordion-up text-sm overflow-hidden" {...props}>
      <div className={cn('pt-0 pb-4 h-(--accordion-panel-height) data-ending-style:h-0 data-starting-style:h-0 text-slate-11', className)}>
        {children}
      </div>
    </AccordionPrimitive.Panel>
  );
}
```

---

### 📂 2.5 Componente `Input` (`/packages/ui/src/components/input.tsx`)

```tsx
import { Input as InputPrimitive } from '@base-ui/react/input';
import { cn } from '@manager/lib';
import type * as React from 'react';

export function Input({ className, type, ...props }: React.ComponentProps<'input'>) {
  return (
    <InputPrimitive
      type={type}
      className={cn(
        'relative block w-full bg-mauve-1 text-mauve-12 px-3.5 py-2 placeholder-mauve-9 outline-hidden sm:px-3 sm:py-1.5 sm:text-sm/6',
        'h-9 rounded-md border border-mauve-6 shadow-xs transition-[color,box-shadow]',
        'focus-visible:border-mauve-8 focus-visible:ring-[3px] focus-visible:ring-mauve-4',
        'aria-invalid:border-red-8 aria-invalid:ring-[3px] aria-invalid:ring-red-4',
        'disabled:pointer-events-none disabled:opacity-50',
        className,
      )}
      {...props}
    />
  );
}
```

---

### 📂 2.6 Componente `Textarea` (`/packages/ui/src/components/textarea.tsx`)

```tsx
import { cn } from '@manager/lib';
import type * as React from 'react';

export function Textarea({ className, ...props }: React.ComponentProps<'textarea'>) {
  return (
    <textarea
      className={cn(
        'border-mauve-6 bg-mauve-1 text-mauve-12 rounded-md border px-2.5 py-2 text-base shadow-xs transition-[color,box-shadow] focus-visible:ring-[3px] focus-visible:ring-mauve-4 focus-visible:border-mauve-8 aria-invalid:ring-[3px] aria-invalid:ring-red-4 aria-invalid:border-red-8 md:text-sm placeholder:text-mauve-9 flex field-sizing-content min-h-16 w-full outline-none disabled:cursor-not-allowed disabled:opacity-50',
        className,
      )}
      {...props}
    />
  );
}
```

---

### 📂 2.7 Componente `Card` (`/packages/ui/src/components/card.tsx`)

```tsx
import { cn } from '@manager/lib';
import type * as React from 'react';

export function Card({ className, size = 'default', ...props }: React.ComponentProps<'div'> & { size?: 'default' | 'sm' }) {
  return (
    <div data-slot="card" data-size={size} className={cn('bg-slate-3 text-slate-12 gap-6 overflow-hidden rounded-xl border border-slate-6 p-6 text-sm shadow-sm flex flex-col', className)} {...props} />
  );
}

export function CardHeader({ className, ...props }: React.ComponentProps<'div'>) {
  return <div data-slot="card-header" className={cn('gap-1 flex flex-col', className)} {...props} />;
}

export function CardTitle({ className, ...props }: React.ComponentProps<'div'>) {
  return <div data-slot="card-title" className={cn('text-base leading-normal font-medium text-slate-12', className)} {...props} />;
}

export function CardDescription({ className, ...props }: React.ComponentProps<'div'>) {
  return <div data-slot="card-description" className={cn('text-slate-11 text-sm', className)} {...props} />;
}

export function CardContent({ className, ...props }: React.ComponentProps<'div'>) {
  return <div data-slot="card-content" className={cn('flex-1 text-slate-11', className)} {...props} />;
}

export function CardFooter({ className, ...props }: React.ComponentProps<'div'>) {
  return <div data-slot="card-footer" className={cn('flex items-center gap-2 mt-auto', className)} {...props} />;
}
```

---

### 📂 2.8 Componente `Badge` (`/packages/ui/src/components/badge.tsx`)

```tsx
import { mergeProps } from '@base-ui/react/merge-props';
import { useRender } from '@base-ui/react/use-render';
import { type VariantProps, cva } from 'class-variance-authority';
import type * as React from 'react';
import { cn } from '@manager/lib';

const badgeVariants = cva(
  'inline-flex items-center px-2 py-0.5 text-xs font-semibold gap-1.5 border transition-colors w-fit whitespace-nowrap shrink-0',
  {
    variants: {
      variant: {
        default: 'border-transparent bg-mauve-11 text-mauve-1',
        secondary: 'bg-mauve-3 text-mauve-12 border border-mauve-5',
        danger: 'border-transparent bg-red-4 text-red-11 border border-red-7',
        warning: 'border-transparent bg-amber-4 text-amber-11 border border-amber-7',
        success: 'border-transparent bg-green-3 text-green-11 border border-green-7',
        info: 'border-transparent bg-blue-4 text-blue-11 border border-blue-7',
        outline: 'text-mauve-11 border-mauve-7 bg-transparent',
      },
      radii: {
        full: 'rounded-full',
        md: 'rounded-md',
        none: 'rounded-none',
      },
    },
    defaultVariants: { variant: 'default', radii: 'full' },
  }
);

export function Badge({ className, variant = 'default', radii = 'full', render, ...props }: any) {
  return useRender({
    defaultTagName: 'span',
    props: mergeProps({ className: cn(badgeVariants({ variant, radii }), className) }, props),
    render,
  });
}
```

---

---

### 📂 2.9 Componente `FormField` (Padrão para `react-hook-form`)

Wrapper que unifica label + input + mensagem de erro, eliminando a repetição em todos os formulários:

```tsx
import { cn } from '@manager/lib';
import type * as React from 'react';

interface FormFieldProps {
  label: string;
  error?: string;
  required?: boolean;
  children: React.ReactNode;
  className?: string;
}

export function FormField({ label, error, required, children, className }: FormFieldProps) {
  return (
    <div className={cn('flex flex-col gap-1.5', className)}>
      <label className="text-xs font-semibold text-slate-12">
        {label}
        {required && <span className="text-red-9 ml-0.5">*</span>}
      </label>
      {children}
      {error && (
        <p className="text-xs text-red-11" role="alert">
          {error}
        </p>
      )}
    </div>
  );
}
```

Uso com `react-hook-form`:
```tsx
<FormField label="Nome" error={errors.name?.message} required>
  <Input {...register('name')} aria-invalid={!!errors.name} />
</FormField>
```

---

## ⚠️ Notas Técnicas Importantes

### Tipos `any` no componente `Modal`
Os sub-componentes do `Modal` (ex: `ModalBackdrop`, `ModalContainer`, `ModalDialog`) usam `any` internamente para simplificar a composição. Em projetos que exigem TypeScript estrito (`strict: true`), substitua os `any` pelos tipos derivados dos primitivos do Base UI (`Dialog.Root.Props`, `Dialog.Popup.Props`, etc.) ao estender esses componentes.

### `asChild` no `Button`
O `Button` aceita a prop `asChild` para compatibilidade interna com casos de edge (ex: `<Button asChild><Link /></Button>`). Para consumo normal, prefira sempre `render={<a />}` ou `render={<RouterLink />}`, que é o padrão do Base UI e não ativa o caminho de código do `asChild`.

---

## 🛠️ 3. Instalação e uso em novos projetos

Ao criar uma nova interface AT5, sempre inicie importando os componentes exportados de `@manager/ui`. A IA ou o desenvolvedor devem priorizar o reaproveitamento destes componentes para manter a conformidade do design system, acessibilidade garantida e evitar bundle size redundante com bibliotecas externas de terceiros.
