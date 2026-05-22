---
name: at5-frontend-stack
description: Master guide and orchestrator for the AT5 frontend stack. Use this skill when setting up or working on any frontend project with Vite 8+, React 19, React Compiler, Tailwind CSS v4, Base UI (@base-ui/react), ofetch, NiceModal, Zod, or React Hook Form. Always trigger when the user asks about frontend architecture, project structure, responsive layout, accessibility (a11y), data fetching patterns, monorepo setup, or iOS/Safari compatibility. Also activates the specialized sub-skills: at5-ui-components, at5-nicemodal, at5-radix-colors-tailwind, and at5-toasts.
---

# at5-frontend-stack — Guia Mestre e Orquestrador Frontend da AT5

Este é o guia definitivo de arquitetura, padrões e engenharia de software para o desenvolvimento frontend no ecossistema **AT5**. Ele atua como orquestrador principal e "fonte da verdade" para guiar agentes de IA e desenvolvedores a criarem códigos consistentes, modernos, acessíveis e altamente otimizados.

---

## 🚀 1. Stack Tecnológica Core

Qualquer projeto novo ou existente que siga a arquitetura AT5 deve adotar estritamente as seguintes tecnologias e configurações de última geração:

1. **Vite 8+**: Motor de build ultra-rápido para desenvolvimento local e bundling de produção.
2. **React 19**: Versão estável do React que introduz suporte nativo a Actions, `use` API e melhorias significativas de performance.
3. **React Compiler**: O compilador automático de React. **Requer `@vitejs/plugin-react` (Babel) — incompatível com a variante SWC**. Configure no `vite.config.ts` via `babel-plugin-react-compiler`:
   ```ts
   import react from '@vitejs/plugin-react';
   // vite.config.ts
   plugins: [react({ babel: { plugins: [['babel-plugin-react-compiler']] } })]
   ```
   Dispensa a necessidade de memoização manual excessiva com `useMemo` e `useCallback` na grande maioria dos casos.
4. **Tailwind CSS v4.0**: A nova era do Tailwind baseada em motor nativo em Rust, sem arquivo `tailwind.config.js` redundante, onde todo o setup é feito diretamente no CSS de entrada (`index.css`) via variáveis CSS nativas.
5. **Base UI (`@base-ui/react`)**: Biblioteca de componentes não estilizados (headless) de altíssima acessibilidade, focada em performance e conformidade com os padrões da W3C.
6. **Zod**: Biblioteca de validação de schemas TypeScript-first, usada como única fonte de verdade para contratos de API e formulários.
7. **React Hook Form**: Orquestrador de formulários leve e baseado em refs, integrado com validação Zod.
8. **ofetch**: Cliente HTTP ultra-leve, resiliente, compatível com Node/Browser, que substitui o Axios com melhor suporte a timeouts, retry e tratamento nativo de tokens.
9. **NiceModal**: Gerenciador declarativo de modais/diálogos baseado em estado do React que permite criar e abrir diálogos sem poluir a árvore JSX local. **Requer** envolver o `<App>` com `<NiceModal.Provider>`:
   ```tsx
   import NiceModal from '@ebay/nice-modal-react';
   // main.tsx ou App.tsx raiz
   <NiceModal.Provider>
     <App />
   </NiceModal.Provider>
   ```

---

## 📦 2. Estrutura do Monorepo e Contratos

