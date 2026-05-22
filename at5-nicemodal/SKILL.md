---
name: at5-nicemodal
description: Declarative modal and form dialog patterns for AT5 projects using NiceModal + React Hook Form + Zod. Use when creating modals, dialogs, form popups, confirmation dialogs, alert dialogs, or any overlay workflow. Always trigger on keywords like modal, dialog, form dialog, popup, createFormDialog, createAlertDialog, close guard, or NiceModal. Includes sidebar tab layout, dirty form protection, and async close confirmation.
---

# at5-nicemodal — Diálogos e Formulários Declarativos de Alta Performance

Esta skill autônoma documenta a arquitetura de **Diálogos Declarativos** no ecossistema **AT5**, eliminando a poluição visual do JSX e wrappers manuais desnecessários do NiceModal. Ela apresenta os códigos de infraestrutura completos de **`createFormDialog`** e **`createAlertDialog`**, integrados com **React Hook Form**, **Zod** e o compound component de `Modal` do design system.

---

## 🛠️ 1. A Filosofia Declarativa

Em vez de controlar estados `isOpen`, `onClose` manuais no componente pai e poluir a árvore JSX com modais, o AT5 adota o **NiceModal** envelopado em utilitários estritos. Qualquer diálogo no sistema é disparado de forma declarativa e síncrona/assíncrona:

```typescript
import { NiceModal } from '@ebay/nice-modal-react';
import { UserFormDialog } from './user-form-dialog';

// Dispara e aguarda o sucesso do formulário de forma assíncrona
async function handleEditUser(user) {
  try {
    const result = await NiceModal.show(UserFormDialog, { mode: 'edit', user });
    console.log('Usuário atualizado com sucesso:', result);
  } catch (err) {
    // Cancelamento ou fechamento silencioso
  }
}
```

> [!NOTE]
> **Nota sobre Imports**: Todos os códigos abaixo utilizam os aliases ilustrativos `@/components/ui` (ou `@/packages/ui`) e `@/lib/utils` para garantir compatibilidade agnóstica. Ao implementar em um projeto específico, adapte os aliases de importação de acordo com o `tsconfig.json` e a estrutura de diretórios local.

---

## 🏗️ 2. Código Real do Helper `createFormDialog`

Este é o helper definitivo (`apps/web/src/lib/create-form-dialog.tsx` ou equivalente local) que automatiza a criação de modais de formulário. Ele suporta:
1. **Layout com Sidebar**: Menus laterais verticais em desktop e barra de abas de rolagem horizontal em mobile.
2. **Close Guard Inteligente**: Bloqueia o fechamento acidental do modal caso o formulário esteja `isDirty` (sujo/modificado), disparando um diálogo de confirmação.
3. **Pending States**: Gerenciamento automático de estado de loading ao enviar o formulário.

```tsx
import { type ComponentType, useEffect, useRef, useState } from 'react';
import NiceModal, { useModal } from '@ebay/nice-modal-react';
import { Modal } from '@/components/ui/modal'; // Adapte o alias para o design system local
import { Button } from '@/components/ui/button'; // Adapte o alias para o design system local
import { ChevronRight } from '@untitled-ui/icons-react'; // Ou outra biblioteca de ícones do projeto

export interface SidebarTabConfig {
  key: string;
  label: string;
  icon: ComponentType<{ className?: string }>;
}

export interface FormDialogInjectedProps {
  formId: string;
  onSuccess: (data?: any) => void;
  onPendingChange?: (isPending: boolean) => void;
  onSuccessChange?: (isDirty: boolean) => void; // notifica o close guard sobre estado sujo do form
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

  return NiceModal.create((props: TProps) => {
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
    const resolveSubmitLabel = typeof config.submitLabel === 'function' ? config.submitLabel(props) : (config.submitLabel ?? 'Confirmar');
    const resolveSize = typeof config.size === 'function' ? config.size(props) : (config.size ?? 'md');

    // Close Guard: Se o form estiver sujo, abre confirmação via createAlertDialog (design system)
    const handleCloseAttempt = async () => {
      if (isDirtyRef.current) {
        // Importar e usar createAlertDialog para manter o padrão visual do design system
        const { DiscardChangesDialog } = await import('./create-alert-dialog'); // ajuste o path
        const confirmed = await NiceModal.show(DiscardChangesDialog);
        if (!confirmed) return;
      }
      modal.hide();
    };

    // Exemplo de DiscardChangesDialog para usar no close guard:
    // export const DiscardChangesDialog = createAlertDialog({
    //   title: 'Descartar alterações?',
    //   description: 'Você tem alterações não salvas. Ao fechar, todas as alterações serão perdidas.',
    //   confirmLabel: 'Descartar',
    //   cancelLabel: 'Continuar editando',
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
      <Modal open={modal.visible} onOpenChange={(open) => !open && handleCloseAttempt()}>
        <Modal.Backdrop isDismissable={config.isDismissable ?? true} variant={config.backdropVariant ?? 'opaque'}>
          <Modal.Container size={resolveSize} placement={config.placement ?? 'auto'} scroll="inside">
            <Modal.Dialog>
              <Modal.CloseTrigger onClick={(e) => { e.preventDefault(); handleCloseAttempt(); }} />
              <Modal.Header>
                <Modal.Heading>{resolveTitle}</Modal.Heading>
              </Modal.Header>

              {showSidebar && tabs.length > 0 ? (
                /* Layout Sidebar Compound */
                <div className="mt-2 min-h-0 flex-1 flex flex-col sm:flex-row overflow-hidden">
                  {/* Sidebar Desktop */}
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
                  
                  {/* Abas responsivas Mobile (rolagem horizontal) */}
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

                  {/* Container de formulário com scroll independente */}
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
                /* Modal sem Sidebar Clássico */
                <Modal.Body>
                  <FormComponent
                    key={resetKey}
                    {...props}
                    formId={FORM_ID}
                    onSuccess={handleSuccess}
                    onPendingChange={setIsPending}
                    onSuccessChange={(dirty: boolean) => { isDirtyRef.current = dirty; }}
                  />
                </Modal.Body>
              )}

              <Modal.Footer>
                <Button variant="outline" accent="secondary" type="button" onClick={handleCloseAttempt}>
                  {config.cancelLabel ?? 'Cancelar'}
                </Button>
                <Button type="submit" form={FORM_ID} isLoading={isPending}>
                  {resolveSubmitLabel}
                </Button>
              </Modal.Footer>
            </Modal.Dialog>
          </Modal.Container>
        </Modal.Backdrop>
      </Modal>
    );
  });
}
```

