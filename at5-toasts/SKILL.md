---
name: at5-toasts
description: Toast and notification system for AT5 projects using Base UI primitives. Use when implementing toasts, snackbars, success/error messages, loading indicators, async promise feedback, or contextually anchored alerts. Always trigger on keywords like toast, notification, toastManager, snackbar, loading state, success message, error message, promise feedback, AnchoredToastProvider, or Base UI toast.
---

# at5-toasts — Gerenciamento de Notificações e Toasts com Base UI

Esta skill autônoma documenta a arquitetura de **Gerenciamento de Notificações e Toasts** no ecossistema **AT5**, integrada com o motor headless **Base UI** (`@base-ui/react`). Ela apresenta o código real e completo para os providers de Toasts síncronos, assíncronos e ancorados (`ToastProvider`, `AnchoredToastProvider`), o gerenciador imperativo `toastManager` com suporte a Promises/loaders, e animações premium.

---

## 🛠️ 1. A Filosofia das Notificações AT5

O AT5 adota uma interface limpa, focada no usuário e com micro-animações fluidas. Os toasts do sistema evitam bibliotecas opinativas e pesadas, utilizando os primitivos acessíveis e headless do **Base UI**, estilizados com os tokens de cores do **Radix Colors** sob o **Tailwind v4.0**.

### Princípios de UX de Notificações:
1. **Não-obstrutivo**: Toasts normais aparecem nos cantos da tela e somem após `4000ms`.
2. **Estados Transitórios Claros**: Suporte nativo a status de `loading`, transicionando suavemente para `success` ou `error` via Promises.
3. **Ancoragem Contextual**: O `anchoredToastManager` permite disparar avisos contextuais colados a elementos de formulário ou botões específicos usando posicionamento por âncora (Anchor Positioning).
4. **Sem JSX no Fluxo de Negócios**: O desenvolvedor dispara notificações de forma 100% imperativa a partir de ações, queries ou services.

---

## 🏗️ 2. Código da Infraestrutura Imperativa (`toast-store.ts`)

Para permitir chamadas imperativas a partir de qualquer arquivo TypeScript do projeto (sem precisar de React Contexts locais), criamos um micro-store baseado em subscrição. 

Crie este arquivo em `apps/web/src/components/ui/toast/toast-store.ts` (ou local equivalente):

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
    // Retorna a função de unsubscribe
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
 * toastManager - API Imperativa Exposta para toda a aplicação
 */
