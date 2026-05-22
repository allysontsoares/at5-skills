# 📦 Inputs & Form Elements

Campos de entrada de texto, seleção, inputs de OTP, caixas de seleção, botões de rádio e utilitários de formulário estruturado.

---

## 📂 `input.tsx`

Caminho original no repositório: `/packages/ui/src/components/input.tsx`

```tsx
import { Input as InputPrimitive } from '@base-ui/react/input';
import type * as React from 'react';

import { cn } from '@manager/lib';

function Input({ className, type, ...props }: React.ComponentProps<'input'>) {
  return (
    <InputPrimitive
      type={type}
      data-slot="input"
      className={cn(
        'relative block w-full bg-mauve-1 text-mauve-12 px-3.5 py-2 placeholder-mauve-9 outline-hidden sm:px-3 sm:py-1.5 sm:text-sm/6 [&::-ms-reveal]:hidden [&::-webkit-search-cancel-button]:hidden',
        'h-9 rounded-md border border-mauve-6 shadow-xs transition-[color,box-shadow]',
        'focus-visible:border-mauve-8 focus-visible:ring-[3px] focus-visible:ring-mauve-4',
        'aria-invalid:border-red-8 aria-invalid:ring-[3px] aria-invalid:ring-red-4',
        'disabled:pointer-events-none disabled:cursor-not-allowed disabled:opacity-50',
        'file:inline-flex file:h-7 file:border-0 file:bg-transparent file:text-sm file:font-medium file:text-mauve-12',
        className,
      )}
      {...props}
    />
  );
}

export { Input };
```

---

## 📂 `textarea.tsx`

Caminho original no repositório: `/packages/ui/src/components/textarea.tsx`

```tsx
import type * as React from 'react';

import { cn } from '@manager/lib';

function Textarea({ className, ...props }: React.ComponentProps<'textarea'>) {
  return (
    <textarea
      data-slot="textarea"
      className={cn(
        'border-zinc-200 dark:bg-zinc-200/30 focus-visible:border-zinc-950 focus-visible:ring-zinc-950/50 aria-invalid:ring-red-500/20 dark:aria-invalid:ring-red-500/40 aria-invalid:border-red-500 dark:aria-invalid:border-red-500/50 rounded-md border bg-transparent px-2.5 py-2 text-base shadow-xs transition-[color,box-shadow] focus-visible:ring-[3px] aria-invalid:ring-[3px] md:text-sm placeholder:text-zinc-500 flex field-sizing-content min-h-16 w-full outline-none disabled:cursor-not-allowed disabled:opacity-50 dark:border-zinc-800 dark:dark:bg-zinc-800/30 dark:focus-visible:border-zinc-300 dark:focus-visible:ring-zinc-300/50 dark:aria-invalid:ring-red-900/20 dark:dark:aria-invalid:ring-red-900/40 dark:aria-invalid:border-red-900 dark:dark:aria-invalid:border-red-900/50 dark:placeholder:text-zinc-400',
        className,
      )}
      {...props}
    />
  );
}

export { Textarea };
```

---

## 📂 `checkbox.tsx`

Caminho original no repositório: `/packages/ui/src/components/checkbox.tsx`

```tsx
import { Checkbox as CheckboxPrimitive } from '@base-ui/react/checkbox';

import { cn } from '@manager/lib';
import { Check } from '@untitled-ui/icons-react';

function Checkbox({ className, ...props }: CheckboxPrimitive.Root.Props) {
  return (
    <CheckboxPrimitive.Root
      data-slot="checkbox"
      className={cn(
        'border-zinc-200 dark:bg-zinc-200/30 data-checked:bg-zinc-900 data-checked:text-zinc-50 dark:data-checked:bg-zinc-900 data-checked:border-zinc-900 aria-invalid:aria-checked:border-zinc-900 aria-invalid:border-red-500 dark:aria-invalid:border-red-500/50 focus-visible:border-zinc-950 focus-visible:ring-zinc-950/50 aria-invalid:ring-red-500/20 dark:aria-invalid:ring-red-500/40 flex size-4 items-center justify-center rounded-[4px] border shadow-xs transition-shadow group-has-disabled/field:opacity-50 focus-visible:ring-[3px] aria-invalid:ring-[3px] peer relative shrink-0 outline-none after:absolute after:-inset-x-3 after:-inset-y-2 disabled:cursor-not-allowed disabled:opacity-50 dark:border-zinc-800 dark:dark:bg-zinc-800/30 dark:data-checked:bg-zinc-50 dark:data-checked:text-zinc-900 dark:dark:data-checked:bg-zinc-50 dark:data-checked:border-zinc-50 dark:aria-invalid:aria-checked:border-zinc-50 dark:aria-invalid:border-red-900 dark:dark:aria-invalid:border-red-900/50 dark:focus-visible:border-zinc-300 dark:focus-visible:ring-zinc-300/50 dark:aria-invalid:ring-red-900/20 dark:dark:aria-invalid:ring-red-900/40',
        className,
      )}
      {...props}
    >
      <CheckboxPrimitive.Indicator
        data-slot="checkbox-indicator"
        className="[&>svg]:size-3.5 grid place-content-center text-current transition-none"
      >
        <Check strokeWidth={2} />
      </CheckboxPrimitive.Indicator>
    </CheckboxPrimitive.Root>
  );
}

export { Checkbox };
```

---

## 📂 `switch.tsx`

Caminho original no repositório: `/packages/ui/src/components/switch.tsx`

```tsx
import { Switch as SwitchPrimitive } from '@base-ui/react/switch';

import { cn } from '@manager/lib';

function Switch({
  className,
  size = 'default',
  ...props
}: SwitchPrimitive.Root.Props & {
  size?: 'sm' | 'default';
}) {
  return (
    <SwitchPrimitive.Root
      data-slot="switch"
      data-size={size}
      className={cn(
        'data-checked:bg-zinc-900 data-unchecked:bg-zinc-200 focus-visible:border-zinc-950 focus-visible:ring-zinc-950/50 aria-invalid:ring-red-500/20 dark:aria-invalid:ring-red-500/40 aria-invalid:border-red-500 dark:aria-invalid:border-red-500/50 dark:data-unchecked:bg-zinc-200/80 shrink-0 rounded-full border border-zinc-200 border-transparent shadow-xs focus-visible:ring-[3px] aria-invalid:ring-[3px] data-[size=default]:h-[18.4px] data-[size=default]:w-[32px] data-[size=sm]:h-[14px] data-[size=sm]:w-[24px] peer group/switch relative inline-flex items-center transition-all outline-none after:absolute after:-inset-x-3 after:-inset-y-2 data-disabled:cursor-not-allowed data-disabled:opacity-50 dark:data-checked:bg-zinc-50 dark:data-unchecked:bg-zinc-800 dark:focus-visible:border-zinc-300 dark:focus-visible:ring-zinc-300/50 dark:aria-invalid:ring-red-900/20 dark:dark:aria-invalid:ring-red-900/40 dark:aria-invalid:border-red-900 dark:dark:aria-invalid:border-red-900/50 dark:dark:data-unchecked:bg-zinc-800/80 dark:border-zinc-800',
        className,
      )}
      {...props}
    >
      <SwitchPrimitive.Thumb
        data-slot="switch-thumb"
        className="bg-white dark:data-unchecked:bg-zinc-950 dark:data-checked:bg-zinc-50 rounded-full group-data-[size=default]/switch:size-4 group-data-[size=sm]/switch:size-3 group-data-[size=default]/switch:data-checked:translate-x-[calc(100%-2px)] group-data-[size=sm]/switch:data-checked:translate-x-[calc(100%-2px)] group-data-[size=default]/switch:data-unchecked:translate-x-0 group-data-[size=sm]/switch:data-unchecked:translate-x-0 pointer-events-none block ring-0 transition-transform dark:bg-zinc-950 dark:dark:data-unchecked:bg-zinc-50 dark:dark:data-checked:bg-zinc-900"
      />
    </SwitchPrimitive.Root>
  );
}

export { Switch };
```