---

## 🏗️ 3. Código Real do Helper `createAlertDialog`

Perfeito para modais de confirmação simples de ações destrutivas (ex: "Excluir Registro"), bloqueios ou avisos de segurança:

```tsx
import NiceModal, { useModal } from '@ebay/nice-modal-react';
import { Modal } from '@/components/ui/modal'; // Adapte o alias para o design system local
import { Button } from '@/components/ui/button'; // Adapte o alias para o design system local

export interface AlertDialogConfig {
  title: string;
  description: string;
  confirmLabel?: string;
  cancelLabel?: string;
  variant?: 'danger' | 'warning' | 'primary';
}

export function createAlertDialog(config: AlertDialogConfig) {
  return NiceModal.create(() => {
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
      <Modal open={modal.visible} onOpenChange={(open) => !open && handleCancel()}>
        <Modal.Backdrop isDismissable={true} variant="blur">
          <Modal.Container size="sm" placement="center">
            <Modal.Dialog>
              <Modal.Header>
                <Modal.Heading>{config.title}</Modal.Heading>
              </Modal.Header>
              <Modal.Body>
                <p className="text-sm text-slate-11 leading-relaxed">{config.description}</p>
              </Modal.Body>
              <Modal.Footer>
                <Button variant="outline" accent="secondary" type="button" onClick={handleCancel}>
                  {config.cancelLabel ?? 'Cancelar'}
                </Button>
                <Button
                  type="button"
                  accent={config.variant === 'danger' ? 'danger' : 'primary'}
                  onClick={handleConfirm}
                >
                  {config.confirmLabel ?? 'Confirmar'}
                </Button>
              </Modal.Footer>
            </Modal.Dialog>
          </Modal.Container>
        </Modal.Backdrop>
      </Modal>
    );
  });
}
```

---

## 📝 4. Exemplo Completo e Real: Diálogo de Formulário `UserDialog`

Aqui está como um desenvolvedor ou agente deve implementar uma tela de formulário declarativa de acordo com os padrões da AT5:

### Arquivo `UserForm.tsx` (Componente de Formulário)
```tsx
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { z } from 'zod';
import { Input, Textarea } from '@/components/ui'; // Adapte o alias para o design system local
import { FormDialogInjectedProps } from './create-form-dialog';
import { useEffect } from 'react';

const userSchema = z.object({
  name: z.string().min(3, 'Nome deve conter no mínimo 3 caracteres'),
  email: z.string().email('Email inválido'),
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

  // Notifica o close guard sobre mudanças no formulário
  useEffect(() => {
    onSuccessChange?.(isDirty);
  }, [isDirty, onSuccessChange]);

  // Notifica o botão de loading sobre submissão pendente
  useEffect(() => {
    onPendingChange?.(isSubmitting);
  }, [isSubmitting, onPendingChange]);

  const onSubmit = async (data: UserFormData) => {
    // Simula chamada de API
    await new Promise((resolve) => setTimeout(resolve, 1500));
    console.log('Payload enviado:', data);
    onSuccess(data);
  };

  return (
    <form id={formId} onSubmit={handleSubmit(onSubmit)} className="space-y-4">
      <div>
        <label className="block text-xs font-semibold text-slate-12 mb-1.5">Nome Completo</label>
        <Input {...register('name')} placeholder="Digite o nome do usuário" aria-invalid={!!errors.name} />
        {errors.name && <p className="mt-1 text-xs text-red-11">{errors.name.message}</p>}
      </div>

      <div>
        <label className="block text-xs font-semibold text-slate-12 mb-1.5">Endereço de E-mail</label>
        <Input {...register('email')} type="email" placeholder="usuario@at5.com" aria-invalid={!!errors.email} />
        {errors.email && <p className="mt-1 text-xs text-red-11">{errors.email.message}</p>}
      </div>

      <div>
        <label className="block text-xs font-semibold text-slate-12 mb-1.5">Biografia</label>
        <Textarea {...register('bio')} placeholder="Fale um pouco sobre o usuário..." />
      </div>
    </form>
  );
}
```

### Arquivo `UserDialog.tsx` (Configuração Declarativa)
```tsx
import { createFormDialog } from './create-form-dialog';
import { UserForm } from './UserForm';

export const UserDialog = createFormDialog(UserForm, {
  title: (props) => props.mode === 'edit' ? 'Editar Perfil do Usuário' : 'Criar Novo Usuário',
  submitLabel: (props) => props.mode === 'edit' ? 'Salvar Alterações' : 'Criar Conta',
  size: 'lg',
});
```
