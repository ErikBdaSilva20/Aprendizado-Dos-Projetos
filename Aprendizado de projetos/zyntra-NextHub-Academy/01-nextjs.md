# Next.js — App Router

## Conceitos principais

- **App Router** — sistema de roteamento baseado em pastas (desde o Next.js 13)
- **Server Components** — componentes que rodam no servidor, sem JavaScript no cliente
- **Client Components** — componentes que rodam no browser (`"use client"`)
- **Layout** — UI compartilhada entre várias páginas (sidebar, header, etc.)
- **Metadata** — título e descrição de SEO definidas no código

---

## Por que o Next.js existe?

O React puro não tem roteamento, SEO ou renderização no servidor. O Next.js resolve todos esses problemas de uma só vez. Ele transforma o React em um framework completo para produção.

- **Sem Next.js:** você precisaria de React Router, SSR manual, config de bundler...
- **Com Next.js:** tudo isso vem pronto, só codando as páginas

---

## Como o roteamento funciona aqui

No Next.js App Router, **cada pasta dentro de `src/app` vira uma rota**.

```
src/app/
├── layout.tsx              → layout raiz (wraps tudo)
├── (private)/              → grupo de rotas (não vira URL)
│   └── dashboard/
│       ├── layout.tsx      → layout do dashboard (sidebar, etc)
│       ├── page.tsx        → rota: /dashboard
│       ├── billing/
│       │   └── page.tsx    → rota: /dashboard/billing
│       └── settings/
│           └── page.tsx    → rota: /dashboard/settings
└── (public)/               → rotas públicas (login, etc.)
    └── login/
        └── page.tsx        → rota: /login
```

> **Parênteses em pasta** = grupo de rotas. A pasta `(private)` e `(public)` não aparecem na URL, servem só pra organizar.

---

## O arquivo `layout.tsx` raiz

Este é o arquivo mais importante do projeto. Ele envolve **todas** as páginas:

```tsx
// src/app/layout.tsx
import { ConvexAuthNextjsServerProvider } from "@convex-dev/auth/nextjs/server";
import { ConvexClientProvider } from "@/app/_providers/ConvexClientProvider";

export const metadata: Metadata = {
  title: "Zyntra",
  description: "Plataforma moderna de gestão de clínicas",
};

export default function RootLayout({ children }) {
  return (
    <ConvexAuthNextjsServerProvider>   {/* Auth no servidor */}
      <html lang="pt-BR" className="dark">
        <body>
          <ConvexClientProvider>       {/* Conexão com o banco */}
            <TooltipProvider>
              {children}               {/* ← aqui entram todas as páginas */}
            </TooltipProvider>
          </ConvexClientProvider>
        </body>
      </html>
    </ConvexAuthNextjsServerProvider>
  );
}
```

**O que cada provider faz:**
- `ConvexAuthNextjsServerProvider` → habilita autenticação no lado servidor
- `ConvexClientProvider` → conecta o frontend ao banco Convex em tempo real
- `TooltipProvider` → habilita tooltips globais (shadcn/ui)

---

## Server Component vs Client Component

### Server Component (padrão)
```tsx
// src/app/(private)/dashboard/page.tsx
// SEM "use client" no topo = roda no servidor

export default async function DashboardPage() {
  // Pode fazer fetch direto, acessar banco, variáveis de ambiente seguras
  return <div>Dashboard</div>;
}
```

### Client Component
```tsx
"use client"; // ← obrigatório declarar

import { useState } from "react";

export function Counter() {
  const [count, setCount] = useState(0); // useState só funciona com "use client"
  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```

### Regra de ouro
| Precisa de... | Use |
|---|---|
| `useState`, `useEffect`, eventos de click | Client Component |
| fetch de dados, lógica pesada, SEO | Server Component |
| hooks do Convex (`useQuery`, `useMutation`) | Client Component |

> No Zyntra, quase todos os arquivos em `_components/` têm `"use client"` porque usam hooks do Convex.

---

## Metadata (SEO)

```tsx
// Em qualquer page.tsx ou layout.tsx
export const metadata: Metadata = {
  title: "Zyntra",
  description: "Plataforma moderna de gestão de clínicas",
};
```

O Next.js injeta isso automaticamente nas tags `<title>` e `<meta>` do HTML.

---

## Problemas comuns

### ❌ "useState can't be used in Server Component"
**Causa:** você esqueceu o `"use client"` no topo do arquivo.  
**Solução:** adicione `"use client"` na primeira linha.

### ❌ "useQuery is not a function" / hooks do Convex não funcionam
**Causa:** o componente não está dentro do `ConvexClientProvider`.  
**Solução:** verifique se o `layout.tsx` raiz tem o provider corretamente.

### ❌ A rota não aparece
**Causa:** o arquivo se chama `index.tsx` em vez de `page.tsx`.  
**Solução:** no App Router, o arquivo da página **sempre** se chama `page.tsx`.

---

## Boas práticas no projeto

- Pastas com `_` prefixo (`_components`, `_hooks`, `_providers`) são privadas da rota, não viram URL
- Layouts são reutilizados — não repita sidebar/header em cada página
- Use Server Components por padrão; só adicione `"use client"` quando necessário
- Metadata é definida por rota, não globalmente só no `layout.tsx` raiz