---

## 📂 `radio-group.tsx`

Caminho original no repositório: `/packages/ui/src/components/radio-group.tsx`

```tsx
import { Radio as RadioPrimitive } from '@base-ui/react/radio';
import { RadioGroup as RadioGroupPrimitive } from '@base-ui/react/radio-group';

import { cn } from '@manager/lib';
import { Circle } from '@untitled-ui/icons-react';

function RadioGroup({ className, ...props }: RadioGroupPrimitive.Props) {
  return (
    <RadioGroupPrimitive
      data-slot="radio-group"
      className={cn('grid gap-3 w-full', className)}
      {...props}
    />
  );
}

function RadioGroupItem({ className, ...props }: RadioPrimitive.Root.Props) {
  return (
    <RadioPrimitive.Root
      data-slot="radio-group-item"
      className={cn(
        'border-zinc-200 text-zinc-900 dark:bg-zinc-200/30 focus-visible:border-zinc-950 focus-visible:ring-zinc-950/50 aria-invalid:ring-red-500/20 dark:aria-invalid:ring-red-500/40 aria-invalid:border-red-500 dark:aria-invalid:border-red-500/50 flex size-4 rounded-full shadow-xs focus-visible:ring-[3px] aria-invalid:ring-[3px] group/radio-group-item peer relative aspect-square shrink-0 border outline-none after:absolute after:-inset-x-3 after:-inset-y-2 disabled:cursor-not-allowed disabled:opacity-50 dark:border-zinc-800 dark:text-zinc-50 dark:dark:bg-zinc-800/30 dark:focus-visible:border-zinc-300 dark:focus-visible:ring-zinc-300/50 dark:aria-invalid:ring-red-900/20 dark:dark:aria-invalid:ring-red-900/40 dark:aria-invalid:border-red-900 dark:dark:aria-invalid:border-red-900/50',
        className,
      )}
      {...props}
    >
      <RadioPrimitive.Indicator
        data-slot="radio-group-indicator"
        className="group-aria-invalid/radio-group-item:text-destructive text-zinc-900 flex size-4 items-center justify-center dark:text-zinc-50"
      >
        <Circle className="absolute top-1/2 left-1/2 size-2 -translate-x-1/2 -translate-y-1/2 fill-current" />
      </RadioPrimitive.Indicator>
    </RadioPrimitive.Root>
  );
}

export { RadioGroup, RadioGroupItem };
```

---

## 📂 `input-group.tsx`

Caminho original no repositório: `/packages/ui/src/components/input-group.tsx`

```tsx
'use client';

import { type VariantProps, cva } from 'class-variance-authority';
import type * as React from 'react';

import { cn } from '@manager/lib';
import { Button } from './button';
import { Input } from './input';
import { Textarea } from './textarea';

function InputGroup({ className, ...props }: React.ComponentProps<'div'>) {
  return (
    <div
      data-slot="input-group"
      role="group"
      className={cn(
        'border-zinc-200 dark:bg-zinc-200/30 has-[[data-slot=input-group-control]:focus-visible]:border-zinc-950 has-[[data-slot=input-group-control]:focus-visible]:ring-zinc-950/50 has-[[data-slot][aria-invalid=true]]:ring-red-500/20 has-[[data-slot][aria-invalid=true]]:border-red-500 dark:has-[[data-slot][aria-invalid=true]]:ring-red-500/40 h-9 rounded-md border shadow-xs transition-[color,box-shadow] has-[[data-slot=input-group-control]:focus-visible]:ring-[3px] has-[[data-slot][aria-invalid=true]]:ring-[3px] has-[>[data-align=block-end]]:h-auto has-[>[data-align=block-end]]:flex-col has-[>[data-align=block-start]]:h-auto has-[>[data-align=block-start]]:flex-col has-[>[data-align=block-end]]:[&>input]:pt-3 has-[>[data-align=block-start]]:[&>input]:pb-3 has-[>[data-align=inline-end]]:[&>input]:pr-1.5 has-[>[data-align=inline-start]]:[&>input]:pl-1.5 [[data-slot=combobox-content]_&]:focus-within:border-inherit [[data-slot=combobox-content]_&]:focus-within:ring-0 group/input-group relative flex w-full min-w-0 items-center outline-none has-[>textarea]:h-auto dark:border-zinc-800 dark:dark:bg-zinc-800/30 dark:has-[[data-slot=input-group-control]:focus-visible]:border-zinc-300 dark:has-[[data-slot=input-group-control]:focus-visible]:ring-zinc-300/50 dark:has-[[data-slot][aria-invalid=true]]:ring-red-900/20 dark:has-[[data-slot][aria-invalid=true]]:border-red-900 dark:dark:has-[[data-slot][aria-invalid=true]]:ring-red-900/40',
        className,
      )}
      {...props}
    />
  );
}

const inputGroupAddonVariants = cva(
  "text-zinc-500 h-auto gap-2 py-1.5 text-sm font-medium group-data-[disabled=true]/input-group:opacity-50 [&>kbd]:rounded-[calc(var(--radius)-5px)] [&>svg:not([class*='size-'])]:size-4 flex cursor-text items-center justify-center select-none dark:text-zinc-400",
  {
    variants: {
      align: {
        'inline-start': 'pl-2 has-[>button]:ml-[-0.25rem] has-[>kbd]:ml-[-0.15rem] order-first',
        'inline-end': 'pr-2 has-[>button]:mr-[-0.25rem] has-[>kbd]:mr-[-0.15rem] order-last',
        'block-start':
          'px-2.5 pt-2 group-has-[>input]/input-group:pt-2 [.border-b]:pb-2 order-first w-full justify-start',
        'block-end':
          'px-2.5 pb-2 group-has-[>input]/input-group:pb-2 [.border-t]:pt-2 order-last w-full justify-start',
      },
    },
    defaultVariants: {
      align: 'inline-start',
    },
  },
);

function InputGroupAddon({
  className,
  align = 'inline-start',
  ...props
}: React.ComponentProps<'div'> & VariantProps<typeof inputGroupAddonVariants>) {
  return (
    <div
      role="group"
      data-slot="input-group-addon"
      data-align={align}
      className={cn(inputGroupAddonVariants({ align }), className)}
      onClick={(e) => {
        if ((e.target as HTMLElement).closest('button')) {
          return;
        }
        e.currentTarget.parentElement?.querySelector('input')?.focus();
      }}
      {...props}
    />
  );
}

const inputGroupButtonVariants = cva('gap-2 text-sm shadow-none flex items-center', {
  variants: {
    size: {
      xs: "h-6 gap-1 rounded-[calc(var(--radius)-5px)] px-1.5 [&>svg:not([class*='size-'])]:size-3.5",
      sm: '',
      'icon-xs': 'size-6 rounded-[calc(var(--radius)-5px)] p-0 has-[>svg]:p-0',
      'icon-sm': 'size-8 p-0 has-[>svg]:p-0',
    },
  },
  defaultVariants: {
    size: 'xs',
  },
});

function InputGroupButton({
  className,
  type = 'button',
  variant = 'ghost',
  size = 'xs',
  ...props
}: Omit<React.ComponentProps<typeof Button>, 'size' | 'type'> &
  VariantProps<typeof inputGroupButtonVariants> & {
    type?: 'button' | 'submit' | 'reset';
  }) {
  return (
    <Button
      type={type}
      data-size={size}
      variant={variant}
      className={cn(inputGroupButtonVariants({ size }), className)}
      {...props}
    />
  );
}

function InputGroupText({ className, ...props }: React.ComponentProps<'span'>) {
  return (
    <span
      className={cn(
        "text-zinc-500 gap-2 text-sm [&_svg:not([class*='size-'])]:size-4 flex items-center [&_svg]:pointer-events-none dark:text-zinc-400",
        className,
      )}
      {...props}
    />
  );
}

function InputGroupInput({ className, ...props }: React.ComponentProps<'input'>) {
  return (
    <Input
      data-slot="input-group-control"
      className={cn(
        'rounded-none border-0 bg-transparent shadow-none ring-0 focus-visible:ring-0 aria-invalid:ring-0 dark:bg-transparent flex-1',
        className,
      )}
      {...props}
    />
  );
}

function InputGroupTextarea({ className, ...props }: React.ComponentProps<'textarea'>) {
  return (
    <Textarea
      data-slot="input-group-control"
      className={cn(
        'rounded-none border-0 bg-transparent py-2 shadow-none ring-0 focus-visible:ring-0 aria-invalid:ring-0 dark:bg-transparent flex-1 resize-none',
        className,
      )}
      {...props}
    />
  );
}

export {
  InputGroup,
  InputGroupAddon,
  InputGroupButton,
  InputGroupText,
  InputGroupInput,
  InputGroupTextarea,
};
```