export const toastManager = {
  success: (msg: string, duration?: number) => toastStore.show(msg, 'success', duration),
  error: (msg: string, duration?: number) => toastStore.show(msg, 'error', duration),
  warning: (msg: string, duration?: number) => toastStore.show(msg, 'warning', duration),
  info: (msg: string, duration?: number) => toastStore.show(msg, 'info', duration),
  loading: (msg: string) => toastStore.show(msg, 'loading', 999999),
  dismiss: (id: string) => toastStore.dismiss(id),
  
  // Resolve promises automaticamente alterando o estado do toast de Loading para Success ou Error
  promise: <T>(
    promise: Promise<T>,
    messages: { loading: string; success: string; error: string },
    duration = 4000
  ): Promise<T> => {
    const id = toastStore.show(messages.loading, 'loading', 999999);
    return promise
      .then((res) => {
        toastStore.update(id, { message: messages.success, type: 'success', duration });
        // Agenda remoção
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

## 🏗️ 3. Código Real do Componente `ToastProvider`

Este é o provider visual (`apps/web/src/components/ui/toast/toast-provider.tsx` ou equivalente local) que consome o store imperativo e renderiza os toasts utilizando os primitivos acessíveis do **Base UI**.

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
  XClose 
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
      {/* Container de exibição dos Toasts (canto inferior direito) */}
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
    success: 'bg-slate-2 border-success-6 text-slate-12',
    error: 'bg-slate-2 border-error-6 text-slate-12',
    warning: 'bg-slate-2 border-warning-6 text-slate-12',
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

## 🏗️ 4. Código Real do `AnchoredToastProvider` (Notificações Ancoradas)

Diferente do toast flutuante tradicional, o toast ancorado é ideal para alertas persistentes focados em elementos de tela (como aviso de validação próximo a um botão ou input que falhou). O provider abaixo gerencia dinamicamente elementos virtuais ou âncoras reais.

> [!WARNING]
> **Compatibilidade de Browser**: O `AnchoredToastProvider` usa a [CSS Anchor Positioning API](https://caniuse.com/css-anchor-positioning) (`anchor-name`, `position-anchor`). Suporte disponível apenas em **Chrome 125+ / Edge 125+**. Firefox e Safari ainda não suportam. Use como **progressive enhancement** — o toast ainda aparece, mas pode não se posicionar corretamente em browsers sem suporte.

Crie o arquivo em `apps/web/src/components/ui/toast/anchored-toast.tsx` ou equivalente:

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
        
        {/* Renderiza dinamicamente as portas ancoradas */}
        {Object.entries(activeToasts).map(([anchorId, { message }]) => (
          <Toast.Root
            key={anchorId}
            open={true}
            onOpenChange={(open) => !open && hideAnchored(anchorId)}
            className="z-50 bg-slate-12 text-slate-1 border border-slate-11 px-3 py-1.5 rounded-md text-xs font-semibold shadow-md max-w-xs animate-in zoom-in-95 fade-in-10"
            // CSS Anchor Positioning — Chrome 125+ / Edge 125+
            style={{
              position: 'absolute',
              // @ts-expect-error — propriedade CSS experimental ainda sem tipo no TS
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
    throw new Error('useAnchoredToast deve ser usado sob um AnchoredToastProvider');
  }
  return context;
}
```

---

## 📝 5. Exemplos de Utilização Prática

### 5.1 Toasts Simples Síncronos
```tsx
import { toastManager } from '@/components/ui/toast/toast-store';

function handleSaveSettings() {
  try {
    // Executa ação...
    toastManager.success('Configurações salvas com sucesso!');
  } catch (error) {
    toastManager.error('Falha ao salvar as configurações.');
  }
}
```

### 5.2 Toasts Assíncronos Baseados em Promise (Loaders)
```tsx
import { toastManager } from '@/components/ui/toast/toast-store';
import { apiClient } from '@/lib/api-client';

async function handleImportBilling() {
  const importPromise = apiClient.post('/billing/import', { year: 2026 });

  // Gerencia loading, sucesso e erro em uma única linha
  await toastManager.promise(importPromise, {
    loading: 'Processando arquivo de faturamento...',
    success: 'Faturamento importado e processado com sucesso!',
    error: 'Erro crítico ao processar o arquivo. Verifique os logs.',
  });
}
```

### 5.3 Notificações Ancoradas
Para que a ancoragem funcione, o botão ou input deve receber uma propriedade CSS `--anchor-name` customizada.

```tsx
import { useAnchoredToast } from '@/components/ui/toast/anchored-toast';

export function ExportButton() {
  const { showAnchored } = useAnchoredToast();

  const handleExport = () => {
    // Dispara exportação...
    showAnchored('export-btn', 'Exportação iniciada em segundo plano!', 3000);
  };

  return (
    <button
      id="export-btn"
      onClick={handleExport}
      className="bg-brand-9 text-white px-4 py-2 rounded-md hover:bg-brand-10"
      // Define a âncora para o CSS posicionar o toast ancorado — Chrome 125+
      // @ts-expect-error — propriedade CSS experimental sem tipo no TS
      style={{ anchorName: '--export-btn' }}
    >
      Exportar Dados
    </button>
  );
}
```

---

## ⚙️ 6. Como Validar e Auditar

Sempre que a IA ou o desenvolvedor fizerem alterações no sistema de Toasts, audite os seguintes itens:
1. **Acessibilidade ARIA**: Certifique-se de que os toasts possuem papéis de acessibilidade corretos (usando primitivos `Toast.Root` e `Toast.Description` do Base UI).
2. **Foco no Teclado**: Garanta que o aparecimento de um toast **não** roube o foco do teclado do usuário (propriedade `autoFocus={false}` ativa).
3. **Comportamento Responsivo**: Verifique se o painel de Toasts se ajusta ao tamanho da tela e empilha corretamente em telas de dispositivos móveis.
