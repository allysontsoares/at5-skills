# 📦 Buttons & Actions

Componentes de botões, grupos de botões, seletores de alternância e elementos interativos de ação básica.

---

## 📂 `button.tsx`

Caminho original no repositório: `/packages/ui/src/components/button.tsx`

```tsx
'use client';

import { mergeProps, useRender } from '@base-ui/react';
import { useMediaQuery } from '@manager/hooks';
import { cn } from '@manager/lib';
import * as React from 'react';
import { forwardRef } from 'react';
import { type VariantProps, tv } from 'tailwind-variants';
import { Loader } from './loader';

// =================================================================================
// ESTILOS E TIPAGEM
// =================================================================================

/**
 * @description Define os estilos base e variantes para o componente Button utilizando `tailwind-variants`.
 * Este objeto mapeia propriedades como `variant`, `accent`, `size`, `radius` e `isPending`
 * para classes Tailwind CSS correspondentes.
 */
const buttonStyles = tv({
  /**
   * Classes base aplicadas a todos os botões para layout, foco, transições e acessibilidade.
   */
  base: 'inline-flex items-center justify-center whitespace-nowrap rounded-md font-medium transition-colors duration-200 outline-none focus-visible:ring-1 focus-visible:ring-ring disabled:pointer-events-none disabled:opacity-50 [&_svg]:shrink-0 active:scale-95',

  variants: {
    /**
     * @description Define o estilo visual principal do botão.
     * - `solid`: Preenchido com a cor de destaque.
     * - `soft`: Fundo suave com texto na cor de destaque.
     * - `surface`: Fundo claro com borda e texto na cor de destaque.
     * - `outline`: Borda na cor de destaque com fundo transparente.
     * - `ghost`: Fundo transparente com texto na cor de destaque, sem borda.
     * - `dashed`: Borda tracejada com fundo transparente.
     * - `dim`: Texto esmaecido sem fundo.
     */
    variant: {
      solid: '',
      soft: '',
      surface: 'border-2',
      outline: 'border-1 bg-transparent',
      ghost: 'bg-transparent',
      dashed: 'border-1 border-dashed bg-transparent',
      dim: 'bg-transparent',
    },
    /**
     * @description Define o esquema de cores do botão. Pode ser uma das cores da aplicação
     * (primary, secondary, success, warning, danger) ou cores do espectro Radix UI.
     */
    accent: {
      // Application colors
      primary: '',
      secondary: '',
      success: '',
      warning: '',
      danger: '',

      // Radix colors (expandable as needed)
      gray: '',
      mauve: '',
      slate: '',
      sage: '',
      olive: '',
      sand: '',
      tomato: '',
      red: '',
      ruby: '',
      crimson: '',
      pink: '',
      plum: '',
      purple: '',
      violet: '',
      iris: '',
      indigo: '',
      blue: '',
      cyan: '',
      teal: '',
      jade: '',
      green: '',
      grass: '',
      brown: '',
      bronze: '',
      gold: '',
      sky: '',
      mint: '',
      lime: '',
      yellow: '',
      amber: '',
      orange: '',
    },
    /**
     * @description Define o tamanho do botão, incluindo padding, altura mínima e tamanho dos ícones.
     */
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
    /**
     * @description Define o raio da borda do botão.
     */
    radius: {
      none: 'rounded-none',
      sm: 'rounded-sm',
      md: 'rounded-md',
      lg: 'rounded-lg',
      xl: 'rounded-xl',
      full: 'rounded-full',
    },
    /**
     * @description Define o comportamento e função do botão.
     * - `default`: Comportamento padrão de botão.
     * - `icon`: Botão quadrado otimizado para ícones apenas.
     * - `link`: Botão com aparência de link.
     * - `input`: Botão com aparência de input (para dropdowns).
     */
    mode: {
      default: '',
      icon: '',
      link: 'h-auto p-0 bg-transparent rounded-none hover:bg-transparent data-[state=open]:bg-transparent',
      input:
        'justify-start font-normal hover:bg-background [&_svg]:transition-colors [&_svg]:hover:text-foreground data-[state=open]:bg-background focus-visible:border-ring focus-visible:outline-hidden focus-visible:ring-[3px] focus-visible:ring-ring/30',
    },
    /**
     * @description Define a aparência visual do botão.
     * - `default`: Com sombra/elevação padrão.
     * - `ghost`: Sem sombra/elevação.
     */
    appearance: {
      default: '',
      ghost: '',
    },
    /**
     * @description Define o formato das bordas do botão.
     * - `default`: Bordas normais baseadas em radius.
     * - `circle`: Bordas totalmente circulares.
     */
    shape: {
      default: '',
      circle: 'rounded-full',
    },
    /**
     * @description Permite altura automática baseada no conteúdo.
     */
    autoHeight: {
      true: '',
      false: '',
    },
    /**
     * @description Indica se o botão está em um estado de "loading" (carregando/aguardando).
     * Aplica estilos visuais para indicar o estado.
     */
    isLoading: {
      true: 'cursor-wait opacity-70',
      false: '',
    },
    /**
     * @description Controla sublinhado no hover.
     */
    underline: {
      solid: '',
      dashed: '',
    },
    /**
     * @description Controla sublinhado permanente.
     */
    underlined: {
      solid: '',
      dashed: '',
    },
    /**
     * @description Indica se o botão mostra texto placeholder.
     */
    placeholder: {
      true: 'text-muted-foreground',
      false: '',
    },
  },
  /**
   * @description Combinações específicas de `variant` e `accent` para aplicar estilos mais detalhados.
   * Isso permite definir o esquema de cores para cada combinação de variante e destaque.
   */
  compoundVariants: [
    // Mode Icon - tamanhos quadrados
    {
      mode: 'icon',
      size: 'xs',
      className: 'size-7 p-0',
    },
    {
      mode: 'icon',
      size: 'sm',
      className: 'size-8 p-0',
    },
    {
      mode: 'icon',
      size: 'md',
      className: 'size-9 p-0',
    },
    {
      mode: 'icon',
      size: 'lg',
      className: 'size-10 p-0',
    },
    {
      mode: 'icon',
      size: 'xl',
      className: 'size-11 p-0',
    },

    // Auto height variants
    {
      size: 'xs',
      autoHeight: true,
      className: 'h-auto min-h-7',
    },
    {
      size: 'sm',
      autoHeight: true,
      className: 'h-auto min-h-8',
    },
    {
      size: 'md',
      autoHeight: true,
      className: 'h-auto min-h-9',
    },
    {
      size: 'lg',
      autoHeight: true,
      className: 'h-auto min-h-10',
    },
    {
      size: 'xl',
      autoHeight: true,
      className: 'h-auto min-h-11',
    },

    // Shadow support for default appearance
    {
      variant: 'solid',
      mode: 'default',
      appearance: 'default',
      className: 'shadow-xs shadow-black/5',
    },
    {
      variant: 'soft',
      mode: 'default',
      appearance: 'default',
      className: 'shadow-xs shadow-black/5',
    },
    {
      variant: 'surface',
      mode: 'default',
      appearance: 'default',
      className: 'shadow-xs shadow-black/5',
    },
    {
      variant: 'outline',
      mode: 'default',
      appearance: 'default',
      className: 'shadow-xs shadow-black/5',
    },
    {
      variant: 'dashed',
      mode: 'default',
      appearance: 'default',
      className: 'shadow-xs shadow-black/5',
    },

    // Link mode variants
    {
      variant: 'solid',
      mode: 'link',
      underline: 'solid',
      className: 'font-medium hover:underline hover:underline-offset-4 hover:decoration-solid',
    },
    {
      variant: 'solid',
      mode: 'link',
      underline: 'dashed',
      className:
        'font-medium hover:underline hover:underline-offset-4 hover:decoration-dashed decoration-1',
    },
    {
      variant: 'solid',
      mode: 'link',
      underlined: 'solid',
      className: 'font-medium underline underline-offset-4 decoration-solid',
    },
    {
      variant: 'solid',
      mode: 'link',
      underlined: 'dashed',
      className: 'font-medium underline underline-offset-4 decoration-dashed decoration-1',
    },

    // Ghost appearance variants
    {
      variant: 'solid',
      appearance: 'ghost',
      className: 'bg-transparent hover:bg-primary/5 data-[state=open]:bg-primary/5',
    },
    {
      variant: 'outline',
      appearance: 'ghost',
      className: 'bg-transparent hover:bg-accent data-[state=open]:bg-accent',
    },

    // Icons opacity for variants
    {
      variant: 'ghost',
      mode: 'default',
      className: '[&_svg:not([role=img]):not([class*=text-]):not([class*=opacity-])]:opacity-60',
    },
    {
      variant: 'outline',
      mode: 'default',
      className: '[&_svg:not([role=img]):not([class*=text-]):not([class*=opacity-])]:opacity-60',
    },
    {
      variant: 'dashed',
      mode: 'default',
      className: '[&_svg:not([role=img]):not([class*=text-]):not([class*=opacity-])]:opacity-60',
    },
    {
      variant: 'soft',
      mode: 'default',
      className: '[&_svg:not([role=img]):not([class*=text-]):not([class*=opacity-])]:opacity-60',
    },

    // Primary (Application brand color)
    {
      variant: 'solid',
      accent: 'primary',
      className: 'bg-brand-9 text-white hover:bg-brand-10 focus-visible:outline-brand-9',
    },
    {
      variant: 'soft',
      accent: 'primary',
      className: 'bg-brand-5 text-brand-11 hover:bg-brand-6 focus-visible:outline-brand-9',
    },
    {
      variant: 'surface',
      accent: 'primary',
      className:
        'bg-brand-2 border-brand-7 text-brand-11 hover:bg-brand-3 focus-visible:outline-brand-9',
    },
    {
      variant: 'outline',
      accent: 'primary',
      className: 'border-brand-7 text-brand-11 hover:bg-brand-3 focus-visible:outline-brand-9',
    },
    {
      variant: 'ghost',
      accent: 'primary',
      className: 'text-brand-11 hover:bg-brand-3 focus-visible:outline-brand-9',
    },

    // Secondary
    {
      variant: 'solid',
      accent: 'secondary',
      className:
        'bg-mauve-5 text-mauve-12 border border-mauve-5 hover:bg-mauve-6 focus-visible:outline-mauve-9',
    },
    {
      variant: 'soft',
      accent: 'secondary',
      className: 'bg-mauve-4 text-mauve-11 hover:bg-mauve-5 focus-visible:outline-mauve-9',
    },
    {
      variant: 'surface',
      accent: 'secondary',
      className:
        'bg-gray-2 border-mauve-7 text-mauve-11 hover:bg-mauve-3 focus-visible:outline-mauve-9',
    },
    {
      variant: 'outline',
      accent: 'secondary',
      className: 'border-mauve-7 text-mauve-11 hover:bg-mauve-3 focus-visible:outline-mauve-9',
    },
    {
      variant: 'ghost',
      accent: 'secondary',
      className: 'text-mauve-11 hover:bg-mauve-3 focus-visible:outline-mauve-9',
    },

    // Success
    {
      variant: 'solid',
      accent: 'success',
      className: 'bg-green-9 text-white hover:bg-green-10 focus-visible:outline-green-9',
    },
    {
      variant: 'soft',
      accent: 'success',
      className: 'bg-green-4 text-green-11 hover:bg-green-5 focus-visible:outline-green-9',
    },
    {
      variant: 'surface',
      accent: 'success',
      className:
        'bg-green-2 border-green-7 text-green-11 hover:bg-green-3 focus-visible:outline-green-9',
    },
    {
      variant: 'outline',
      accent: 'success',
      className: 'border-green-7 text-green-11 hover:bg-green-3 focus-visible:outline-green-9',
    },
    {
      variant: 'ghost',
      accent: 'success',
      className: 'text-green-11 hover:bg-green-3 focus-visible:outline-green-9',
    },

    // Warning
    {
      variant: 'solid',
      accent: 'warning',
      className: 'bg-amber-9 text-amber-1 hover:bg-amber-10 focus-visible:outline-amber-9',
    },
    {
      variant: 'soft',
      accent: 'warning',
      className: 'bg-amber-4 text-amber-11 hover:bg-amber-5 focus-visible:outline-amber-9',
    },
    {
      variant: 'surface',
      accent: 'warning',
      className:
        'bg-amber-2 border-amber-7 text-amber-11 hover:bg-amber-3 focus-visible:outline-amber-9',
    },
    {
      variant: 'outline',
      accent: 'warning',
      className: 'border-amber-7 text-amber-11 hover:bg-amber-3 focus-visible:outline-amber-9',
    },
    {
      variant: 'ghost',
      accent: 'warning',
      className: 'text-amber-11 hover:bg-amber-3 focus-visible:outline-amber-9',
    },

    // Danger
    {
      variant: 'solid',
      accent: 'danger',
      className: 'bg-red-9 text-white hover:bg-red-10 focus-visible:outline-red-9',
    },
    {
      variant: 'soft',
      accent: 'danger',
      className: 'bg-red-4 text-red-11 hover:bg-red-5 focus-visible:outline-red-9',
    },
    {
      variant: 'surface',
      accent: 'danger',
      className: 'bg-red-2 border-red-7 text-red-11 hover:bg-red-3 focus-visible:outline-red-9',
    },
    {
      variant: 'outline',
      accent: 'danger',
      className: 'border-red-7 text-red-11 hover:bg-red-3 focus-visible:outline-red-9',
    },
    {
      variant: 'ghost',
      accent: 'danger',
      className: 'text-red-11 hover:bg-red-3 focus-visible:outline-red-9',
    },
    {
      variant: 'dashed',
      accent: 'danger',
      className: 'border-red-7 text-red-11 hover:bg-red-3 focus-visible:outline-red-9',
    },
    {
      variant: 'dim',
      accent: 'danger',
      className: 'text-red-8 hover:text-red-11 focus-visible:outline-red-9',
    },

    // Radix colors - All colors with all variants

    // Gray
    {
      variant: 'solid',
      accent: 'gray',
      className: 'bg-gray-9 text-white hover:bg-gray-10 focus-visible:outline-gray-9',
    },
    {
      variant: 'soft',
      accent: 'gray',
      className: 'bg-gray-4 text-gray-11 hover:bg-gray-5 focus-visible:outline-gray-9',
    },
    {
      variant: 'surface',
      accent: 'gray',
      className:
        'bg-gray-2 border-gray-7 text-gray-11 hover:bg-gray-3 focus-visible:outline-gray-9',
    },
    {
      variant: 'outline',
      accent: 'gray',
      className: 'border-gray-7 text-gray-11 hover:bg-gray-3 focus-visible:outline-gray-9',
    },
    {
      variant: 'ghost',
      accent: 'gray',
      className: 'text-gray-11 hover:bg-gray-3 focus-visible:outline-gray-9',
    },
    {
      variant: 'dashed',
      accent: 'gray',
      className: 'border-gray-7 text-gray-11 hover:bg-gray-3 focus-visible:outline-gray-9',
    },
    {
      variant: 'dim',
      accent: 'gray',
      className: 'text-gray-8 hover:text-gray-11 focus-visible:outline-gray-9',
    },

    // Mauve
    {
      variant: 'solid',
      accent: 'mauve',
      className: 'bg-mauve-9 text-white hover:bg-mauve-10 focus-visible:outline-mauve-9',
    },
    {
      variant: 'soft',
      accent: 'mauve',
      className: 'bg-mauve-4 text-mauve-11 hover:bg-mauve-5 focus-visible:outline-mauve-9',
    },
    {
      variant: 'surface',
      accent: 'mauve',
      className:
        'bg-mauve-2 border-mauve-7 text-mauve-11 hover:bg-mauve-3 focus-visible:outline-mauve-9',
    },
    {
      variant: 'outline',
      accent: 'mauve',
      className: 'border-mauve-7 text-mauve-11 hover:bg-mauve-3 focus-visible:outline-mauve-9',
    },
    {
      variant: 'ghost',
      accent: 'mauve',
      className: 'text-mauve-11 hover:bg-mauve-3 focus-visible:outline-mauve-9',
    },
    {
      variant: 'dashed',
      accent: 'mauve',
      className: 'border-mauve-7 text-mauve-11 hover:bg-mauve-3 focus-visible:outline-mauve-9',
    },
    {
      variant: 'dim',
      accent: 'mauve',
      className: 'text-mauve-8 hover:text-mauve-11 focus-visible:outline-mauve-9',
    },

    // Slate
    {
      variant: 'solid',
      accent: 'slate',
      className: 'bg-mauve-9 text-white hover:bg-mauve-10 focus-visible:outline-mauve-9',
    },
    {
      variant: 'soft',
      accent: 'slate',
      className: 'bg-mauve-4 text-mauve-11 hover:bg-mauve-5 focus-visible:outline-mauve-9',
    },
    {
      variant: 'surface',
      accent: 'slate',
      className:
        'bg-mauve-2 border-mauve-7 text-mauve-11 hover:bg-mauve-3 focus-visible:outline-mauve-9',
    },
    {
      variant: 'outline',
      accent: 'slate',
      className: 'border-mauve-7 text-mauve-11 hover:bg-mauve-3 focus-visible:outline-mauve-9',
    },
    {
      variant: 'ghost',
      accent: 'slate',
      className: 'text-mauve-11 hover:bg-mauve-3 focus-visible:outline-mauve-9',
    },
    {
      variant: 'dashed',
      accent: 'slate',
      className: 'border-mauve-7 text-mauve-11 hover:bg-mauve-3 focus-visible:outline-mauve-9',
    },
    {
      variant: 'dim',
      accent: 'slate',
      className: 'text-mauve-8 hover:text-mauve-11 focus-visible:outline-mauve-9',
    },

    // Sage
    {
      variant: 'solid',
      accent: 'sage',
      className: 'bg-sage-9 text-white hover:bg-sage-10 focus-visible:outline-sage-9',
    },
    {
      variant: 'soft',
      accent: 'sage',
      className: 'bg-sage-4 text-sage-11 hover:bg-sage-5 focus-visible:outline-sage-9',
    },
    {
      variant: 'surface',
      accent: 'sage',
      className:
        'bg-sage-2 border-sage-7 text-sage-11 hover:bg-sage-3 focus-visible:outline-sage-9',
    },
    {
      variant: 'outline',
      accent: 'sage',
      className: 'border-sage-7 text-sage-11 hover:bg-sage-3 focus-visible:outline-sage-9',
    },
    {
      variant: 'ghost',
      accent: 'sage',
      className: 'text-sage-11 hover:bg-sage-3 focus-visible:outline-sage-9',
    },
    {
      variant: 'dashed',
      accent: 'sage',
      className: 'border-sage-7 text-sage-11 hover:bg-sage-3 focus-visible:outline-sage-9',
    },
    {
      variant: 'dim',
      accent: 'sage',
      className: 'text-sage-8 hover:text-sage-11 focus-visible:outline-sage-9',
    },

    // Olive
    {
      variant: 'solid',
      accent: 'olive',
      className: 'bg-olive-9 text-white hover:bg-olive-10 focus-visible:outline-olive-9',
    },
    {
      variant: 'soft',
      accent: 'olive',
      className: 'bg-olive-4 text-olive-11 hover:bg-olive-5 focus-visible:outline-olive-9',
    },
    {
      variant: 'surface',
      accent: 'olive',
      className:
        'bg-olive-2 border-olive-7 text-olive-11 hover:bg-olive-3 focus-visible:outline-olive-9',
    },
    {
      variant: 'outline',
      accent: 'olive',
      className: 'border-olive-7 text-olive-11 hover:bg-olive-3 focus-visible:outline-olive-9',
    },
    {
      variant: 'ghost',
      accent: 'olive',
      className: 'text-olive-11 hover:bg-olive-3 focus-visible:outline-olive-9',
    },
    {
      variant: 'dashed',
      accent: 'olive',
      className: 'border-olive-7 text-olive-11 hover:bg-olive-3 focus-visible:outline-olive-9',
    },
    {
      variant: 'dim',
      accent: 'olive',
      className: 'text-olive-8 hover:text-olive-11 focus-visible:outline-olive-9',
    },

    // Sand
    {
      variant: 'solid',
      accent: 'sand',
      className: 'bg-sand-9 text-white hover:bg-sand-10 focus-visible:outline-sand-9',
    },
    {
      variant: 'soft',
      accent: 'sand',
      className: 'bg-sand-4 text-sand-11 hover:bg-sand-5 focus-visible:outline-sand-9',
    },
    {
      variant: 'surface',
      accent: 'sand',
      className:
        'bg-sand-2 border-sand-7 text-sand-11 hover:bg-sand-3 focus-visible:outline-sand-9',
    },
    {
      variant: 'outline',
      accent: 'sand',
      className: 'border-sand-7 text-sand-11 hover:bg-sand-3 focus-visible:outline-sand-9',
    },
    {
      variant: 'ghost',
      accent: 'sand',
      className: 'text-sand-11 hover:bg-sand-3 focus-visible:outline-sand-9',
    },
    {
      variant: 'dashed',
      accent: 'sand',
      className: 'border-sand-7 text-sand-11 hover:bg-sand-3 focus-visible:outline-sand-9',
    },
    {
      variant: 'dim',
      accent: 'sand',
      className: 'text-sand-8 hover:text-sand-11 focus-visible:outline-sand-9',
    },

    // Tomato
    {
      variant: 'solid',
      accent: 'tomato',
      className: 'bg-tomato-9 text-white hover:bg-tomato-10 focus-visible:outline-tomato-9',
    },
    {
      variant: 'soft',
      accent: 'tomato',
      className: 'bg-tomato-4 text-tomato-11 hover:bg-tomato-5 focus-visible:outline-tomato-9',
    },
    {
      variant: 'surface',
      accent: 'tomato',
      className:
        'bg-tomato-2 border-tomato-7 text-tomato-11 hover:bg-tomato-3 focus-visible:outline-tomato-9',
    },
    {
      variant: 'outline',
      accent: 'tomato',
      className: 'border-tomato-7 text-tomato-11 hover:bg-tomato-3 focus-visible:outline-tomato-9',
    },
    {
      variant: 'ghost',
      accent: 'tomato',
      className: 'text-tomato-11 hover:bg-tomato-3 focus-visible:outline-tomato-9',
    },
    {
      variant: 'dashed',
      accent: 'tomato',
      className: 'border-tomato-7 text-tomato-11 hover:bg-tomato-3 focus-visible:outline-tomato-9',
    },
    {
      variant: 'dim',
      accent: 'tomato',
      className: 'text-tomato-8 hover:text-tomato-11 focus-visible:outline-tomato-9',
    },

    // Red
    {
      variant: 'solid',
      accent: 'red',
      className: 'bg-red-9 text-white hover:bg-red-10 focus-visible:outline-red-9',
    },
    {
      variant: 'soft',
      accent: 'red',
      className: 'bg-red-4 text-red-11 hover:bg-red-5 focus-visible:outline-red-9',
    },
    {
      variant: 'surface',
      accent: 'red',
      className: 'bg-red-2 border-red-7 text-red-11 hover:bg-red-3 focus-visible:outline-red-9',
    },
    {
      variant: 'outline',
      accent: 'red',
      className: 'border-red-7 text-red-11 hover:bg-red-3 focus-visible:outline-red-9',
    },
    {
      variant: 'ghost',
      accent: 'red',
      className: 'text-red-11 hover:bg-red-3 focus-visible:outline-red-9',
    },
    {
      variant: 'dashed',
      accent: 'red',
      className: 'border-red-7 text-red-11 hover:bg-red-3 focus-visible:outline-red-9',
    },
    {
      variant: 'dim',
      accent: 'red',
      className: 'text-red-8 hover:text-red-11 focus-visible:outline-red-9',
    },

    // Ruby
    {
      variant: 'solid',
      accent: 'ruby',
      className: 'bg-ruby-9 text-white hover:bg-ruby-10 focus-visible:outline-ruby-9',
    },
    {
      variant: 'soft',
      accent: 'ruby',
      className: 'bg-ruby-4 text-ruby-11 hover:bg-ruby-5 focus-visible:outline-ruby-9',
    },
    {
      variant: 'surface',
      accent: 'ruby',
      className:
        'bg-ruby-2 border-ruby-7 text-ruby-11 hover:bg-ruby-3 focus-visible:outline-ruby-9',
    },
    {
      variant: 'outline',
      accent: 'ruby',
      className: 'border-ruby-7 text-ruby-11 hover:bg-ruby-3 focus-visible:outline-ruby-9',
    },
    {
      variant: 'ghost',
      accent: 'ruby',
      className: 'text-ruby-11 hover:bg-ruby-3 focus-visible:outline-ruby-9',
    },
    {
      variant: 'dashed',
      accent: 'ruby',
      className: 'border-ruby-7 text-ruby-11 hover:bg-ruby-3 focus-visible:outline-ruby-9',
    },
    {
      variant: 'dim',
      accent: 'ruby',
      className: 'text-ruby-8 hover:text-ruby-11 focus-visible:outline-ruby-9',
    },

    // Crimson
    {
      variant: 'solid',
      accent: 'crimson',
      className: 'bg-crimson-9 text-white hover:bg-crimson-10 focus-visible:outline-crimson-9',
    },
    {
      variant: 'soft',
      accent: 'crimson',
      className: 'bg-crimson-4 text-crimson-11 hover:bg-crimson-5 focus-visible:outline-crimson-9',
    },
    {
      variant: 'surface',
      accent: 'crimson',
      className:
        'bg-crimson-2 border-crimson-7 text-crimson-11 hover:bg-crimson-3 focus-visible:outline-crimson-9',
    },
    {
      variant: 'outline',
      accent: 'crimson',
      className:
        'border-crimson-7 text-crimson-11 hover:bg-crimson-3 focus-visible:outline-crimson-9',
    },
    {
      variant: 'ghost',
      accent: 'crimson',
      className: 'text-crimson-11 hover:bg-crimson-3 focus-visible:outline-crimson-9',
    },
    {
      variant: 'dashed',
      accent: 'crimson',
      className:
        'border-crimson-7 text-crimson-11 hover:bg-crimson-3 focus-visible:outline-crimson-9',
    },
    {
      variant: 'dim',
      accent: 'crimson',
      className: 'text-crimson-8 hover:text-crimson-11 focus-visible:outline-crimson-9',
    },

    // Pink
    {
      variant: 'solid',
      accent: 'pink',
      className: 'bg-pink-9 text-white hover:bg-pink-10 focus-visible:outline-pink-9',
    },
    {
      variant: 'soft',
      accent: 'pink',
      className: 'bg-pink-4 text-pink-11 hover:bg-pink-5 focus-visible:outline-pink-9',
    },
    {
      variant: 'surface',
      accent: 'pink',
      className:
        'bg-pink-2 border-pink-7 text-pink-11 hover:bg-pink-3 focus-visible:outline-pink-9',
    },
    {
      variant: 'outline',
      accent: 'pink',
      className: 'border-pink-7 text-pink-11 hover:bg-pink-3 focus-visible:outline-pink-9',
    },
    {
      variant: 'ghost',
      accent: 'pink',
      className: 'text-pink-11 hover:bg-pink-3 focus-visible:outline-pink-9',
    },
    {
      variant: 'dashed',
      accent: 'pink',
      className: 'border-pink-7 text-pink-11 hover:bg-pink-3 focus-visible:outline-pink-9',
    },
    {
      variant: 'dim',
      accent: 'pink',
      className: 'text-pink-8 hover:text-pink-11 focus-visible:outline-pink-9',
    },

    // Plum
    {
      variant: 'solid',
      accent: 'plum',
      className: 'bg-plum-9 text-white hover:bg-plum-10 focus-visible:outline-plum-9',
    },
    {
      variant: 'soft',
      accent: 'plum',
      className: 'bg-plum-4 text-plum-11 hover:bg-plum-5 focus-visible:outline-plum-9',
    },
    {
      variant: 'surface',
      accent: 'plum',
      className:
        'bg-plum-2 border-plum-7 text-plum-11 hover:bg-plum-3 focus-visible:outline-plum-9',
    },
    {
      variant: 'outline',
      accent: 'plum',
      className: 'border-plum-7 text-plum-11 hover:bg-plum-3 focus-visible:outline-plum-9',
    },
    {
      variant: 'ghost',
      accent: 'plum',
      className: 'text-plum-11 hover:bg-plum-3 focus-visible:outline-plum-9',
    },
    {
      variant: 'dashed',
      accent: 'plum',
      className: 'border-plum-7 text-plum-11 hover:bg-plum-3 focus-visible:outline-plum-9',
    },
    {
      variant: 'dim',
      accent: 'plum',
      className: 'text-plum-8 hover:text-plum-11 focus-visible:outline-plum-9',
    },

    // Purple
    {
      variant: 'solid',
      accent: 'purple',
      className: 'bg-purple-9 text-white hover:bg-purple-10 focus-visible:outline-purple-9',
    },
    {
      variant: 'soft',
      accent: 'purple',
      className: 'bg-purple-4 text-purple-11 hover:bg-purple-5 focus-visible:outline-purple-9',
    },
    {
      variant: 'surface',
      accent: 'purple',
      className:
        'bg-purple-2 border-purple-7 text-purple-11 hover:bg-purple-3 focus-visible:outline-purple-9',
    },
    {
      variant: 'outline',
      accent: 'purple',
      className: 'border-purple-7 text-purple-11 hover:bg-purple-3 focus-visible:outline-purple-9',
    },
    {
      variant: 'ghost',
      accent: 'purple',
      className: 'text-purple-11 hover:bg-purple-3 focus-visible:outline-purple-9',
    },
    {
      variant: 'dashed',
      accent: 'purple',
      className: 'border-purple-7 text-purple-11 hover:bg-purple-3 focus-visible:outline-purple-9',
    },
    {
      variant: 'dim',
      accent: 'purple',
      className: 'text-purple-8 hover:text-purple-11 focus-visible:outline-purple-9',
    },

    // Violet
    {
      variant: 'solid',
      accent: 'violet',
      className: 'bg-violet-9 text-white hover:bg-violet-10 focus-visible:outline-violet-9',
    },
    {
      variant: 'soft',
      accent: 'violet',
      className: 'bg-violet-4 text-violet-11 hover:bg-violet-5 focus-visible:outline-violet-9',
    },
    {
      variant: 'surface',
      accent: 'violet',
      className:
        'bg-violet-2 border-violet-7 text-violet-11 hover:bg-violet-3 focus-visible:outline-violet-9',
    },
    {
      variant: 'outline',
      accent: 'violet',
      className: 'border-violet-7 text-violet-11 hover:bg-violet-3 focus-visible:outline-violet-9',
    },
    {
      variant: 'ghost',
      accent: 'violet',
      className: 'text-violet-11 hover:bg-violet-3 focus-visible:outline-violet-9',
    },
    {
      variant: 'dashed',
      accent: 'violet',
      className: 'border-violet-7 text-violet-11 hover:bg-violet-3 focus-visible:outline-violet-9',
    },
    {
      variant: 'dim',
      accent: 'violet',
      className: 'text-violet-8 hover:text-violet-11 focus-visible:outline-violet-9',
    },

    // Iris
    {
      variant: 'solid',
      accent: 'iris',
      className: 'bg-iris-9 text-white hover:bg-iris-10 focus-visible:outline-iris-9',
    },
    {
      variant: 'soft',
      accent: 'iris',
      className: 'bg-iris-4 text-iris-11 hover:bg-iris-5 focus-visible:outline-iris-9',
    },
    {
      variant: 'surface',
      accent: 'iris',
      className:
        'bg-iris-2 border-iris-7 text-iris-11 hover:bg-iris-3 focus-visible:outline-iris-9',
    },
    {
      variant: 'outline',
      accent: 'iris',
      className: 'border-iris-7 text-iris-11 hover:bg-iris-3 focus-visible:outline-iris-9',
    },
    {
      variant: 'ghost',
      accent: 'iris',
      className: 'text-iris-11 hover:bg-iris-3 focus-visible:outline-iris-9',
    },
    {
      variant: 'dashed',
      accent: 'iris',
      className: 'border-iris-7 text-iris-11 hover:bg-iris-3 focus-visible:outline-iris-9',
    },
    {
      variant: 'dim',
      accent: 'iris',
      className: 'text-iris-8 hover:text-iris-11 focus-visible:outline-iris-9',
    },

    // Indigo
    {
      variant: 'solid',
      accent: 'indigo',
      className: 'bg-indigo-9 text-white hover:bg-indigo-10 focus-visible:outline-indigo-9',
    },
    {
      variant: 'soft',
      accent: 'indigo',
      className: 'bg-indigo-4 text-indigo-11 hover:bg-indigo-5 focus-visible:outline-indigo-9',
    },
    {
      variant: 'surface',
      accent: 'indigo',
      className:
        'bg-indigo-2 border-indigo-7 text-indigo-11 hover:bg-indigo-3 focus-visible:outline-indigo-9',
    },
    {
      variant: 'outline',
      accent: 'indigo',
      className: 'border-indigo-7 text-indigo-11 hover:bg-indigo-3 focus-visible:outline-indigo-9',
    },
    {
      variant: 'ghost',
      accent: 'indigo',
      className: 'text-indigo-11 hover:bg-indigo-3 focus-visible:outline-indigo-9',
    },
    {
      variant: 'dashed',
      accent: 'indigo',
      className: 'border-indigo-7 text-indigo-11 hover:bg-indigo-3 focus-visible:outline-indigo-9',
    },
    {
      variant: 'dim',
      accent: 'indigo',
      className: 'text-indigo-8 hover:text-indigo-11 focus-visible:outline-indigo-9',
    },

    // Blue
    {
      variant: 'solid',
      accent: 'blue',
      className: 'bg-blue-9 text-white hover:bg-blue-10 focus-visible:outline-blue-9',
    },
    {
      variant: 'soft',
      accent: 'blue',
      className: 'bg-blue-4 text-blue-11 hover:bg-blue-5 focus-visible:outline-blue-9',
    },
    {
      variant: 'surface',
      accent: 'blue',
      className:
        'bg-blue-2 border-blue-7 text-blue-11 hover:bg-blue-3 focus-visible:outline-blue-9',
    },
    {
      variant: 'outline',
      accent: 'blue',
      className: 'border-blue-7 text-blue-11 hover:bg-blue-3 focus-visible:outline-blue-9',
    },
    {
      variant: 'ghost',
      accent: 'blue',
      className: 'text-blue-11 hover:bg-blue-3 focus-visible:outline-blue-9',
    },
    {
      variant: 'dashed',
      accent: 'blue',
      className: 'border-blue-7 text-blue-11 hover:bg-blue-3 focus-visible:outline-blue-9',
    },
    {
      variant: 'dim',
      accent: 'blue',
      className: 'text-blue-8 hover:text-blue-11 focus-visible:outline-blue-9',
    },

    // Cyan
    {
      variant: 'solid',
      accent: 'cyan',
      className: 'bg-cyan-9 text-white hover:bg-cyan-10 focus-visible:outline-cyan-9',
    },
    {
      variant: 'soft',
      accent: 'cyan',
      className: 'bg-cyan-4 text-cyan-11 hover:bg-cyan-5 focus-visible:outline-cyan-9',
    },
    {
      variant: 'surface',
      accent: 'cyan',
      className:
        'bg-cyan-2 border-cyan-7 text-cyan-11 hover:bg-cyan-3 focus-visible:outline-cyan-9',
    },
    {
      variant: 'outline',
      accent: 'cyan',
      className: 'border-cyan-7 text-cyan-11 hover:bg-cyan-3 focus-visible:outline-cyan-9',
    },
    {
      variant: 'ghost',
      accent: 'cyan',
      className: 'text-cyan-11 hover:bg-cyan-3 focus-visible:outline-cyan-9',
    },
    {
      variant: 'dashed',
      accent: 'cyan',
      className: 'border-cyan-7 text-cyan-11 hover:bg-cyan-3 focus-visible:outline-cyan-9',
    },
    {
      variant: 'dim',
      accent: 'cyan',
      className: 'text-cyan-8 hover:text-cyan-11 focus-visible:outline-cyan-9',
    },

    // Teal
    {
      variant: 'solid',
      accent: 'teal',
      className: 'bg-teal-9 text-white hover:bg-teal-10 focus-visible:outline-teal-9',
    },
    {
      variant: 'soft',
      accent: 'teal',
      className: 'bg-teal-4 text-teal-11 hover:bg-teal-5 focus-visible:outline-teal-9',
    },
    {
      variant: 'surface',
      accent: 'teal',
      className:
        'bg-teal-2 border-teal-7 text-teal-11 hover:bg-teal-3 focus-visible:outline-teal-9',
    },
    {
      variant: 'outline',
      accent: 'teal',
      className: 'border-teal-7 text-teal-11 hover:bg-teal-3 focus-visible:outline-teal-9',
    },
    {
      variant: 'ghost',
      accent: 'teal',
      className: 'text-teal-11 hover:bg-teal-3 focus-visible:outline-teal-9',
    },
    {
      variant: 'dashed',
      accent: 'teal',
      className: 'border-teal-7 text-teal-11 hover:bg-teal-3 focus-visible:outline-teal-9',
    },
    {
      variant: 'dim',
      accent: 'teal',
      className: 'text-teal-8 hover:text-teal-11 focus-visible:outline-teal-9',
    },

    // Jade
    {
      variant: 'solid',
      accent: 'jade',
      className: 'bg-jade-9 text-white hover:bg-jade-10 focus-visible:outline-jade-9',
    },
    {
      variant: 'soft',
      accent: 'jade',
      className: 'bg-jade-4 text-jade-11 hover:bg-jade-5 focus-visible:outline-jade-9',
    },
    {
      variant: 'surface',
      accent: 'jade',
      className:
        'bg-jade-2 border-jade-7 text-jade-11 hover:bg-jade-3 focus-visible:outline-jade-9',
    },
    {
      variant: 'outline',
      accent: 'jade',
      className: 'border-jade-7 text-jade-11 hover:bg-jade-3 focus-visible:outline-jade-9',
    },
    {
      variant: 'ghost',
      accent: 'jade',
      className: 'text-jade-11 hover:bg-jade-3 focus-visible:outline-jade-9',
    },
    {
      variant: 'dashed',
      accent: 'jade',
      className: 'border-jade-7 text-jade-11 hover:bg-jade-3 focus-visible:outline-jade-9',
    },
    {
      variant: 'dim',
      accent: 'jade',
      className: 'text-jade-8 hover:text-jade-11 focus-visible:outline-jade-9',
    },

    // Green
    {
      variant: 'solid',
      accent: 'green',
      className: 'bg-green-9 text-white hover:bg-green-10 focus-visible:outline-green-9',
    },
    {
      variant: 'soft',
      accent: 'green',
      className: 'bg-green-4 text-green-11 hover:bg-green-5 focus-visible:outline-green-9',
    },
    {
      variant: 'surface',
      accent: 'green',
      className:
        'bg-green-2 border-green-7 text-green-11 hover:bg-green-3 focus-visible:outline-green-9',
    },
    {
      variant: 'outline',
      accent: 'green',
      className: 'border-green-7 text-green-11 hover:bg-green-3 focus-visible:outline-green-9',
    },
    {
      variant: 'ghost',
      accent: 'green',
      className: 'text-green-11 hover:bg-green-3 focus-visible:outline-green-9',
    },
    {
      variant: 'dashed',
      accent: 'green',
      className: 'border-green-7 text-green-11 hover:bg-green-3 focus-visible:outline-green-9',
    },
    {
      variant: 'dim',
      accent: 'green',
      className: 'text-green-8 hover:text-green-11 focus-visible:outline-green-9',
    },

    // Grass
    {
      variant: 'solid',
      accent: 'grass',
      className: 'bg-grass-9 text-white hover:bg-grass-10 focus-visible:outline-grass-9',
    },
    {
      variant: 'soft',
      accent: 'grass',
      className: 'bg-grass-4 text-grass-11 hover:bg-grass-5 focus-visible:outline-grass-9',
    },
    {
      variant: 'surface',
      accent: 'grass',
      className:
        'bg-grass-2 border-grass-7 text-grass-11 hover:bg-grass-3 focus-visible:outline-grass-9',
    },
    {
      variant: 'outline',
      accent: 'grass',
      className: 'border-grass-7 text-grass-11 hover:bg-grass-3 focus-visible:outline-grass-9',
    },
    {
      variant: 'ghost',
      accent: 'grass',
      className: 'text-grass-11 hover:bg-grass-3 focus-visible:outline-grass-9',
    },
    {
      variant: 'dashed',
      accent: 'grass',
      className: 'border-grass-7 text-grass-11 hover:bg-grass-3 focus-visible:outline-grass-9',
    },
    {
      variant: 'dim',
      accent: 'grass',
      className: 'text-grass-8 hover:text-grass-11 focus-visible:outline-grass-9',
    },

    // Brown
    {
      variant: 'solid',
      accent: 'brown',
      className: 'bg-brown-9 text-white hover:bg-brown-10 focus-visible:outline-brown-9',
    },
    {
      variant: 'soft',
      accent: 'brown',
      className: 'bg-brown-4 text-brown-11 hover:bg-brown-5 focus-visible:outline-brown-9',
    },
    {
      variant: 'surface',
      accent: 'brown',
      className:
        'bg-brown-2 border-brown-7 text-brown-11 hover:bg-brown-3 focus-visible:outline-brown-9',
    },
    {
      variant: 'outline',
      accent: 'brown',
      className: 'border-brown-7 text-brown-11 hover:bg-brown-3 focus-visible:outline-brown-9',
    },
    {
      variant: 'ghost',
      accent: 'brown',
      className: 'text-brown-11 hover:bg-brown-3 focus-visible:outline-brown-9',
    },
    {
      variant: 'dashed',
      accent: 'brown',
      className: 'border-brown-7 text-brown-11 hover:bg-brown-3 focus-visible:outline-brown-9',
    },
    {
      variant: 'dim',
      accent: 'brown',
      className: 'text-brown-8 hover:text-brown-11 focus-visible:outline-brown-9',
    },

    // Bronze
    {
      variant: 'solid',
      accent: 'bronze',
      className: 'bg-bronze-9 text-white hover:bg-bronze-10 focus-visible:outline-bronze-9',
    },
    {
      variant: 'soft',
      accent: 'bronze',
      className: 'bg-bronze-4 text-bronze-11 hover:bg-bronze-5 focus-visible:outline-bronze-9',
    },
    {
      variant: 'surface',
      accent: 'bronze',
      className:
        'bg-bronze-2 border-bronze-7 text-bronze-11 hover:bg-bronze-3 focus-visible:outline-bronze-9',
    },
    {
      variant: 'outline',
      accent: 'bronze',
      className: 'border-bronze-7 text-bronze-11 hover:bg-bronze-3 focus-visible:outline-bronze-9',
    },
    {
      variant: 'ghost',
      accent: 'bronze',
      className: 'text-bronze-11 hover:bg-bronze-3 focus-visible:outline-bronze-9',
    },
    {
      variant: 'dashed',
      accent: 'bronze',
      className: 'border-bronze-7 text-bronze-11 hover:bg-bronze-3 focus-visible:outline-bronze-9',
    },
    {
      variant: 'dim',
      accent: 'bronze',
      className: 'text-bronze-8 hover:text-bronze-11 focus-visible:outline-bronze-9',
    },

    // Gold
    {
      variant: 'solid',
      accent: 'gold',
      className: 'bg-gold-9 text-gold-1 hover:bg-gold-10 focus-visible:outline-gold-9',
    },
    {
      variant: 'soft',
      accent: 'gold',
      className: 'bg-gold-4 text-gold-11 hover:bg-gold-5 focus-visible:outline-gold-9',
    },
    {
      variant: 'surface',
      accent: 'gold',
      className:
        'bg-gold-2 border-gold-7 text-gold-11 hover:bg-gold-3 focus-visible:outline-gold-9',
    },
    {
      variant: 'outline',
      accent: 'gold',
      className: 'border-gold-7 text-gold-11 hover:bg-gold-3 focus-visible:outline-gold-9',
    },
    {
      variant: 'ghost',
      accent: 'gold',
      className: 'text-gold-11 hover:bg-gold-3 focus-visible:outline-gold-9',
    },
    {
      variant: 'dashed',
      accent: 'gold',
      className: 'border-gold-7 text-gold-11 hover:bg-gold-3 focus-visible:outline-gold-9',
    },
    {
      variant: 'dim',
      accent: 'gold',
      className: 'text-gold-8 hover:text-gold-11 focus-visible:outline-gold-9',
    },

    // Sky
    {
      variant: 'solid',
      accent: 'sky',
      className: 'bg-sky-9 text-white hover:bg-sky-10 focus-visible:outline-sky-9',
    },
    {
      variant: 'soft',
      accent: 'sky',
      className: 'bg-sky-4 text-sky-11 hover:bg-sky-5 focus-visible:outline-sky-9',
    },
    {
      variant: 'surface',
      accent: 'sky',
      className: 'bg-sky-2 border-sky-7 text-sky-11 hover:bg-sky-3 focus-visible:outline-sky-9',
    },
    {
      variant: 'outline',
      accent: 'sky',
      className: 'border-sky-7 text-sky-11 hover:bg-sky-3 focus-visible:outline-sky-9',
    },
    {
      variant: 'ghost',
      accent: 'sky',
      className: 'text-sky-11 hover:bg-sky-3 focus-visible:outline-sky-9',
    },
    {
      variant: 'dashed',
      accent: 'sky',
      className: 'border-sky-7 text-sky-11 hover:bg-sky-3 focus-visible:outline-sky-9',
    },
    {
      variant: 'dim',
      accent: 'sky',
      className: 'text-sky-8 hover:text-sky-11 focus-visible:outline-sky-9',
    },

    // Mint
    {
      variant: 'solid',
      accent: 'mint',
      className: 'bg-mint-9 text-white hover:bg-mint-10 focus-visible:outline-mint-9',
    },
    {
      variant: 'soft',
      accent: 'mint',
      className: 'bg-mint-4 text-mint-11 hover:bg-mint-5 focus-visible:outline-mint-9',
    },
    {
      variant: 'surface',
      accent: 'mint',
      className:
        'bg-mint-2 border-mint-7 text-mint-11 hover:bg-mint-3 focus-visible:outline-mint-9',
    },
    {
      variant: 'outline',
      accent: 'mint',
      className: 'border-mint-7 text-mint-11 hover:bg-mint-3 focus-visible:outline-mint-9',
    },
    {
      variant: 'ghost',
      accent: 'mint',
      className: 'text-mint-11 hover:bg-mint-3 focus-visible:outline-mint-9',
    },
    {
      variant: 'dashed',
      accent: 'mint',
      className: 'border-mint-7 text-mint-11 hover:bg-mint-3 focus-visible:outline-mint-9',
    },
    {
      variant: 'dim',
      accent: 'mint',
      className: 'text-mint-8 hover:text-mint-11 focus-visible:outline-mint-9',
    },

    // Lime
    {
      variant: 'solid',
      accent: 'lime',
      className: 'bg-lime-9 text-lime-1 hover:bg-lime-10 focus-visible:outline-lime-9',
    },
    {
      variant: 'soft',
      accent: 'lime',
      className: 'bg-lime-4 text-lime-11 hover:bg-lime-5 focus-visible:outline-lime-9',
    },
    {
      variant: 'surface',
      accent: 'lime',
      className:
        'bg-lime-2 border-lime-7 text-lime-11 hover:bg-lime-3 focus-visible:outline-lime-9',
    },
    {
      variant: 'outline',
      accent: 'lime',
      className: 'border-lime-7 text-lime-11 hover:bg-lime-3 focus-visible:outline-lime-9',
    },
    {
      variant: 'ghost',
      accent: 'lime',
      className: 'text-lime-11 hover:bg-lime-3 focus-visible:outline-lime-9',
    },
    {
      variant: 'dashed',
      accent: 'lime',
      className: 'border-lime-7 text-lime-11 hover:bg-lime-3 focus-visible:outline-lime-9',
    },
    {
      variant: 'dim',
      accent: 'lime',
      className: 'text-lime-8 hover:text-lime-11 focus-visible:outline-lime-9',
    },

    // Yellow
    {
      variant: 'solid',
      accent: 'yellow',
      className: 'bg-yellow-9 text-yellow-1 hover:bg-yellow-10 focus-visible:outline-yellow-9',
    },
    {
      variant: 'soft',
      accent: 'yellow',
      className: 'bg-yellow-4 text-yellow-11 hover:bg-yellow-5 focus-visible:outline-yellow-9',
    },
    {
      variant: 'surface',
      accent: 'yellow',
      className:
        'bg-yellow-2 border-yellow-7 text-yellow-11 hover:bg-yellow-3 focus-visible:outline-yellow-9',
    },
    {
      variant: 'outline',
      accent: 'yellow',
      className: 'border-yellow-7 text-yellow-11 hover:bg-yellow-3 focus-visible:outline-yellow-9',
    },
    {
      variant: 'ghost',
      accent: 'yellow',
      className: 'text-yellow-11 hover:bg-yellow-3 focus-visible:outline-yellow-9',
    },
    {
      variant: 'dashed',
      accent: 'yellow',
      className: 'border-yellow-7 text-yellow-11 hover:bg-yellow-3 focus-visible:outline-yellow-9',
    },
    {
      variant: 'dim',
      accent: 'yellow',
      className: 'text-yellow-8 hover:text-yellow-11 focus-visible:outline-yellow-9',
    },

    // Amber
    {
      variant: 'solid',
      accent: 'amber',
      className: 'bg-amber-9 text-amber-1 hover:bg-amber-10 focus-visible:outline-amber-9',
    },
    {
      variant: 'soft',
      accent: 'amber',
      className: 'bg-amber-4 text-amber-11 hover:bg-amber-5 focus-visible:outline-amber-9',
    },
    {
      variant: 'surface',
      accent: 'amber',
      className:
        'bg-amber-2 border-amber-7 text-amber-11 hover:bg-amber-3 focus-visible:outline-amber-9',
    },
    {
      variant: 'outline',
      accent: 'amber',
      className: 'border-amber-7 text-amber-11 hover:bg-amber-3 focus-visible:outline-amber-9',
    },
    {
      variant: 'ghost',
      accent: 'amber',
      className: 'text-amber-11 hover:bg-amber-3 focus-visible:outline-amber-9',
    },
    {
      variant: 'dashed',
      accent: 'amber',
      className: 'border-amber-7 text-amber-11 hover:bg-amber-3 focus-visible:outline-amber-9',
    },
    {
      variant: 'dim',
      accent: 'amber',
      className: 'text-amber-8 hover:text-amber-11 focus-visible:outline-amber-9',
    },

    // Orange
    {
      variant: 'solid',
      accent: 'orange',
      className: 'bg-orange-9 text-white hover:bg-orange-10 focus-visible:outline-orange-9',
    },
    {
      variant: 'soft',
      accent: 'orange',
      className: 'bg-orange-4 text-orange-11 hover:bg-orange-5 focus-visible:outline-orange-9',
    },
    {
      variant: 'surface',
      accent: 'orange',
      className:
        'bg-orange-2 border-orange-7 text-orange-11 hover:bg-orange-3 focus-visible:outline-orange-9',
    },
    {
      variant: 'outline',
      accent: 'orange',
      className: 'border-orange-7 text-orange-11 hover:bg-orange-3 focus-visible:outline-orange-9',
    },
    {
      variant: 'ghost',
      accent: 'orange',
      className: 'text-orange-11 hover:bg-orange-3 focus-visible:outline-orange-9',
    },
    {
      variant: 'dashed',
      accent: 'orange',
      className: 'border-orange-7 text-orange-11 hover:bg-orange-3 focus-visible:outline-orange-9',
    },
    {
      variant: 'dim',
      accent: 'orange',
      className: 'text-orange-8 hover:text-orange-11 focus-visible:outline-orange-9',
    },
  ],
  /**
   * @description Valores padrão para as variantes do botão.
   */
  defaultVariants: {
    variant: 'solid',
    accent: 'primary',
    size: 'md',
    radius: 'md',
    mode: 'default',
    appearance: 'default',
    shape: 'default',
    autoHeight: false,
    isLoading: false,
    placeholder: false,
  },
});

/**
 * @description Propriedades para o componente Button.
 * Estende as propriedades de variantes do `buttonStyles`, atributos HTML de botão
 * e propriedades de renderização (`useRender.ComponentProps`).
 */
export interface ButtonProps
  extends VariantProps<typeof buttonStyles>,
    Omit<React.ButtonHTMLAttributes<HTMLButtonElement>, 'disabled'>,
    useRender.ComponentProps<'button'> {
  /**
   * Texto a ser exibido quando o botão está no estado `isLoading`.
   */
  pendingText?: string;
  /**
   * Controla o estado desabilitado do botão. Prevalece sobre `isLoading` se ambos forem `true`.
   */
  isDisabled?: boolean;
  /**
   * Suporte ao composition pattern do Radix.
   */
  asChild?: boolean;
  /**
   * Indica se o botão está selecionado (para estados visuais).
   */
  selected?: boolean;

  // Responsive variants
  /**
   * @description Define o `variant` do botão para telas de desktop (>= 768px).
   * Se definido, substitui a prop `variant` padrão em desktop.
   */
  desktopVariant?: VariantProps<typeof buttonStyles>['variant'];
  /**
   * @description Define o `variant` do botão para telas móveis (< 768px).
   * Se definido, substitui a prop `variant` padrão em mobile.
   */
  mobileVariant?: VariantProps<typeof buttonStyles>['variant'];
  /**
   * @description Define o `accent` do botão para telas de desktop (>= 768px).
   * Se definido, substitui a prop `accent` padrão em desktop.
   */
  desktopAccent?: VariantProps<typeof buttonStyles>['accent'];
  /**
   * @description Define o `accent` do botão para telas móveis (< 768px).
   * Se definido, substitui a prop `accent` padrão em mobile.
   */
  mobileAccent?: VariantProps<typeof buttonStyles>['accent'];
  /**
   * @description Define o `size` do botão para telas de desktop (>= 768px).
   * Se definido, substitui a prop `size` padrão em desktop.
   */
  desktopSize?: VariantProps<typeof buttonStyles>['size'];
  /**
   * @description Define o `size` do botão para telas móveis (< 768px).
   * Se definido, substitui a prop `size` padrão em mobile.
   */
  mobileSize?: VariantProps<typeof buttonStyles>['size'];
  /**
   * @description Define o `mode` do botão para telas de desktop (>= 768px).
   * Se definido, substitui a prop `mode` padrão em desktop.
   */
  desktopMode?: VariantProps<typeof buttonStyles>['mode'];
  /**
   * @description Define o `mode` do botão para telas móveis (< 768px).
   * Se definido, substitui a prop `mode` padrão em mobile.
   */
  mobileMode?: VariantProps<typeof buttonStyles>['mode'];
  /**
   * @description Define o `appearance` do botão para telas de desktop (>= 768px).
   * Se definido, substitui a prop `appearance` padrão em desktop.
   */
  desktopAppearance?: VariantProps<typeof buttonStyles>['appearance'];
  /**
   * @description Define o `appearance` do botão para telas móveis (< 768px).
   * Se definido, substitui a prop `appearance` padrão em mobile.
   */
  mobileAppearance?: VariantProps<typeof buttonStyles>['appearance'];

  // Backward compatibility (deprecated)
  /**
   * @deprecated Use `isLoading` instead
   */
  isPending?: boolean;
}

// =================================================================================
// COMPONENTE
// =================================================================================

/**
 * @description Componente de botão flexível e estilizado com `tailwind-variants`.
 * Suporta diferentes variantes visuais, esquemas de cores, tamanhos, raios de borda,
 * estados de carregamento e configurações responsivas.
 * Permite renderização customizada de elementos internos através da prop `render`.
 */
const Button = forwardRef<HTMLButtonElement, ButtonProps>(
  (
    {
      className,
      variant,
      accent,
      size,
      radius,
      mode,
      appearance,
      shape,
      autoHeight,
      isLoading,
      underline,
      underlined,
      placeholder,
      pendingText,
      desktopVariant,
      mobileVariant,
      desktopAccent,
      mobileAccent,
      desktopSize,
      mobileSize,
      desktopMode,
      mobileMode,
      desktopAppearance,
      mobileAppearance,
      children,
      // biome-ignore lint/a11y/useButtonType: <explanation>
      render = <button />, // Default render element is a button without forcing type
      isDisabled,
      asChild = false,
      selected,
      isPending, // Backward compatibility
      ...otherProps
    },
    forwardedRef,
  ) => {
    /**
     * @description Hook para detectar se a tela atual é desktop (largura mínima de 768px).
     * Usado para aplicar variantes responsivas.
     */
    const isDesktop = useMediaQuery('(min-width: 768px)');

    // Backward compatibility
    const finalIsLoading = isLoading ?? isPending ?? false;

    /**
     * @description Determina a variante final do botão. Prioriza a variante desktop/mobile
     * com base na largura da tela, caso contrário, usa a variante padrão.
     */
    const finalVariant =
      desktopVariant || mobileVariant
        ? isDesktop
          ? desktopVariant || variant
          : mobileVariant || variant
        : variant;

    /**
     * @description Determina o esquema de cores final do botão. Prioriza o accent desktop/mobile
     * com base na largura da tela, caso contrário, usa o accent padrão.
     */
    const finalAccent =
      desktopAccent || mobileAccent
        ? isDesktop
          ? desktopAccent || accent
          : mobileAccent || accent
        : accent;

    /**
     * @description Determina o tamanho final do botão. Prioriza o tamanho desktop/mobile
     * com base na largura da tela, caso contrário, usa o tamanho padrão.
     */
    const finalSize =
      desktopSize || mobileSize ? (isDesktop ? desktopSize || size : mobileSize || size) : size;

    /**
     * @description Determina o modo final do botão. Prioriza o modo desktop/mobile
     * com base na largura da tela, caso contrário, usa o modo padrão.
     */
    const finalMode =
      desktopMode || mobileMode ? (isDesktop ? desktopMode || mode : mobileMode || mode) : mode;

    /**
     * @description Determina a aparência final do botão. Prioriza a aparência desktop/mobile
     * com base na largura da tela, caso contrário, usa a aparência padrão.
     */
    const finalAppearance =
      desktopAppearance || mobileAppearance
        ? isDesktop
          ? desktopAppearance || appearance
          : mobileAppearance || appearance
        : appearance;

    /**
     * @description Propriedades padrão e calculadas aplicadas ao elemento subjacente do botão.
     * Inclui classes de estilo geradas por `buttonStyles` e a lógica de `disabled`.
     */
    const defaultProps = {
      type: 'button', // Default type for buttons (can be overridden by otherProps)
      'data-slot': 'button', // Atributo para identificar o slot do componente
      className: cn(
        buttonStyles({
          variant: finalVariant,
          accent: finalAccent,
          size: finalSize,
          radius,
          mode: finalMode,
          appearance: finalAppearance,
          shape,
          autoHeight,
          isLoading: finalIsLoading,
          underline,
          underlined,
          placeholder,
          className,
        }),
        asChild && (isDisabled || finalIsLoading) && 'pointer-events-none opacity-50',
      ),
      disabled: isDisabled || finalIsLoading, // O botão é desabilitado se isDisabled ou isLoading for true
      ...(selected && { 'data-state': 'open' as const }),
    } as const;

    /**
     * @description Gerencia a exibição do `Loader` e `pendingText` quando o botão está em estado `isLoading`.
     */
    const buttonChildren = (
      <>
        {finalIsLoading && <Loader data-slot="loader" />}{' '}
        {/* Exibe o loader se estiver carregando */}
        {finalIsLoading && pendingText ? pendingText : children}{' '}
        {/* Exibe o texto pendente ou os filhos normais */}
      </>
    );

    // Determine render element based on asChild prop
    const renderElement =
      asChild && React.isValidElement(children)
        ? (children as React.ReactElement<
            Record<string, unknown>,
            string | React.JSXElementConstructor<unknown>
          >)
        : // biome-ignore lint/a11y/useButtonType: <explanation>
          render || <button />;
    Button;
    // When using asChild, children becomes the element props, otherwise use children normally
    const finalProps =
      asChild && React.isValidElement(children)
        ? mergeProps(defaultProps, otherProps)
        : mergeProps(defaultProps, { ...otherProps, children: buttonChildren });

    /**
     * @description Utiliza o hook `useRender` para renderizar o elemento do botão.
     * Permite que o elemento base seja substituído via a prop `render` ou `asChild`.
     */
    const element = useRender({
      render: renderElement,
      props: finalProps,
      ref: forwardedRef,
    });

    return element;
  },
);

Button.displayName = 'Button';

// ButtonArrow component for dropdown arrows
interface ButtonArrowProps extends React.SVGProps<SVGSVGElement> {
  icon?: React.ComponentType<React.SVGProps<SVGSVGElement>>;
}

function ButtonArrow({
  icon: Icon = ({ className, ...props }) => (
    <svg
      aria-hidden="true"
      className={className}
      {...props}
      viewBox="0 0 24 24"
      fill="none"
      stroke="currentColor"
      strokeWidth="2"
    >
      <path d="m6 9 6 6 6-6" />
    </svg>
  ),
  className,
  ...props
}: ButtonArrowProps) {
  return <Icon data-slot="button-arrow" className={cn('ms-auto -me-1', className)} {...props} />;
}

export { Button, ButtonArrow, buttonStyles, buttonStyles as buttonVariants };
```