---

## 📂 `input-otp.tsx`

Caminho original no repositório: `/packages/ui/src/components/input-otp.tsx`

```tsx
import { OTPInput, OTPInputContext } from 'input-otp';
import * as React from 'react';

import { cn } from '@manager/lib';
import { Minus } from '@untitled-ui/icons-react';

function InputOTP({
  className,
  containerClassName,
  ...props
}: React.ComponentProps<typeof OTPInput> & {
  containerClassName?: string;
}) {
  return (
    <OTPInput
      data-slot="input-otp"
      containerClassName={cn(
        'cn-input-otp flex items-center has-disabled:opacity-50',
        containerClassName,
      )}
      spellCheck={false}
      className={cn('disabled:cursor-not-allowed', className)}
      {...props}
    />
  );
}

function InputOTPGroup({ className, ...props }: React.ComponentProps<'div'>) {
  return (
    <div
      data-slot="input-otp-group"
      className={cn(
        'has-aria-invalid:ring-red-500/20 dark:has-aria-invalid:ring-red-500/40 has-aria-invalid:border-red-500 rounded-md has-aria-invalid:ring-[3px] flex items-center dark:has-aria-invalid:ring-red-900/20 dark:dark:has-aria-invalid:ring-red-900/40 dark:has-aria-invalid:border-red-900',
        className,
      )}
      {...props}
    />
  );
}

function InputOTPSlot({
  index,
  className,
  ...props
}: React.ComponentProps<'div'> & {
  index: number;
}) {
  const inputOTPContext = React.useContext(OTPInputContext);
  const { char, hasFakeCaret, isActive } = inputOTPContext?.slots[index] ?? {};

  return (
    <div
      data-slot="input-otp-slot"
      data-active={isActive}
      className={cn(
        'dark:bg-zinc-200/30 border-zinc-200 data-[active=true]:border-zinc-950 data-[active=true]:ring-zinc-950/50 data-[active=true]:aria-invalid:ring-red-500/20 dark:data-[active=true]:aria-invalid:ring-red-500/40 aria-invalid:border-red-500 data-[active=true]:aria-invalid:border-red-500 size-9 border-y border-r text-sm shadow-xs transition-all outline-none first:rounded-l-md first:border-l last:rounded-r-md data-[active=true]:ring-[3px] relative flex items-center justify-center data-[active=true]:z-10 dark:dark:bg-zinc-800/30 dark:border-zinc-800 dark:data-[active=true]:border-zinc-300 dark:data-[active=true]:ring-zinc-300/50 dark:data-[active=true]:aria-invalid:ring-red-900/20 dark:dark:data-[active=true]:aria-invalid:ring-red-900/40 dark:aria-invalid:border-red-900 dark:data-[active=true]:aria-invalid:border-red-900',
        className,
      )}
      {...props}
    >
      {char}
      {hasFakeCaret && (
        <div className="pointer-events-none absolute inset-0 flex items-center justify-center">
          <div className="animate-caret-blink bg-zinc-950 h-4 w-px duration-1000 dark:bg-zinc-50" />
        </div>
      )}
    </div>
  );
}

function InputOTPSeparator({ ...props }: React.ComponentProps<'div'>) {
  return (
    <div
      data-slot="input-otp-separator"
      className="[&_svg:not([class*='size-'])]:size-4 flex items-center"
      role="separator"
      {...props}
    >
      <Minus />
    </div>
  );
}

export { InputOTP, InputOTPGroup, InputOTPSlot, InputOTPSeparator };
```

---

## 📂 `autocomplete.tsx`

Caminho original no repositório: `/packages/ui/src/components/autocomplete.tsx`

