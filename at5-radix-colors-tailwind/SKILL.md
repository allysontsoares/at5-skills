---
name: at5-radix-colors-tailwind
description: Design token architecture integrating Radix Colors with Tailwind CSS v4 for AT5 projects. Use when setting up color tokens, dark mode toggle, brand color scale, semantic color palettes (error, success, warning), or OKLCH color values. Always trigger on keywords like Radix Colors, @radix-ui/colors, @theme, dark mode, color scale, mauve, slate, brand color, oklch, tailwind colors, or prefers-color-scheme.
---

# at5-radix-colors — Design Tokens de Cores com Radix Colors e Tailwind v4.0

Esta skill autônoma documenta a arquitetura de **Design Tokens de Cores** no ecossistema **AT5**, integrando a escala **Radix Colors** nativamente com o **Tailwind CSS v4.0**. Ela define a escala de steps de 1 a 12, a conversão para o espaço de cores perceptualmente uniforme **OKLCH**, o suporte nativo a temas automáticos claro/escuro (`light`/`dark`) e a proibição explícita de classes arbitrárias de cores clássicas do Tailwind (ex: `bg-blue-600`).

---

## 🎨 1. A Filosofia das Cores AT5

O design system **AT5** adota uma abordagem estrita baseada nas escalas profissionais da Radix Colors. Isso garante contraste acessível (compatível com WCAG AA/AAA), suavidade visual, harmonia cromática e suporte perfeito a temas escuros sem a necessidade de duplicar classes utilitárias no HTML (como `dark:bg-slate-900`).

### ⛔ Regras Estritas de Implementação (Proibições)
1. **PROIBIDO** o uso de cores do Tailwind clássico como `bg-blue-500`, `text-slate-800`, `border-gray-200`, `hover:bg-red-600`.
2. **PROIBIDO** o uso de cores hexadecimais ou RGB puras (`#ffffff`, `rgb(0,0,0)`) diretamente em classes utilitárias ou estilos inline ad-hoc.
3. **MANDATÓRIO** o uso exclusivo de tokens semânticos mapeados nas escalas Radix de 1 a 12 (ex: `bg-slate-1`, `text-slate-12`, `border-slate-6`).
4. **MANDATÓRIO** o uso de variáveis CSS nativas no bloco `@theme` do Tailwind v4.0.

---

## 📐 2. A Semântica dos Steps de 1 a 12

Cada escala de cor da Radix possui exatamente 12 tons (steps) com propósitos de design rigorosos. Compreender e respeitar esses steps é vital para a consistência estética e acessibilidade.

| Step | Uso Principal | Descrição e Comportamento | Exemplos de Classes |
| :--- | :--- | :--- | :--- |
| **1** | **App Background** | Fundo principal da aplicação. Estritamente fosco e sem brilho. | `bg-slate-1`, `bg-brand-1` |
| **2** | **Subtle Background** | Fundos sutis. Usado em sidebars, cards destacados ou tabelas com listras. | `bg-slate-2`, `bg-brand-2` |
| **3** | **UI Background Normal** | Fundo de componentes interativos normais (inputs, select triggers, botões secundários). | `bg-slate-3`, `bg-brand-3` |
| **4** | **UI Background Hover** | Fundo de componentes interativos no estado hover. | `hover:bg-slate-4` |
| **5** | **UI Background Active** | Fundo de componentes interativos no estado pressionado ou selecionado. | `active:bg-slate-5`, `data-[state=active]:bg-slate-5` |
| **6** | **Subtle Border** | Bordas sutis. Divisórias, bordas de cards ou separadores sutis. | `border-slate-6`, `border-brand-6` |
| **7** | **Strong Border** | Bordas de componentes interativos em estado de repouso ou focus ring sutil. | `border-slate-7`, `border-brand-7` |
| **8** | **Hover Border** | Bordas de componentes interativos focados ou em hover. | `hover:border-slate-8`, `focus:border-brand-8` |
| **9** | **Solid Background** | Fundo sólido forte. Botões primários, badges sólidos, ícones importantes ou estados ativos de status. | `bg-slate-9`, `bg-brand-9` |
| **10** | **Hover Solid Background** | Fundo sólido forte em estado hover. | `hover:bg-slate-10`, `hover:bg-brand-10` |
| **11** | **Low-Contrast Text** | Texto secundário, descrições secundárias ou labels de menor prioridade. | `text-slate-11`, `text-brand-11` |
| **12** | **High-Contrast Text** | Texto principal, títulos primários e conteúdo de alto destaque. | `text-slate-12`, `text-brand-12` |

---

## 🛠️ 3. Configuração do `index.css` no Tailwind v4.0

> [!IMPORTANT]
> **Fonte da verdade**: O `apps/web/src/index.css` do projeto já contém a configuração real e completa. Esta seção documenta o **padrão arquitetural** para novos projetos. Não recrie valores manualmente — use sempre o pacote `@radix-ui/colors`.

### 3.1 Abordagem Correta: Importar do Pacote `@radix-ui/colors`

Nunca defina valores de cores manualmente com OKLCH aproximados. Use os arquivos CSS oficiais do pacote, que contêm os valores exatos e já incluem suporte a temas claro/escuro automaticamente:

```css
@import 'tailwindcss';

/* Escalas neutras */
@import '@radix-ui/colors/mauve.css';
@import '@radix-ui/colors/mauve-dark.css';
@import '@radix-ui/colors/mauve-alpha.css';
@import '@radix-ui/colors/mauve-dark-alpha.css';

@import '@radix-ui/colors/slate.css';
@import '@radix-ui/colors/slate-dark.css';

/* Escalas de feedback */
@import '@radix-ui/colors/red.css';
@import '@radix-ui/colors/red-dark.css';

@import '@radix-ui/colors/green.css';
@import '@radix-ui/colors/green-dark.css';

@import '@radix-ui/colors/amber.css';
@import '@radix-ui/colors/amber-dark.css';

/* Ativa a variante dark via classe .dark (ThemeProvider) */
@custom-variant dark (&:is(.dark *));
```

### 3.2 Dark Mode: Classe `.dark` (não `prefers-color-scheme`)

O AT5 usa **toggle manual via classe** para dark mode, controlado pelo `ThemeProvider` + `useThemeStore`. O Radix Colors dark já é aplicado automaticamente pelo `@radix-ui/colors/*-dark.css` sob a classe `.dark`:

```css
/* ✅ CORRETO — toggle por classe (ThemeProvider) */
@custom-variant dark (&:is(.dark *));

/* ❌ NÃO USAR para AT5 — automático pelo sistema operacional */
/* @media (prefers-color-scheme: dark) { ... } */
```

### 3.3 Mapeamento de Cores Customizadas no `@theme`

Apenas cores **fora do Radix padrão** (ex: brand customizado) precisam ser definidas no `@theme`. As escalas Radix (mauve, slate, etc.) são mapeadas via `tailwind.config.js` apontando para as variáveis CSS do pacote (`--mauve-N`, `--slate-N`):

```css
@theme {
  /* Escala brand customizada (ex: teal do projeto) */
  --color-brand-1:  var(--brand-1);
  --color-brand-2:  var(--brand-2);
  /* ... até --color-brand-12 */
}

/* Valores brand no tema claro */
:root, .light, .light-theme {
  --brand-9: oklch(66% 0.1217 221.7); /* cor principal da identidade */
  /* ... demais steps */
}

/* Valores brand no tema escuro */
.dark, .dark-theme {
  --brand-9: oklch(66% 0.1217 221.7); /* geralmente igual ao light */
  /* ... demais steps */
}
```

### 3.4 Mapeamento no `tailwind.config.js`

Para que classes como `bg-mauve-6`, `text-slate-12`, `border-red-7` funcionem, mapeie as escalas Radix no config:

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
        // brand é mapeado via @theme no index.css
      },
    },
  },
};
```

---

## 🛠️ 4. Guia de Utilização Prática de Cores

Abaixo estão os padrões semânticos de estilização para manter os componentes consistentes, elegantes e vivos em qualquer tema.

### 4.1 Background Geral e Cards
```tsx
// Página inteira com fundo principal do app
<div className="min-h-screen bg-slate-1 text-slate-12">
  
  {/* Card em destaque usando fundo secundário e bordas sutis */}
  <div className="bg-slate-2 border border-slate-6 rounded-lg p-6 shadow-sm">
    <h2 className="text-lg font-semibold text-slate-12">Configurações Gerais</h2>
    <p className="text-sm text-slate-11 mt-1">Gerencie as preferências globais do monorepo.</p>
  </div>

</div>
```

### 4.2 Componentes de Formulário (Inputs, Selects)
```tsx
// Inputs com transições de foco elegantes e bordas adaptativas
<input
  className="w-full h-10 px-3 rounded-md bg-slate-3 border border-slate-7 text-slate-12 placeholder-slate-11 focus:outline-none focus:border-brand-8 focus:ring-1 focus:ring-brand-8 transition-colors"
  placeholder="Digite seu nome..."
/>
```

### 4.3 Botões de Ação (Primário, Secundário, Destrutivo)
```tsx
// Botão Primário (Forte)
<button className="h-10 px-4 rounded-md bg-brand-9 hover:bg-brand-10 text-white font-medium shadow-sm active:scale-[0.98] transition-all">
  Confirmar Ação
</button>

// Botão Secundário (Sutil)
<button className="h-10 px-4 rounded-md bg-slate-3 hover:bg-slate-4 text-slate-12 border border-slate-6 active:scale-[0.98] transition-all">
  Cancelar
</button>

// Botão de Alerta Destrutivo (Perigo)
<button className="h-10 px-4 rounded-md bg-error-9 hover:bg-error-10 text-white font-medium shadow-sm active:scale-[0.98] transition-all">
  Excluir Registro
</button>
```

---

## ⚙️ 5. Como Validar e Auditar

Sempre que a IA ou o desenvolvedor fizerem alterações no design de cores, execute e audite os seguintes pontos:
1. **Verificação de Contraste**: Garanta que qualquer texto sobre fundo atinja no mínimo `4.5:1` de contraste (escala 11 ou 12 sobre backgrounds 1, 2 ou 3).
2. **Auditoria de Variáveis**: Certifique-se de que não há declarações CSS no escopo do projeto contendo `#` ou `rgb` literais para cores de interface.
3. **Validação do Tema Escuro**: Desative as cores do sistema e teste a transição suave de `prefers-color-scheme`.