---

## 📂 `button-group.tsx`

Caminho original no repositório: `/packages/ui/src/components/button-group.tsx`

```tsx
import { mergeProps } from '@base-ui/react/merge-props';
import { useRender } from '@base-ui/react/use-render';
import { type VariantProps, cva } from 'class-variance-authority';

import { cn } from '@manager/lib';
import { Separator } from './separator';

const buttonGroupVariants = cva(
  "has-[>[data-slot=button-group]]:gap-2 has-[select[aria-hidden=true]:last-child]:[&>[data-slot=select-trigger]:last-of-type]:rounded-r-md flex w-fit items-stretch [&>*]:focus-visible:z-10 [&>*]:focus-visible:relative [&>[data-slot=select-trigger]:not([class*='w-'])]:w-fit [&>input]:flex-1",
  {
    variants: {
      orientation: {
        horizontal:
          '[&>[data-slot]~[data-slot]]:rounded-l-none [&>[data-slot]~[data-slot]]:border-l-0 [&>[data-slot]]:rounded-r-none',
        vertical:
          'flex-col [&>[data-slot]~[data-slot]]:rounded-t-none [&>[data-slot]~[data-slot]]:border-t-0 [&>[data-slot]]:rounded-b-none',
      },
      shape: {
        default: '',
        pill: '[&>[data-slot]:first-child]:rounded-l-full [&>[data-slot]:not(:has(~[data-slot]))]:rounded-r-full!',
      },
    },
    compoundVariants: [
      {
        orientation: 'horizontal',
        shape: 'default',
        className: '[&>[data-slot]:not(:has(~[data-slot]))]:rounded-r-md!',
      },
      {
        orientation: 'vertical',
        shape: 'default',
        className: '[&>[data-slot]:not(:has(~[data-slot]))]:rounded-b-md!',
      },
    ],
    defaultVariants: {
      orientation: 'horizontal',
      shape: 'default',
    },
  },
);

function ButtonGroup({
  className,
  orientation,
  shape,
  ...props
}: React.ComponentProps<'div'> & VariantProps<typeof buttonGroupVariants>) {
  return (
    <div
      role="group"
      data-slot="button-group"
      data-orientation={orientation}
      className={cn(buttonGroupVariants({ orientation, shape }), className)}
      {...props}
    />
  );
}

function ButtonGroupText({ className, render, ...props }: useRender.ComponentProps<'div'>) {
  return useRender({
    defaultTagName: 'div',
    props: mergeProps<'div'>(
      {
        className: cn(
          "bg-zinc-100 gap-2 rounded-md border border-zinc-200 px-2.5 text-sm font-medium shadow-xs [&_svg:not([class*='size-'])]:size-4 flex items-center [&_svg]:pointer-events-none dark:bg-zinc-800 dark:border-zinc-800",
          className,
        ),
      },
      props,
    ),
    render,
    state: {
      slot: 'button-group-text',
    },
  });
}

function ButtonGroupSeparator({
  className,
  orientation = 'vertical',
  ...props
}: React.ComponentProps<typeof Separator>) {
  return (
    <Separator
      data-slot="button-group-separator"
      orientation={orientation}
      className={cn(
        'bg-zinc-200 relative self-stretch data-[orientation=horizontal]:mx-px data-[orientation=horizontal]:w-auto data-[orientation=vertical]:my-px data-[orientation=vertical]:h-auto dark:bg-zinc-800',
        className,
      )}
      {...props}
    />
  );
}

export { ButtonGroup, ButtonGroupSeparator, ButtonGroupText, buttonGroupVariants };
```

