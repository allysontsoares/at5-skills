# 🔧 Utilities & Hooks

Utilitários e hooks internos que auxiliam os componentes do design system a funcionarem de forma fluida.

---

## 📂 `truncate.ts`

Caminho original no repositório: `/packages/ui/src/utils/truncate.ts`

```typescript
export function truncate(str: string | null | undefined, maxLength: number): string {
  if (!str) return '';
  return str.length > maxLength ? `${str.substring(0, maxLength)}...` : str;
}
```

---

## 📂 `use-mobile.ts`

Caminho original no repositório: `/packages/ui/src/hooks/use-mobile.ts`

```typescript
import * as React from 'react';

const MOBILE_BREAKPOINT = 768;

export function useIsMobile() {
  const [isMobile, setIsMobile] = React.useState<boolean | undefined>(undefined);

  React.useEffect(() => {
    const mql = window.matchMedia(`(max-width: ${MOBILE_BREAKPOINT - 1}px)`);
    const onChange = () => {
      setIsMobile(window.innerWidth < MOBILE_BREAKPOINT);
    };
    mql.addEventListener('change', onChange);
    setIsMobile(window.innerWidth < MOBILE_BREAKPOINT);
    return () => mql.removeEventListener('change', onChange);
  }, []);

  return !!isMobile;
}
```

---