```tsx
import { Autocomplete as BaseAutocomplete } from '@base-ui/react/autocomplete';
import { cn } from '@manager/lib';
import { XIcon } from 'lucide-react';
import type * as React from 'react';

function Autocomplete({ ...props }: React.ComponentProps<typeof BaseAutocomplete.Root>) {
  return <BaseAutocomplete.Root data-slot="autocomplete" {...props} />;
}

function AutocompletePortal({ ...props }: React.ComponentProps<typeof BaseAutocomplete.Portal>) {
  return <BaseAutocomplete.Portal data-slot="autocomplete-portal" {...props} />;
}

function AutocompletePositioner({
  ...props
}: React.ComponentProps<typeof BaseAutocomplete.Positioner>) {
  return <BaseAutocomplete.Positioner data-slot="autocomplete-positioner" {...props} />;
}

function AutocompleteValue({ ...props }: React.ComponentProps<typeof BaseAutocomplete.Value>) {
  return <BaseAutocomplete.Value data-slot="autocomplete-value" {...props} />;
}

function AutocompleteTrigger({ ...props }: React.ComponentProps<typeof BaseAutocomplete.Trigger>) {
  return <BaseAutocomplete.Trigger data-slot="autocomplete-trigger" {...props} />;
}

function AutocompleteIcon({ ...props }: React.ComponentProps<typeof BaseAutocomplete.Icon>) {
  return <BaseAutocomplete.Icon data-slot="autocomplete-icon" {...props} />;
}

function AutocompleteClear({ ...props }: React.ComponentProps<typeof BaseAutocomplete.Clear>) {
  return <BaseAutocomplete.Clear data-slot="autocomplete-clear" {...props} />;
}

function AutocompleteInput({
  className,
  inputContainerClassName,
  showClear = false,
  ...props
}: React.ComponentProps<typeof BaseAutocomplete.Input> & {
  inputContainerClassName?: string;
  showClear?: boolean;
}) {
  return (
    <div
      className={cn(
        'group relative w-full has-[[data-slot=autocomplete-input][data-disabled]]:pointer-events-none has-[[data-slot=autocomplete-input][data-disabled]]:opacity-50',
        inputContainerClassName,
      )}
      data-slot="autocomplete-input-container"
    >
      <BaseAutocomplete.Input
        data-slot="autocomplete-input"
        className={cn(
          'flex h-9 w-full min-w-0 rounded-md px-3 text-sm',
          'bg-mauve-1 border border-mauve-6 shadow-xs',
          'text-mauve-12 placeholder:text-mauve-9',
          'transition-[color,box-shadow] outline-none',
          'focus-visible:border-mauve-8 focus-visible:ring-[3px] focus-visible:ring-mauve-4',
          'disabled:pointer-events-none disabled:cursor-not-allowed disabled:opacity-50',
          'aria-invalid:border-red-8 aria-invalid:ring-[3px] aria-invalid:ring-red-4',
          showClear && 'pr-8',
          className,
        )}
        {...props}
      />
      {showClear && (
        <AutocompleteClear
          data-slot="autocomplete-clear"
          className="text-mauve-11 absolute top-1/2 right-3 shrink-0 -translate-y-1/2 [&_svg]:shrink-0 cursor-pointer hover:text-mauve-12"
        >
          <XIcon className="size-4" />
        </AutocompleteClear>
      )}
    </div>
  );
}

function AutocompleteContent({
  className,
  children,
  sideOffset = 4,
  ...props
}: React.ComponentProps<typeof BaseAutocomplete.Popup> & {
  sideOffset?: BaseAutocomplete.Positioner.Props['sideOffset'];
}) {
  return (
    <AutocompletePortal>
      <AutocompletePositioner className="outline-none z-[70]" sideOffset={sideOffset}>
        <BaseAutocomplete.Popup
          data-slot="autocomplete-content"
          className={cn(
            'bg-mauve-1 text-mauve-12 border-mauve-6 relative z-[70] max-h-[min(var(--available-height),20rem)] min-w-[var(--anchor-width)] origin-[var(--transform-origin)] overflow-x-hidden overflow-y-auto overscroll-contain rounded-md border p-1 shadow-md transition-all data-ending-style:duration-0 data-starting-style:scale-98 data-starting-style:opacity-0',
            className,
          )}
          {...props}
        >
          {children}
        </BaseAutocomplete.Popup>
      </AutocompletePositioner>
    </AutocompletePortal>
  );
}

function AutocompleteItem({
  className,
  children,
  ...props
}: React.ComponentProps<typeof BaseAutocomplete.Item>) {
  return (
    <BaseAutocomplete.Item
      data-slot="autocomplete-item"
      className={cn(
        'data-highlighted:bg-mauve-3 data-highlighted:text-mauve-12 [&_svg:not([class*="text-"])]:text-mauve-11 relative flex w-full cursor-default items-center gap-2 rounded-sm px-2 py-1.5 text-sm outline-hidden select-none data-disabled:pointer-events-none data-disabled:opacity-50 [&_svg]:pointer-events-none [&_svg]:shrink-0 [&_svg:not([class*="size-"])]:size-4',
        className,
      )}
      {...props}
    >
      {children}
    </BaseAutocomplete.Item>
  );
}

function AutocompleteEmpty({
  className,
  ...props
}: React.ComponentProps<typeof BaseAutocomplete.Empty>) {
  return (
    <BaseAutocomplete.Empty
      data-slot="autocomplete-empty"
      className={cn('py-6 text-center text-sm empty:m-0 empty:p-0', className)}
      {...props}
    />
  );
}

function AutocompleteStatus({
  className,
  ...props
}: React.ComponentProps<typeof BaseAutocomplete.Status>) {
  return (
    <BaseAutocomplete.Status
      data-slot="autocomplete-status"
      className={cn('text-mauve-11 flex items-center gap-2 px-2 py-1.5 text-sm', className)}
      {...props}
    />
  );
}

function AutocompleteGroup({ ...props }: React.ComponentProps<typeof BaseAutocomplete.Group>) {
  return <BaseAutocomplete.Group data-slot="autocomplete-group" {...props} />;
}

function AutocompleteGroupLabel({
  className,
  ...props
}: React.ComponentProps<typeof BaseAutocomplete.GroupLabel>) {
  return (
    <BaseAutocomplete.GroupLabel
      data-slot="autocomplete-group-label"
      className={cn('text-mauve-11 px-2 py-1.5 text-xs font-medium', className)}
      {...props}
    />
  );
}

function AutocompleteSeparator({
  className,
  ...props
}: React.ComponentProps<typeof BaseAutocomplete.Separator>) {
  return (
    <BaseAutocomplete.Separator
      data-slot="autocomplete-separator"
      className={cn('bg-mauve-6 pointer-events-none -mx-1 my-1 h-px', className)}
      {...props}
    />
  );
}

function AutocompleteList(props: React.ComponentProps<typeof BaseAutocomplete.List>) {
  return <BaseAutocomplete.List data-slot="autocomplete-list" {...props} />;
}

function AutocompleteCollection(props: React.ComponentProps<typeof BaseAutocomplete.Collection>) {
  return <BaseAutocomplete.Collection data-slot="autocomplete-collection" {...props} />;
}

function AutocompleteRow({
  className,
  ...props
}: React.ComponentProps<typeof BaseAutocomplete.Row>) {
  return (
    <BaseAutocomplete.Row
      data-slot="autocomplete-row"
      className={cn('grid w-full scroll-my-1.5', className)}
      {...props}
    />
  );
}

export {
  Autocomplete,
  AutocompleteClear,
  AutocompleteCollection,
  AutocompleteContent,
  AutocompleteEmpty,
  AutocompleteGroup,
  AutocompleteGroupLabel,
  AutocompleteIcon,
  AutocompleteInput,
  AutocompleteItem,
  AutocompleteList,
  AutocompletePortal,
  AutocompletePositioner,
  AutocompleteRow,
  AutocompleteSeparator,
  AutocompleteStatus,
  AutocompleteTrigger,
  AutocompleteValue,
};
```

---

## 📂 `select.tsx`

Caminho original no repositório: `/packages/ui/src/components/select.tsx`

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