---

## 📂 `toggle.tsx`

Caminho original no repositório: `/packages/ui/src/components/toggle.tsx`

```tsx
'use client';

import { Toggle as TogglePrimitive } from '@base-ui/react/toggle';
import { type VariantProps, cva } from 'class-variance-authority';

import { cn } from '@manager/lib';

const toggleVariants = cva(
  "hover:text-zinc-950 aria-pressed:bg-zinc-100 focus-visible:border-zinc-950 focus-visible:ring-zinc-950/50 aria-invalid:ring-red-500/20 dark:aria-invalid:ring-red-500/40 aria-invalid:border-red-500 gap-1 rounded-md text-sm font-medium transition-[color,box-shadow] [&_svg:not([class*='size-'])]:size-4 group/toggle hover:bg-zinc-100 inline-flex items-center justify-center whitespace-nowrap outline-none focus-visible:ring-[3px] disabled:pointer-events-none disabled:opacity-50 [&_svg]:pointer-events-none [&_svg]:shrink-0 dark:hover:text-zinc-50 dark:aria-pressed:bg-zinc-800 dark:focus-visible:border-zinc-300 dark:focus-visible:ring-zinc-300/50 dark:aria-invalid:ring-red-900/20 dark:dark:aria-invalid:ring-red-900/40 dark:aria-invalid:border-red-900 dark:hover:bg-zinc-800",
  {
    variants: {
      variant: {
        default: 'bg-transparent',
        outline:
          'border-zinc-200 hover:bg-zinc-100 border bg-transparent shadow-xs dark:border-zinc-800 dark:hover:bg-zinc-800',
      },
      size: {
        default: 'h-9 min-w-9 px-2',
        sm: 'h-8 min-w-8 px-1.5',
        lg: 'h-10 min-w-10 px-2.5',
      },
    },
    defaultVariants: {
      variant: 'default',
      size: 'default',
    },
  },
);

function Toggle({
  className,
  variant = 'default',
  size = 'default',
  ...props
}: TogglePrimitive.Props & VariantProps<typeof toggleVariants>) {
  return (
    <TogglePrimitive
      data-slot="toggle"
      className={cn(toggleVariants({ variant, size, className }))}
      {...props}
    />
  );
}

export { Toggle, toggleVariants };
```