A AT5 opera sob um monorepo organizado com workspaces `pnpm`. Manter a separação de responsabilidades (Separation of Concerns) e a filosofia DRY (Don't Repeat Yourself) é obrigatório:

```text
/
├── apps/
│   ├── api/            # API backend baseada em Express
│   └── web/            # Aplicação web principal (Vite + React)
├── packages/
│   ├── types/          # Fonte da verdade de tipos e schemas Zod compartilhados
│   ├── ui/             # Design System (Base UI + Tailwind)
│   ├── hooks/          # Hooks customizados e utilitários de estado/media-queries
│   └── config/         # Configurações globais (Biome, tsconfig, etc.)
```

### Contratos DRY com `/packages/types`
Nunca duplique contratos ou interfaces.
- **Zod Schemas**: Todos os contratos de requisição e resposta de API devem ser declarados como schemas Zod em `/packages/types`.
- **Type Inference**: O frontend deve inferir os tipos TypeScript diretamente desses schemas usando `z.infer<typeof Schema>` para garantir segurança ponta a ponta e zero divergência de tipos.

---

## 🌐 3. Data Fetching Resiliente (`apiClient`)

Substituímos o Axios pelo **`ofetch`** por ser mais moderno, leve e oferecer interceptores assíncronos nativos. O `apiClient` está configurado para gerenciar timeouts, injeção dinâmica de JWT (do store Zustand) e deslogar o usuário em caso de erros de autorização (401).

### Código Real do `apiClient.ts`
Implemente ou mantenha o cliente HTTP estruturado da seguinte forma no app frontend (`apps/web/src/services/apiClient.ts`):

```typescript
import { ofetch } from 'ofetch';
import { useAuthStore } from '@/store/auth-store'; // Zustand store de autenticação

const API_BASE_URL = import.meta.env.VITE_API_BASE_URL || 'http://localhost:4100';

export const apiClient = ofetch.create({
  baseURL: API_BASE_URL,
  timeout: 15000, // Timeout padrão de 15 segundos
  headers: {
    'Content-Type': 'application/json',
    Accept: 'application/json',
  },
  async onRequest({ options }) {
    // Busca dinamicamente o JWT atual do Zustand store
    const token = useAuthStore.getState().token;
    if (token) {
      options.headers = {
        ...options.headers,
        Authorization: `Bearer ${token}`,
      };
    }
  },
  async onResponseError({ response }) {
    // Tratamento unificado de erros de autenticação (JWT expirado ou inválido)
    if (response.status === 401) {
      console.warn('Sessão expirada (401). Efetuando logout automático...');
      useAuthStore.getState().logout();
      window.location.href = '/login';
    }
  },
});
```

---

## 📱 4. Responsividade Avançada e Web Standards

### 🍎 Meta Viewport Obrigatório (iOS / Safari / macOS)

Todo projeto AT5 **deve** incluir a seguinte meta tag no `index.html`. Sem ela, o Safari no iOS aplica zoom automático em inputs e o layout quebra em telas pequenas:

```html
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1" />
```

> [!TIP]
> O `maximum-scale=1` previne o zoom automático de inputs no iOS Safari (comportamento padrão quando o `font-size` do input é menor que 16px). Se o seu app precisa de zoom acessível, use `minimum-scale=1` sem o `maximum-scale`.

---

A AT5 proíbe terminantemente o uso de tabelas HTML clássicas (`<table>`, `<tr>`, `<td>`) para layouts complexos ou listagens em dispositivos móveis, pois elas quebram a visualização e impedem a navegabilidade intuitiva.

### 📐 Grade Responsiva vs Tabelas
Sempre prefira estruturas baseadas em **CSS Grid** e **Flexbox** com classes utilitárias do Tailwind v4 para adaptar listas e dados densos:

```tsx
// ❌ NÃO FAZER: Tabelas rígidas que quebram no mobile
// ✅ FAZER: Grid adaptativo e Mobile-First
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

### ♿ Acessibilidade (a11y) como Requisito de Engenharia
Todos os componentes e telas devem ser totalmente acessíveis por padrão:
- **ARIA**: Usar atributos `aria-*` apropriados para descrever estados interativos (ex: `aria-expanded`, `aria-invalid`, `aria-describedby`).
- **Suporte a Teclado**: Garantir que todos os elementos interativos sejam focáveis (`tabIndex={0}`) e possam ser acionados via `Enter` ou `Space`.
- **Foco Visível**: Nunca remova os estilos de foco sem fornecer uma alternativa clara. Use `focus-visible:ring-2 focus-visible:ring-blue-7`.
- **Contraste de Cores**: Seguir as diretrizes do Radix Colors para garantir um contraste APCA adequado para leitura em qualquer tema (Light/Dark).

---

## 🎛️ 5. Invocação das Outras Skills Autônomas

Para implementar qualquer funcionalidade na AT5 com precisão milimétrica, o agente ou desenvolvedor **DEVE ativar e utilizar** as seguintes skills autônomas e independentes localizadas em **`~/.agents/skills/`** com base no escopo do trabalho:

1. 📂 **`at5-ui-components`**: Contém o catálogo completo de componentes React pré-estilizados e o código real de `/packages/ui` para criação rápida de interfaces acessíveis usando o **Slot Pattern** do Base UI (prop `render` ao invés de `asChild`).
2. 📂 **`at5-nicemodal`**: Ensina a orquestrar formulários e diálogos complexos usando o helper robusto `createFormDialog` (com suporte a sidebar e abas em mobile) e `createAlertDialog` sem wrappers manuais.
3. 📂 **`at5-radix-colors-tailwind`**: Guia para manipulação estrita de tokens de cores baseados em Radix Colors, OKLCH, temas automáticos e mapeamento exato de steps de 1 a 12 no `index.css`.
4. 📂 **`at5-toasts`**: Configuração e disparo de notificações assíncronas síncronas e tratamento nativo de Promises com `toastManager` e `anchoredToastManager`.

> [!TIP]
> Caso precise construir componentes de UI ou gerenciar diálogos agora, chame imediatamente `~/.agents/skills/at5-ui-components/SKILL.md` ou `~/.agents/skills/at5-nicemodal/SKILL.md`.