function Select<Value = string, Multiple extends boolean = false>({
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

function SelectGroup({ ...props }: React.ComponentProps<typeof SelectPrimitive.Group>) {
  return <SelectPrimitive.Group data-slot="select-group" {...props} />;
}

function SelectPortal({ ...props }: React.ComponentProps<typeof SelectPrimitive.Portal>) {
  return <SelectPrimitive.Portal data-slot="select-portal" {...props} />;
}

function SelectPositioner({
  className,
  ...props
}: React.ComponentProps<typeof SelectPrimitive.Positioner>) {
  return (
    <SelectPrimitive.Positioner
      data-slot="select-positioner"
      className={cn('z-[60]', className)}
      {...props}
    />
  );
}

function SelectValue({
  placeholder,
  ...props
}: React.ComponentProps<typeof SelectPrimitive.Value> & {
  placeholder?: string;
}) {
  if (!placeholder) {
    return <SelectPrimitive.Value data-slot="select-value" {...props} />;
  }

  return (
    <SelectPrimitive.Value
      className="text-sm"
      render={(_, { value }) => {
        if (value) {
          return <SelectPrimitive.Value data-slot="select-value" {...props} />;
        }

        return (
          <span data-slot="select-value" className="text-mauve-11">
            {placeholder}
          </span>
        );
      }}
      {...props}
    />
  );
}

function SelectClear({ className, children, onClick, ...props }: React.ComponentProps<'button'>) {
  const handleClick = (event: React.MouseEvent<HTMLButtonElement>) => {
    event.preventDefault();
    event.stopPropagation();
    onClick?.(event);
  };

  return (
    <button
      data-slot="select-clear"
      className={cn(
        `
					cursor-pointer absolute top-1/2 -translate-y-1/2 end-2
					rounded-sm opacity-60 hover:opacity-100 transition-opacity
					focus:outline-none focus:ring-0 focus:ring-offset-0
					disabled:pointer-events-none data-[disabled]:pointer-events-none
				`,
        className,
      )}
      onClick={handleClick}
      {...props}
    >
      {children ? children : <X />}
    </button>
  );
}

const selectTriggerVariants = cva(
  `
		group relative flex w-fit items-center justify-between gap-2 rounded-md border border-mauve-6 whitespace-nowrap select-none
		bg-mauve-1 text-mauve-12
		shadow-xs transition-[color,box-shadow,border-color] outline-none
		focus-visible:border-mauve-8 focus-visible:ring-[3px] focus-visible:ring-mauve-4
		aria-invalid:border-red-8 aria-invalid:ring-[3px] aria-invalid:ring-red-4
		data-[disabled]:pointer-events-none data-[disabled]:opacity-60
		*:data-[slot=select-value]:line-clamp-1 *:data-[slot=select-value]:flex *:data-[slot=select-value]:items-center *:data-[slot=select-value]:gap-2
		[&_svg]:pointer-events-none [&_svg]:shrink-0 [&_svg:not([class*='text-'])]:text-mauve-11
	`,
  {
    variants: {
      size: {
        xs: `
					h-7 px-2 text-xs gap-1 rounded-md
					[&_[data-slot=select-icon]]:size-3.5 [&_[data-slot=select-icon]]:-me-0.75
					[&_[data-slot=select-clear]>svg]:size-3 [&_[data-slot=select-clear]]:end-6
				`,
        sm: `
					h-8 px-2.5 text-xs gap-1 rounded-md
					[&_[data-slot=select-icon]]:size-3.5 [&_[data-slot=select-icon]]:-me-0.75
					[&_[data-slot=select-clear]>svg]:size-3 [&_[data-slot=select-clear]]:end-6
				`,
        md: `
					h-9 px-3 text-sm gap-1 rounded-md
					[&_[data-slot=select-icon]]:size-4 [&_[data-slot=select-icon]]:-me-1
					[&_[data-slot=select-clear]>svg]:size-3.5 [&_[data-slot=select-clear]]:end-7
				`,
        lg: `
					h-10 px-4 text-sm gap-1.5 rounded-md
					[&_[data-slot=select-icon]]:size-4 [&_[data-slot=select-icon]]:-me-1.25
					[&_[data-slot=select-clear]>svg]:size-3.5 [&_[data-slot=select-clear]]:end-8
				`,
      },
    },
    defaultVariants: {
      size: 'md',
    },
  },
);

export interface SelectTriggerProps
  extends React.ComponentProps<typeof SelectPrimitive.Trigger>,
    VariantProps<typeof selectTriggerVariants> {}

function SelectTrigger({ className, children, size, ...props }: SelectTriggerProps) {
  const { icon } = React.useContext(SelectContext);

  return (
    <SelectPrimitive.Trigger
      data-slot="select-trigger"
      className={cn(selectTriggerVariants({ size }), className)}
      {...props}
    >
      {children}
      <SelectPrimitive.Icon>
        {icon && isValidElement(icon) ? (
          icon
        ) : (
          <ChevronDown
            data-slot="select-icon"
            className="opacity-60 transition-transform duration-200"
          />
        )}
      </SelectPrimitive.Icon>
    </SelectPrimitive.Trigger>
  );
}

function SelectContent({
  className,
  children,
  side = 'bottom',
  sideOffset = 2,
  align = 'start',
  alignOffset = 0,
  position = 'popper',
  ...props
}: React.ComponentProps<typeof SelectPrimitive.Popup> & {
  sideOffset?: SelectPrimitive.Positioner.Props['sideOffset'];
  side?: SelectPrimitive.Positioner.Props['side'];
  align?: SelectPrimitive.Positioner.Props['align'];
  alignOffset?: SelectPrimitive.Positioner.Props['alignOffset'];
  position?: 'popper' | 'item-aligned';
}) {
  return (
    <SelectPortal>
      <SelectPositioner
        sideOffset={sideOffset}
        alignItemWithTrigger={position === 'item-aligned'}
        side={side}
        align={align}
        alignOffset={alignOffset}
      >
        <SelectScrollUpButton />
        <SelectPrimitive.Popup
          data-slot="select-content"
          className={cn(
            `
							relative max-h-(--available-height) min-w-(--anchor-width) overflow-x-hidden overflow-y-auto overscroll-contain
							bg-mauve-2 text-mauve-12
							rounded-md border border-mauve-6 p-1 shadow-md
							origin-[var(--transform-origin)]
							data-[open]:animate-in data-[closed]:animate-out
							data-[closed]:fade-out-0 data-[open]:fade-in-0
							data-[closed]:zoom-out-95 data-[open]:zoom-in-95
							data-[side=bottom]:slide-in-from-top-2 data-[side=left]:slide-in-from-right-2
							data-[side=right]:slide-in-from-left-2 data-[side=top]:slide-in-from-bottom-2
						`,
            position === 'item-aligned' &&
              '[&_*[data-slot=select-item]]:min-w-[var(--anchor-width)]',
            className,
          )}
          {...props}
        >
          {children}
        </SelectPrimitive.Popup>
        <SelectScrollDownButton />
      </SelectPositioner>
    </SelectPortal>
  );
}

function SelectItem({
  className,
  children,
  ...props
}: React.ComponentProps<typeof SelectPrimitive.Item>) {
  const { indicatorPosition, indicatorVisibility, indicator } = React.useContext(SelectContext);

  return (
    <SelectPrimitive.Item
      data-slot="select-item"
      className={cn(
        `
					relative flex w-full items-center cursor-default select-none
					rounded-sm py-1.5 text-sm outline-hidden text-mauve-12
					data-highlighted:bg-mauve-4 data-highlighted:text-mauve-12
					data-[disabled]:pointer-events-none data-[disabled]:opacity-50
					[&_svg]:pointer-events-none [&_svg]:shrink-0 [&_svg:not([class*='size-'])]:size-4 [&_svg:not([class*='text-'])]:text-mauve-11
					*:[span]:last:flex *:[span]:last:items-center *:[span]:last:gap-2
				`,
        indicatorPosition === 'left' ? 'ps-7 pe-2' : 'pe-7 ps-2',
        className,
      )}
      {...props}
    >
      {indicatorVisibility &&
        (indicator && isValidElement(indicator) ? (
          indicator
        ) : (
          <span
            className={cn(
              'absolute flex h-3.5 w-3.5 items-center justify-center',
              indicatorPosition === 'left' ? 'start-2' : 'end-2',
            )}
          >
            <SelectPrimitive.ItemIndicator data-slot="select-item-indicator">
              <Check className="h-4 w-4 text-blue-9" />
            </SelectPrimitive.ItemIndicator>
          </span>
        ))}
      <SelectPrimitive.ItemText data-slot="select-item-text">{children}</SelectPrimitive.ItemText>
    </SelectPrimitive.Item>
  );
}

function SelectLabel({
  className,
  ...props
}: React.ComponentProps<typeof SelectPrimitive.GroupLabel>) {
  const { indicatorPosition } = React.useContext(SelectContext);

  return (
    <SelectPrimitive.GroupLabel
      data-slot="select-label"
      className={cn(
        'text-mauve-11 py-1.5 text-xs font-medium',
        indicatorPosition === 'left' ? 'ps-7 pe-2' : 'pe-7 ps-2',
        className,
      )}
      {...props}
    />
  );
}

function SelectIndicator({
  children,
  className,
  ...props
}: React.ComponentPropsWithoutRef<'span'>) {
  const { indicatorPosition } = React.useContext(SelectContext);

  return (
    <span
      data-slot="select-indicator"
      className={cn(
        'absolute flex top-1/2 -translate-y-1/2 items-center justify-center',
        indicatorPosition === 'left' ? 'start-2' : 'end-2',
        className,
      )}
      {...props}
    >
      <SelectPrimitive.ItemIndicator>{children}</SelectPrimitive.ItemIndicator>
    </span>
  );
}

function SelectSeparator({
  className,
  ...props
}: React.ComponentProps<typeof SelectPrimitive.Separator>) {
  return (
    <SelectPrimitive.Separator
      data-slot="select-separator"
      className={cn('bg-mauve-6 pointer-events-none -mx-1 my-1 h-px', className)}
      {...props}
    />
  );
}

function SelectScrollUpButton({
  className,
  ...props
}: React.ComponentProps<typeof SelectPrimitive.ScrollUpArrow>) {
  return (
    <SelectPrimitive.ScrollUpArrow
      data-slot="select-scroll-up-button"
      className={cn(
        'fixed top-0 left-0 right-0 z-10 flex w-full cursor-default items-center justify-center rounded-t-md py-1 bg-mauve-2',
        className,
      )}
      {...props}
    >
      <ChevronUp className="size-4" />
    </SelectPrimitive.ScrollUpArrow>
  );
}

function SelectScrollDownButton({
  className,
  ...props
}: React.ComponentProps<typeof SelectPrimitive.ScrollDownArrow>) {
  return (
    <SelectPrimitive.ScrollDownArrow
      data-slot="select-scroll-down-button"
      className={cn(
        'fixed bottom-0 left-0 right-0 z-10 flex w-full cursor-default items-center justify-center rounded-b-md py-1 bg-mauve-2',
        className,
      )}
      {...props}
    >
      <ChevronDown className="size-4" />
    </SelectPrimitive.ScrollDownArrow>
  );
}

export {
  Select,
  SelectClear,
  SelectContent,
  SelectGroup,
  SelectIndicator,
  SelectItem,
  SelectLabel,
  SelectScrollDownButton,
  SelectScrollUpButton,
  SelectSeparator,
  SelectTrigger,
  SelectValue,
};
```

---

## 📂 `form.tsx`

Caminho original no repositório: `/packages/ui/src/components/form.tsx`

```tsx
'use client';

import { useRender } from '@base-ui/react/use-render';
import { cn } from '@manager/lib';
import React from 'react';
import {
  Controller,
  type ControllerProps,
  type FieldPath,
  type FieldValues,
  FormProvider,
  useFormContext,
  useFormState,
} from 'react-hook-form';
import { Label } from './label';

const Form = FormProvider;

type FormFieldContextValue<
  TFieldValues extends FieldValues = FieldValues,
  TName extends FieldPath<TFieldValues> = FieldPath<TFieldValues>,
> = {
  name: TName;
};

const FormFieldContext = React.createContext<FormFieldContextValue>({} as FormFieldContextValue);

const FormField = <
  TFieldValues extends FieldValues = FieldValues,
  TName extends FieldPath<TFieldValues> = FieldPath<TFieldValues>,
>({
  ...props
}: ControllerProps<TFieldValues, TName>) => {
  return (
    <FormFieldContext.Provider value={{ name: props.name }}>
      <Controller {...props} />
    </FormFieldContext.Provider>
  );
};

const useFormField = () => {
  const fieldContext = React.useContext(FormFieldContext);
  const itemContext = React.useContext(FormItemContext);
  const { getFieldState } = useFormContext();
  const formState = useFormState({ name: fieldContext.name });
  const fieldState = getFieldState(fieldContext.name, formState);

  if (!fieldContext) {
    throw new Error('useFormField should be used within <FormField>');
  }

  const { id } = itemContext;

  return {
    id,
    name: fieldContext.name,
    formItemId: `${id}-form-item`,
    formDescriptionId: `${id}-form-item-description`,
    formMessageId: `${id}-form-item-message`,
    ...fieldState,
  };
};

type FormItemContextValue = {
  id: string;
};

const FormItemContext = React.createContext<FormItemContextValue>({} as FormItemContextValue);

function FormItem({ className, ...props }: React.ComponentProps<'div'>) {
  const id = React.useId();

  return (
    <FormItemContext.Provider value={{ id }}>
      <div data-slot="form-item" className={cn('grid gap-2', className)} {...props} />
    </FormItemContext.Provider>
  );
}

function FormLabel({ className, ...props }: React.ComponentProps<typeof Label>) {
  const { error, formItemId } = useFormField();

  return (
    <Label
      data-slot="form-label"
      data-error={!!error}
      className={cn(
        'data-[error=true]:text-destructive peer-disabled:cursor-not-allowed peer-disabled:opacity-50',
        className,
      )}
      htmlFor={formItemId}
      {...props}
    />
  );
}

function FormControl({
  children = <div />,
}: {
  children?: useRender.RenderProp;
}) {
  const { error, formItemId, formDescriptionId, formMessageId } = useFormField();

  return useRender({
    render: children,
    props: {
      'data-slot': 'form-control',
      id: formItemId,
      'aria-describedby': !error ? `${formDescriptionId}` : `${formDescriptionId} ${formMessageId}`,
      'aria-invalid': !!error,
    },
  });
}

function FormDescription({ className, ...props }: React.ComponentProps<'p'>) {
  const { formDescriptionId } = useFormField();

  return (
    <p
      data-slot="form-description"
      id={formDescriptionId}
      className={cn('text-muted-foreground text-sm', className)}
      {...props}
    />
  );
}

function FormMessage({ className, ...props }: React.ComponentProps<'p'>) {
  const { error, formMessageId } = useFormField();
  const body = error ? String(error?.message ?? '') : props.children;

  if (!body) {
    return null;
  }

  return (
    <p
      data-slot="form-message"
      id={formMessageId}
      className={cn('text-destructive text-sm', className)}
      {...props}
    >
      {body}
    </p>
  );
}

export {
  Form,
  FormControl,
  FormDescription,
  FormField,
  FormItem,
  FormLabel,
  FormMessage,
  useFormField,
};
```

---

## 📂 `form-field.tsx`

Caminho original no repositório: `/packages/ui/src/components/form-field.tsx`

```tsx
import { cn } from '@manager/lib';
import type React from 'react';
import { Field } from './field';
import { FieldError } from './field';
import { FieldLabel } from './field';

interface FormFieldProps {
  label: string;
  required?: boolean;
  error?: { message?: string };
  children: React.ReactNode;
  className?: string;
  labelClassName?: string;
}

export function FormField({
  label,
  required = false,
  error,
  children,
  className,
  labelClassName,
}: FormFieldProps) {
  return (
    <Field className={cn('grid md:grid-cols-4 gap-4 items-center', className)}>
      <FieldLabel className={cn('lg:text-right font-medium', labelClassName)}>
        {label}
        {required && <span className="text-red-500 ml-1">*</span>}
      </FieldLabel>
      <div className="col-span-3">
        {children}
        {error && <FieldError>{error.message}</FieldError>}
      </div>
    </Field>
  );
}
```

---

## 📂 `field.tsx`

Caminho original no repositório: `/packages/ui/src/components/field.tsx`

```tsx
import { type VariantProps, cva } from 'class-variance-authority';
import { useMemo } from 'react';

import { cn } from '@manager/lib';
import { Label } from './label';
import { Separator } from './separator';

function FieldSet({ className, ...props }: React.ComponentProps<'fieldset'>) {
  return (
    <fieldset
      data-slot="field-set"
      className={cn(
        'gap-6 has-[>[data-slot=checkbox-group]]:gap-3 has-[>[data-slot=radio-group]]:gap-3 flex flex-col',
        className,
      )}
      {...props}
    />
  );
}

function FieldLegend({
  className,
  variant = 'legend',
  ...props
}: React.ComponentProps<'legend'> & { variant?: 'legend' | 'label' }) {
  return (
    <legend
      data-slot="field-legend"
      data-variant={variant}
      className={cn(
        'mb-3 font-medium data-[variant=label]:text-sm data-[variant=legend]:text-base',
        className,
      )}
      {...props}
    />
  );
}

function FieldGroup({ className, ...props }: React.ComponentProps<'div'>) {
  return (
    <div
      data-slot="field-group"
      className={cn(
        'gap-7 data-[slot=checkbox-group]:gap-3 [&>[data-slot=field-group]]:gap-4 group/field-group @container/field-group flex w-full flex-col',
        className,
      )}
      {...props}
    />
  );
}

const fieldVariants = cva(
  'data-[invalid=true]:text-red-500 gap-3 group/field flex w-full dark:data-[invalid=true]:text-red-900',
  {
    variants: {
      orientation: {
        vertical: 'flex-col [&>*]:w-full [&>.sr-only]:w-auto',
        horizontal:
          'flex-row items-center [&>[data-slot=field-label]]:flex-auto has-[>[data-slot=field-content]]:items-start has-[>[data-slot=field-content]]:[&>[role=checkbox],[role=radio]]:mt-px',
        responsive:
          'flex-col [&>*]:w-full [&>.sr-only]:w-auto @md/field-group:flex-row @md/field-group:items-center @md/field-group:[&>*]:w-auto @md/field-group:[&>[data-slot=field-label]]:flex-auto @md/field-group:has-[>[data-slot=field-content]]:items-start @md/field-group:has-[>[data-slot=field-content]]:[&>[role=checkbox],[role=radio]]:mt-px',
      },
    },
    defaultVariants: {
      orientation: 'vertical',
    },
  },
);

function Field({
  className,
  orientation = 'vertical',
  ...props
}: React.ComponentProps<'div'> & VariantProps<typeof fieldVariants>) {
  return (
    <div
      role="group"
      data-slot="field"
      data-orientation={orientation}
      className={cn(fieldVariants({ orientation }), className)}
      {...props}
    />
  );
}

function FieldContent({ className, ...props }: React.ComponentProps<'div'>) {
  return (
    <div
      data-slot="field-content"
      className={cn('gap-1 group/field-content flex flex-1 flex-col leading-snug', className)}
      {...props}
    />
  );
}

function FieldLabel({ className, ...props }: React.ComponentProps<typeof Label>) {
  return (
    <Label
      data-slot="field-label"
      className={cn(
        'text-xs has-data-checked:bg-zinc-900/5 has-data-checked:border-zinc-900 dark:has-data-checked:bg-zinc-900/10 gap-2 group-data-[disabled=true]/field:opacity-50 has-[>[data-slot=field]]:rounded-md has-[>[data-slot=field]]:border [&>*]:data-[slot=field]:p-3 group/field-label peer/field-label flex w-fit leading-snug dark:has-data-checked:bg-zinc-50/5 dark:has-data-checked:border-zinc-50 dark:dark:has-data-checked:bg-zinc-50/10',
        'has-[>[data-slot=field]]:w-full has-[>[data-slot=field]]:flex-col',
        className,
      )}
      {...props}
    />
  );
}

function FieldTitle({ className, ...props }: React.ComponentProps<'div'>) {
  return (
    <div
      data-slot="field-label"
      className={cn(
        'gap-2 text-sm font-medium group-data-[disabled=true]/field:opacity-50 flex w-fit items-center leading-snug',
        className,
      )}
      {...props}
    />
  );
}

function FieldDescription({ className, ...props }: React.ComponentProps<'p'>) {
  return (
    <p
      data-slot="field-description"
      className={cn(
        'text-zinc-500 text-left text-sm [[data-variant=legend]+&]:-mt-1.5 leading-normal font-normal group-has-[[data-orientation=horizontal]]/field:text-balance dark:text-zinc-400',
        'last:mt-0 nth-last-2:-mt-1',
        '[&>a:hover]:text-zinc-900 [&>a]:underline [&>a]:underline-offset-4 dark:[&>a:hover]:text-zinc-50',
        className,
      )}
      {...props}
    />
  );
}

function FieldSeparator({
  children,
  className,
  ...props
}: React.ComponentProps<'div'> & {
  children?: React.ReactNode;
}) {
  return (
    <div
      data-slot="field-separator"
      data-content={!!children}
      className={cn(
        '-my-2 h-5 text-sm group-data-[variant=outline]/field-group:-mb-2 relative',
        className,
      )}
      {...props}
    >
      <Separator className="absolute inset-0 top-1/2" />
      {children && (
        <span
          className="text-zinc-500 px-2 bg-white relative mx-auto block w-fit dark:text-zinc-400 dark:bg-zinc-950"
          data-slot="field-separator-content"
        >
          {children}
        </span>
      )}
    </div>
  );
}

function FieldError({
  className,
  children,
  errors,
  ...props
}: React.ComponentProps<'div'> & {
  errors?: Array<{ message?: string } | undefined>;
}) {
  const content = useMemo(() => {
    if (children) {
      return children;
    }

    if (!errors?.length) {
      return null;
    }

    const uniqueErrors = [...new Map(errors.map((error) => [error?.message, error])).values()];

    if (uniqueErrors?.length == 1) {
      return uniqueErrors[0]?.message;
    }

    return (
      <ul className="ml-4 flex list-disc flex-col gap-1">
        {uniqueErrors.map((error, index) => error?.message && <li key={index}>{error.message}</li>)}
      </ul>
    );
  }, [children, errors]);

  if (!content) {
    return null;
  }

  return (
    <div
      role="alert"
      data-slot="field-error"
      className={cn('text-red-500 text-sm font-normal dark:text-red-900', className)}
      {...props}
    >
      {content}
    </div>
  );
}

export {
  Field,
  FieldLabel,
  FieldDescription,
  FieldError,
  FieldGroup,
  FieldLegend,
  FieldSeparator,
  FieldSet,
  FieldContent,
  FieldTitle,
};
```

---

## 📂 `label.tsx`

Caminho original no repositório: `/packages/ui/src/components/label.tsx`

```tsx
'use client';

import type * as React from 'react';

import { cn } from '@manager/lib';

function Label({ className, ...props }: React.ComponentProps<'label'>) {
  return (
    <label
      data-slot="label"
      className={cn(
        'gap-2 text-xs leading-none font-medium group-data-[disabled=true]:opacity-50 peer-disabled:opacity-50 flex items-center select-none group-data-[disabled=true]:pointer-events-none peer-disabled:cursor-not-allowed',
        className,
      )}
      {...props}
    />
  );
}

export { Label };
```

---

## 📂 `calendar.tsx`

Caminho original no repositório: `/packages/ui/src/components/calendar.tsx`

```tsx
import * as React from 'react';
import { type DayButton, DayPicker, getDefaultClassNames } from 'react-day-picker';

import { cn } from '@manager/lib';
import { ChevronDown, ChevronLeft, ChevronRight } from '@untitled-ui/icons-react';
import { Button, buttonVariants } from './button';

function Calendar({
  className,
  classNames,
  showOutsideDays = true,
  captionLayout = 'label',
  buttonVariant = 'ghost',
  formatters,
  components,
  ...props
}: React.ComponentProps<typeof DayPicker> & {
  buttonVariant?: React.ComponentProps<typeof Button>['variant'];
}) {
  const defaultClassNames = getDefaultClassNames();

  return (
    <DayPicker
      showOutsideDays={showOutsideDays}
      className={cn(
        'p-3 [--cell-radius:var(--radius-md)] [--cell-size:--spacing(8)] bg-white group/calendar [[data-slot=card-content]_&]:bg-transparent [[data-slot=popover-content]_&]:bg-transparent dark:bg-zinc-950',
        String.raw`rtl:**:[.rdp-button\_next>svg]:rotate-180`,
        String.raw`rtl:**:[.rdp-button\_previous>svg]:rotate-180`,
        className,
      )}
      captionLayout={captionLayout}
      formatters={{
        formatMonthDropdown: (date) => date.toLocaleString('default', { month: 'short' }),
        ...formatters,
      }}
      classNames={{
        root: cn('w-fit', defaultClassNames.root),
        months: cn('flex gap-4 flex-col md:flex-row relative', defaultClassNames.months),
        month: cn('flex flex-col w-full gap-4', defaultClassNames.month),
        nav: cn(
          'flex items-center gap-1 w-full absolute top-0 inset-x-0 justify-between',
          defaultClassNames.nav,
        ),
        button_previous: cn(
          buttonVariants({ variant: buttonVariant }),
          'size-(--cell-size) aria-disabled:opacity-50 p-0 select-none',
          defaultClassNames.button_previous,
        ),
        button_next: cn(
          buttonVariants({ variant: buttonVariant }),
          'size-(--cell-size) aria-disabled:opacity-50 p-0 select-none',
          defaultClassNames.button_next,
        ),
        month_caption: cn(
          'flex items-center justify-center h-(--cell-size) w-full px-(--cell-size)',
          defaultClassNames.month_caption,
        ),
        dropdowns: cn(
          'w-full flex items-center text-sm font-medium justify-center h-(--cell-size) gap-1.5',
          defaultClassNames.dropdowns,
        ),
        dropdown_root: cn(
          'relative cn-calendar-dropdown-root rounded-(--cell-radius)',
          defaultClassNames.dropdown_root,
        ),
        dropdown: cn(
          'absolute bg-white inset-0 opacity-0 dark:bg-zinc-950',
          defaultClassNames.dropdown,
        ),
        caption_label: cn(
          'select-none font-medium',
          captionLayout === 'label'
            ? 'text-sm'
            : 'cn-calendar-caption-label rounded-(--cell-radius) flex items-center gap-1 text-sm  [&>svg]:text-zinc-500 [&>svg]:size-3.5 dark:[&>svg]:text-zinc-400',
          defaultClassNames.caption_label,
        ),
        table: 'w-full border-collapse',
        weekdays: cn('flex', defaultClassNames.weekdays),
        weekday: cn(
          'text-zinc-500 rounded-(--cell-radius) flex-1 font-normal text-[0.8rem] select-none dark:text-zinc-400',
          defaultClassNames.weekday,
        ),
        week: cn('flex w-full mt-2', defaultClassNames.week),
        week_number_header: cn('select-none w-(--cell-size)', defaultClassNames.week_number_header),
        week_number: cn(
          'text-[0.8rem] select-none text-zinc-500 dark:text-zinc-400',
          defaultClassNames.week_number,
        ),
        day: cn(
          'relative w-full rounded-(--cell-radius) h-full p-0 text-center [&:last-child[data-selected=true]_button]:rounded-r-(--cell-radius) group/day aspect-square select-none',
          props.showWeekNumber
            ? '[&:nth-child(2)[data-selected=true]_button]:rounded-l-(--cell-radius)'
            : '[&:first-child[data-selected=true]_button]:rounded-l-(--cell-radius)',
          defaultClassNames.day,
        ),
        range_start: cn(
          'rounded-l-(--cell-radius) bg-zinc-100 elative after:bg-zinc-100 after:absolute after:inset-y-0 after:w-4 after:right-0 -z-0 isolate dark:bg-zinc-800 dark:after:bg-zinc-800',
          defaultClassNames.range_start,
        ),
        range_middle: cn('rounded-none', defaultClassNames.range_middle),
        range_end: cn(
          'rounded-r-(--cell-radius) bg-zinc-100 relative after:bg-zinc-100 after:absolute after:inset-y-0 after:w-4 after:left-0 -z-0 isolate dark:bg-zinc-800 dark:after:bg-zinc-800',
          defaultClassNames.range_end,
        ),
        today: cn(
          'bg-zinc-100 text-zinc-950 rounded-(--cell-radius) data-[selected=true]:rounded-none dark:bg-zinc-800 dark:text-zinc-50',
          defaultClassNames.today,
        ),
        outside: cn(
          'text-zinc-500 aria-selected:text-zinc-500 dark:text-zinc-400 dark:aria-selected:text-zinc-400',
          defaultClassNames.outside,
        ),
        disabled: cn('text-zinc-500 opacity-50 dark:text-zinc-400', defaultClassNames.disabled),
        hidden: cn('invisible', defaultClassNames.hidden),
        ...classNames,
      }}
      components={{
        Root: ({ className, rootRef, ...props }) => {
          return <div data-slot="calendar" ref={rootRef} className={cn(className)} {...props} />;
        },
        Chevron: ({ className, orientation, ...props }) => {
          if (orientation === 'left') {
            return <ChevronLeft strokeWidth={2} className={cn('size-4', className)} {...props} />;
          }

          if (orientation === 'right') {
            return <ChevronRight strokeWidth={2} className={cn('size-4', className)} {...props} />;
          }

          return <ChevronDown strokeWidth={2} className={cn('size-4', className)} {...props} />;
        },
        DayButton: CalendarDayButton,
        WeekNumber: ({ children, ...props }) => {
          return (
            <td {...props}>
              <div className="flex size-(--cell-size) items-center justify-center text-center">
                {children}
              </div>
            </td>
          );
        },
        ...components,
      }}
      {...props}
    />
  );
}

function CalendarDayButton({
  className,
  day,
  modifiers,
  ...props
}: React.ComponentProps<typeof DayButton>) {
  const defaultClassNames = getDefaultClassNames();

  const ref = React.useRef<HTMLButtonElement>(null);
  React.useEffect(() => {
    if (modifiers.focused) ref.current?.focus();
  }, [modifiers.focused]);

  return (
    <Button
      variant="ghost"
      size="icon"
      data-day={day.date.toLocaleDateString()}
      data-selected-single={
        modifiers.selected &&
        !modifiers.range_start &&
        !modifiers.range_end &&
        !modifiers.range_middle
      }
      data-range-start={modifiers.range_start}
      data-range-end={modifiers.range_end}
      data-range-middle={modifiers.range_middle}
      className={cn(
        'data-[selected-single=true]:bg-zinc-900 data-[selected-single=true]:text-zinc-50 data-[range-middle=true]:bg-zinc-100 data-[range-middle=true]:text-zinc-950 data-[range-start=true]:bg-zinc-900 data-[range-start=true]:text-zinc-50 data-[range-end=true]:bg-zinc-900 data-[range-end=true]:text-zinc-50 group-data-[focused=true]/day:border-ring group-data-[focused=true]/day:ring-ring/50 dark:hover:text-zinc-950 relative isolate z-10 flex aspect-square size-auto w-full min-w-(--cell-size) flex-col gap-1 border-0 leading-none font-normal group-data-[focused=true]/day:relative group-data-[focused=true]/day:z-10 group-data-[focused=true]/day:ring-[3px] data-[range-end=true]:rounded-(--cell-radius) data-[range-end=true]:rounded-r-(--cell-radius) data-[range-middle=true]:rounded-none data-[range-start=true]:rounded-(--cell-radius) data-[range-start=true]:rounded-l-(--cell-radius) [&>span]:text-xs [&>span]:opacity-70 dark:data-[selected-single=true]:bg-zinc-50 dark:data-[selected-single=true]:text-zinc-900 dark:data-[range-middle=true]:bg-zinc-800 dark:data-[range-middle=true]:text-zinc-50 dark:data-[range-start=true]:bg-zinc-50 dark:data-[range-start=true]:text-zinc-900 dark:data-[range-end=true]:bg-zinc-50 dark:data-[range-end=true]:text-zinc-900 dark:dark:hover:text-zinc-50',
        defaultClassNames.day,
        className,
      )}
      {...props}
    />
  );
}

export { Calendar, CalendarDayButton };
```

---