---

## 📂 `toggle-group.tsx`

Caminho original no repositório: `/packages/ui/src/components/toggle-group.tsx`

```tsx
import { Toggle as TogglePrimitive } from '@base-ui/react/toggle';
import { ToggleGroup as ToggleGroupPrimitive } from '@base-ui/react/toggle-group';
import type { VariantProps } from 'class-variance-authority';
import * as React from 'react';

import { cn } from '@manager/lib';
import { toggleVariants } from './toggle';

const ToggleGroupContext = React.createContext<
  VariantProps<typeof toggleVariants> & {
    spacing?: number;
    orientation?: 'horizontal' | 'vertical';
  }
>({
  size: 'default',
  variant: 'default',
  spacing: 0,
  orientation: 'horizontal',
});

function ToggleGroup({
  className,
  variant,
  size,
  spacing = 0,
  orientation = 'horizontal',
  children,
  ...props
}: ToggleGroupPrimitive.Props &
  VariantProps<typeof toggleVariants> & {
    spacing?: number;
    orientation?: 'horizontal' | 'vertical';
  }) {
  return (
    <ToggleGroupPrimitive
      data-slot="toggle-group"
      data-variant={variant}
      data-size={size}
      data-spacing={spacing}
      data-orientation={orientation}
      style={{ '--gap': spacing } as React.CSSProperties}
      className={cn(
        'rounded-md data-[spacing=0]:data-[variant=outline]:shadow-xs group/toggle-group flex w-fit flex-row items-center gap-[--spacing(var(--gap))] data-[orientation=vertical]:flex-col data-[orientation=vertical]:items-stretch',
        className,
      )}
      {...props}
    >
      <ToggleGroupContext.Provider value={{ variant, size, spacing, orientation }}>
        {children}
      </ToggleGroupContext.Provider>
    </ToggleGroupPrimitive>
  );
}

function ToggleGroupItem({
  className,
  children,
  variant = 'default',
  size = 'default',
  ...props
}: TogglePrimitive.Props & VariantProps<typeof toggleVariants>) {
  const context = React.useContext(ToggleGroupContext);

  return (
    <TogglePrimitive
      data-slot="toggle-group-item"
      data-variant={context.variant || variant}
      data-size={context.size || size}
      data-spacing={context.spacing}
      className={cn(
        'data-[state=on]:bg-zinc-100 group-data-[spacing=0]/toggle-group:rounded-none group-data-[spacing=0]/toggle-group:px-2 group-data-[spacing=0]/toggle-group:shadow-none group-data-horizontal/toggle-group:data-[spacing=0]:first:rounded-l-md group-data-vertical/toggle-group:data-[spacing=0]:first:rounded-t-md group-data-horizontal/toggle-group:data-[spacing=0]:last:rounded-r-md group-data-vertical/toggle-group:data-[spacing=0]:last:rounded-b-md shrink-0 focus:z-10 focus-visible:z-10 group-data-horizontal/toggle-group:data-[spacing=0]:data-[variant=outline]:border-l-0 group-data-vertical/toggle-group:data-[spacing=0]:data-[variant=outline]:border-t-0 group-data-horizontal/toggle-group:data-[spacing=0]:data-[variant=outline]:first:border-l group-data-vertical/toggle-group:data-[spacing=0]:data-[variant=outline]:first:border-t dark:data-[state=on]:bg-zinc-800',
        toggleVariants({
          variant: context.variant || variant,
          size: context.size || size,
        }),
        className,
      )}
      {...props}
    >
      {children}
    </TogglePrimitive>
  );
}

export { ToggleGroup, ToggleGroupItem };
```

---

## 📂 `kbd.tsx`

Caminho original no repositório: `/packages/ui/src/components/kbd.tsx`

```tsx
import { cn } from '@manager/lib';

function Kbd({ className, ...props }: React.ComponentProps<'kbd'>) {
  return (
    <kbd
      data-slot="kbd"
      className={cn(
        "bg-zinc-100 text-zinc-500 [[data-slot=tooltip-content]_&]:bg-white/20 [[data-slot=tooltip-content]_&]:text-white dark:[[data-slot=tooltip-content]_&]:bg-white/10 h-5 w-fit min-w-5 gap-1 rounded-sm px-1 font-sans text-xs font-medium [&_svg:not([class*='size-'])]:size-3 pointer-events-none inline-flex items-center justify-center select-none dark:bg-zinc-800 dark:text-zinc-400 dark:[[data-slot=tooltip-content]_&]:bg-zinc-950/20 dark:[[data-slot=tooltip-content]_&]:text-zinc-950 dark:dark:[[data-slot=tooltip-content]_&]:bg-zinc-950/10",
        className,
      )}
      {...props}
    />
  );
}

function KbdGroup({ className, ...props }: React.ComponentProps<'div'>) {
  return (
    <kbd
      data-slot="kbd-group"
      className={cn('gap-1 inline-flex items-center', className)}
      {...props}
    />
  );
}

export { Kbd, KbdGroup };
```

---

