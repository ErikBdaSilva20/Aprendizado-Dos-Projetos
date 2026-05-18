# Next.js — Tudo que Você Precisa Saber Antes de Começar

> **Para quem é esse guia?**
> Para quem já conhece React e JavaScript/TypeScript, mas nunca criou um projeto real com Next.js. Vou explicar do jeito mais claro possível, sem enrolação.

---

## Sumário

1. [O que é Next.js e por que usar](#1-o-que-é-nextjs-e-por-que-usar)
2. [Como o Next.js pensa sobre páginas e rotas](#2-como-o-nextjs-pensa-sobre-páginas-e-rotas)
3. [App Router — o coração do Next.js moderno](#3-app-router--o-coração-do-nextjs-moderno)
4. [Server Components vs Client Components](#4-server-components-vs-client-components)
5. [Como o Next.js busca dados](#5-como-o-nextjs-busca-dados)
6. [Layouts — a mágica da estrutura compartilhada](#6-layouts--a-mágica-da-estrutura-compartilhada)
7. [Loading, Error e Not Found — os arquivos especiais](#7-loading-error-e-not-found--os-arquivos-especiais)
8. [Route Handlers — sua mini API dentro do projeto](#8-route-handlers--sua-mini-api-dentro-do-projeto)
9. [Middleware — o segurança da porta](#9-middleware--o-segurança-da-porta)
10. [Variáveis de Ambiente — segredos do projeto](#10-variáveis-de-ambiente--segredos-do-projeto)
11. [Imagens, Fontes e Assets](#11-imagens-fontes-e-assets)
12. [Metadata e SEO](#12-metadata-e-seo)
13. [Desenvolvimento Local — o dia a dia](#13-desenvolvimento-local--o-dia-a-dia)
14. [Build e Produção — o que acontece quando você publica](#14-build-e-produção--o-que-acontece-quando-você-publica)
15. [Deploy na Vercel — o jeito mais simples](#15-deploy-na-vercel--o-jeito-mais-simples)
16. [Erros comuns e como evitar](#16-erros-comuns-e-como-evitar)
17. [Boas práticas gerais](#17-boas-práticas-gerais)
18. [Glossário rápido](#18-glossário-rápido)

---

## 1. O que é Next.js e por que usar

### A explicação simples

Pensa assim: React é como uma caixa de peças de LEGO. Você tem as peças, mas precisa descobrir como encaixar tudo — como criar rotas, como buscar dados, como otimizar para o Google.

Next.js é o manual que já vem com as peças encaixadas nas partes chatas, deixando você focar no que importa: **construir o produto**.

### O que ele resolve que o React puro não resolve

| Problema | React Puro | Next.js |
|---|---|---|
| Criar rotas | Instalar React Router, configurar | Automático pela estrutura de pastas |
| Buscar dados no servidor | Complicado | Nativo |
| SEO (Google te encontrar) | Difícil | Simples |
| Performance de carregamento | Manual | Automático |
| API própria | Projeto separado | Dentro do mesmo projeto |

### Por que usar no SaaS de academias?

- O dashboard precisa carregar rápido → Next.js otimiza isso
- Múltiplas páginas (alunos, frequência, financeiro) → rotas automáticas
- Autenticação e proteção de rotas → middleware nativo
- Supabase + Next.js → combinação muito popular com excelente suporte

---

## 2. Como o Next.js pensa sobre páginas e rotas

### A regra de ouro

> **A estrutura de pastas É a rota da URL.**

Se você cria a pasta `app/alunos/`, a URL `/alunos` existe automaticamente.  
Se você cria `app/alunos/novo/`, a URL `/alunos/novo` existe automaticamente.

Não tem arquivo de configuração de rotas. Não tem `react-router`. A pasta vira a rota.

### Arquivos especiais dentro de cada pasta

Dentro de cada pasta de rota, o Next.js reconhece nomes específicos de arquivo:

| Arquivo | O que faz |
|---|---|
| `page.tsx` | O conteúdo da página naquela rota |
| `layout.tsx` | Estrutura que envolve a página (e todas as filhas) |
| `loading.tsx` | Tela de carregamento automática |
| `error.tsx` | Tela de erro automática |
| `not-found.tsx` | Tela de 404 |
| `route.ts` | Endpoint de API naquela rota |

### Rotas dinâmicas (com parâmetros na URL)

Quando a URL tem um ID variável, tipo `/alunos/123` ou `/alunos/456`:

```
app/
  alunos/
    [id]/           ← os colchetes indicam: esse trecho é dinâmico
      page.tsx      ← recebe o valor de 'id' como prop
```

Dentro do `page.tsx` você acessa assim:

```tsx
// app/alunos/[id]/page.tsx
export default function PaginaAluno({ params }: { params: { id: string } }) {
  // params.id vai ser '123', '456', qualquer valor da URL
  return <div>Aluno: {params.id}</div>
}
```

### Grupos de rotas (sem afetar a URL)

Você pode criar pastas que **agrupam** rotas sem aparecer na URL. Isso é útil para separar páginas autenticadas das públicas:

```
app/
  (auth)/           ← o parêntese indica: pasta de grupo, não aparece na URL
    login/
      page.tsx      → URL: /login
    cadastro/
      page.tsx      → URL: /cadastro

  (dashboard)/      ← outro grupo
    page.tsx        → URL: / (página inicial do dashboard)
    alunos/
      page.tsx      → URL: /alunos
```

Cada grupo pode ter seu próprio `layout.tsx` — por exemplo, o grupo `(dashboard)` tem o layout com sidebar e header, enquanto o `(auth)` tem layout limpo sem menu.

---

## 3. App Router — o coração do Next.js moderno

### O que é o App Router

O Next.js tem dois "modos" de organizar rotas. O antigo se chama **Pages Router** (pasta `pages/`). O novo e atual se chama **App Router** (pasta `app/`).

**Você vai usar o App Router.** É o padrão desde o Next.js 13 e é o futuro do framework. Se você encontrar tutoriais usando a pasta `pages/`, eles podem estar desatualizados.

### Como identificar

- **App Router**: arquivos ficam em `src/app/` ou `app/`
- **Pages Router (antigo)**: arquivos ficam em `pages/`

### Por que o App Router é melhor

- Suporte nativo a **Server Components** (componentes que rodam no servidor)
- **Layouts aninhados** sem gambiarras
- **Loading states** automáticos por rota
- **Streaming** de dados (a página aparece aos poucos conforme carrega)
- Melhor performance geral

---

## 4. Server Components vs Client Components

Essa é **a parte mais importante de entender no Next.js moderno**. Muita confusão acontece aqui.

### A ideia central

No Next.js com App Router, todo componente é **Server Component por padrão**.

Isso significa: ele roda no servidor, gera o HTML, e manda o HTML pronto para o navegador. O JavaScript desse componente **não vai para o navegador**.

### Quando usar cada um

**Server Component (padrão, sem nada especial)**

Use quando o componente:
- Busca dados do banco
- Acessa variáveis de ambiente secretas
- Não precisa de interação do usuário
- Não usa `useState`, `useEffect`, eventos de clique

```tsx
// Sem nenhuma declaração especial = Server Component
// app/alunos/page.tsx
export default async function PaginaAlunos() {
  // Pode buscar dados diretamente aqui, sem useEffect
  const alunos = await buscarAlunos()

  return (
    <ul>
      {alunos.map(aluno => <li key={aluno.id}>{aluno.nome}</li>)}
    </ul>
  )
}
```

**Client Component (`'use client'` no topo)**

Use quando o componente:
- Usa `useState` ou `useEffect`
- Responde a cliques, inputs, eventos do usuário
- Usa hooks do React
- Acessa `window`, `document`, `localStorage`
- Usa bibliotecas que precisam do navegador (ex: gráficos animados)

```tsx
'use client' // ← essa linha transforma em Client Component

import { useState } from 'react'

export function BotaoFavoritar({ alunoId }: { alunoId: string }) {
  const [favoritado, setFavoritado] = useState(false)

  return (
    <button onClick={() => setFavoritado(!favoritado)}>
      {favoritado ? '★ Favoritado' : '☆ Favoritar'}
    </button>
  )
}
```

### A regra prática

> **Comece sempre sem `'use client'`. Só adicione quando o Next.js reclamar que você está usando um hook ou evento.**

### Você pode misturar os dois

Um Server Component pode importar e usar um Client Component dentro dele. O contrário (Client importando Server) tem limitações — evite por enquanto.

```tsx
// page.tsx — Server Component
import { BotaoFavoritar } from './BotaoFavoritar' // Client Component

export default async function PaginaAluno() {
  const aluno = await buscarAluno()

  return (
    <div>
      <h1>{aluno.nome}</h1>
      <BotaoFavoritar alunoId={aluno.id} /> {/* Client dentro de Server: ok! */}
    </div>
  )
}
```

---

## 5. Como o Next.js busca dados

### No Server Component: direto, sem cerimônia

Você pode usar `async/await` diretamente na função do componente:

```tsx
// Isso funciona porque page.tsx é um Server Component
export default async function PaginaAlunos() {
  const response = await fetch('https://sua-api.com/alunos')
  const alunos = await response.json()

  return <ListaAlunos alunos={alunos} />
}
```

Ou com Supabase:

```tsx
export default async function PaginaAlunos() {
  const { data: alunos } = await supabase
    .from('alunos')
    .select('*')
    .eq('academia_id', academiaId)

  return <ListaAlunos alunos={alunos} />
}
```

### No Client Component: com Tanstack Query

Em Client Components (que têm interatividade), você usa o **Tanstack Query** como definido nos agentes:

```tsx
'use client'

import { useQuery } from '@tanstack/react-query'

export function ListaAlunos() {
  const { data: alunos, isLoading } = useQuery({
    queryKey: ['alunos'],
    queryFn: () => alunoService.listar()
  })

  if (isLoading) return <p>Carregando...</p>
  return <ul>{alunos?.map(a => <li key={a.id}>{a.nome}</li>)}</ul>
}
```

### Quando usar cada abordagem

| Situação | Abordagem |
|---|---|
| Dados que existem quando a página carrega (sem interação) | Server Component com async/await |
| Dados que mudam com base em ação do usuário (filtro, busca) | Client Component + Tanstack Query |
| Dados que precisam atualizar em tempo real | Client Component + Tanstack Query |
| Dashboard inicial com métricas | Server Component (carrega mais rápido) |

---

## 6. Layouts — a mágica da estrutura compartilhada

### O que é um layout

Um layout é um componente que **envolve** as páginas filhas e **não é recriado** quando o usuário navega entre elas.

Pensa assim: no seu dashboard, o menu lateral (sidebar) e o cabeçalho (header) aparecem em todas as páginas. Você não quer recriar esses elementos toda vez que o usuário clica em algo. O layout faz exatamente isso — ele fica fixo enquanto só o conteúdo principal muda.

### Como funciona na prática

```
app/
  layout.tsx           ← Layout raiz (envolve tudo)
  (dashboard)/
    layout.tsx         ← Layout do dashboard (sidebar + header)
    page.tsx           ← Conteúdo da home do dashboard
    alunos/
      page.tsx         ← Conteúdo da listagem de alunos
```

```tsx
// app/(dashboard)/layout.tsx
export default function DashboardLayout({
  children  // ← aqui vai o conteúdo de cada page.tsx
}: {
  children: React.ReactNode
}) {
  return (
    <div className="flex h-screen">
      <Sidebar />         {/* Sempre visível */}
      <div className="flex-1">
        <Header />        {/* Sempre visível */}
        <main>
          {children}      {/* Aqui muda conforme a rota */}
        </main>
      </div>
    </div>
  )
}
```

Quando o usuário navega de `/alunos` para `/frequencia`, o `DashboardLayout` **não remonta**. Só o `{children}` muda. Isso é muito mais performático.

### Layout raiz (obrigatório)

O `app/layout.tsx` é obrigatório e envolve absolutamente tudo. É onde você coloca as tags `<html>` e `<body>`, fontes globais, providers do Tanstack Query, etc.

```tsx
// app/layout.tsx
import { QueryProvider } from '@/components/providers/QueryProvider'

export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="pt-BR">
      <body>
        <QueryProvider>
          {children}
        </QueryProvider>
      </body>
    </html>
  )
}
```

---

## 7. Loading, Error e Not Found — os arquivos especiais

### `loading.tsx` — tela de carregamento automática

Quando uma página está buscando dados (com `async/await`), o Next.js mostra o `loading.tsx` automaticamente enquanto espera.

```tsx
// app/alunos/loading.tsx
export default function CarregandoAlunos() {
  return (
    <div>
      {/* Um skeleton simples enquanto carrega */}
      <div className="animate-pulse bg-gray-200 h-8 w-48 rounded mb-4" />
      <div className="animate-pulse bg-gray-200 h-64 rounded" />
    </div>
  )
}
```

Você não precisa chamar esse componente em lugar nenhum. O Next.js sabe que quando `/alunos` está carregando, deve mostrar o `app/alunos/loading.tsx`.

### `error.tsx` — tela de erro automática

Se a `page.tsx` quebrar (erro de rede, banco fora, qualquer exceção), o Next.js mostra o `error.tsx` ao invés de travar tudo.

```tsx
'use client' // error.tsx PRECISA ser Client Component

export default function ErroAlunos({
  error,
  reset
}: {
  error: Error
  reset: () => void // função para tentar novamente
}) {
  return (
    <div>
      <h2>Algo deu errado ao carregar os alunos</h2>
      <p>{error.message}</p>
      <button onClick={reset}>Tentar novamente</button>
    </div>
  )
}
```

### `not-found.tsx` — página 404

Quando um aluno com aquele ID não existe, você pode chamar `notFound()` e o Next.js mostra o `not-found.tsx`:

```tsx
// app/alunos/[id]/page.tsx
import { notFound } from 'next/navigation'

export default async function PaginaAluno({ params }: { params: { id: string } }) {
  const aluno = await buscarAluno(params.id)

  if (!aluno) {
    notFound() // ← redireciona para o not-found.tsx
  }

  return <DetalhesAluno aluno={aluno} />
}
```

---

## 8. Route Handlers — sua mini API dentro do projeto

### O que são

No Next.js, você pode criar endpoints de API dentro do mesmo projeto, sem precisar de um servidor separado. Eles ficam em arquivos chamados `route.ts`.

```
app/
  api/
    alunos/
      route.ts    → GET /api/alunos, POST /api/alunos
    alunos/
      [id]/
        route.ts  → GET /api/alunos/:id, PUT /api/alunos/:id
```

### Como escrever um Route Handler

```ts
// app/api/alunos/route.ts

import { NextRequest, NextResponse } from 'next/server'

// Responde requisições GET em /api/alunos
export async function GET(request: NextRequest) {
  const alunos = await buscarAlunos()
  return NextResponse.json(alunos)
}

// Responde requisições POST em /api/alunos
export async function POST(request: NextRequest) {
  const body = await request.json()
  const novoAluno = await criarAluno(body)
  return NextResponse.json(novoAluno, { status: 201 })
}
```

### Quando usar Route Handlers

- Quando precisar de um webhook (ex: Stripe, WhatsApp)
- Quando precisar de lógica que não pode rodar no cliente (ex: enviar email)
- Quando precisar de um endpoint para integração com terceiros

**No seu projeto com Supabase**, você vai usar o SDK do Supabase direto nos Server Components e Client Components na maioria dos casos. Route Handlers ficam para casos específicos.

---

## 9. Middleware — o segurança da porta

### O que é

O middleware é um código que roda **antes de qualquer requisição chegar na página**. Ele funciona como um segurança na porta: verifica se a pessoa pode entrar antes de abrir.

No seu projeto, ele vai verificar se o usuário está autenticado antes de deixar ele acessar o dashboard.

### Onde fica

O arquivo se chama `middleware.ts` e fica **na raiz do projeto** (fora da pasta `app/`):

```
src/
  app/
  middleware.ts    ← aqui
```

### Exemplo com Supabase Auth

```ts
// middleware.ts
import { createServerClient } from '@supabase/ssr'
import { NextResponse } from 'next/server'
import type { NextRequest } from 'next/server'

export async function middleware(request: NextRequest) {
  const response = NextResponse.next()

  const supabase = createServerClient(
    process.env.NEXT_PUBLIC_SUPABASE_URL!,
    process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!,
    { cookies: { /* configuração de cookies */ } }
  )

  const { data: { user } } = await supabase.auth.getUser()

  // Se não está logado e está tentando acessar o dashboard
  if (!user && request.nextUrl.pathname.startsWith('/dashboard')) {
    return NextResponse.redirect(new URL('/login', request.url))
  }

  // Se está logado e tenta acessar o login, manda para o dashboard
  if (user && request.nextUrl.pathname === '/login') {
    return NextResponse.redirect(new URL('/dashboard', request.url))
  }

  return response
}

// Define quais rotas o middleware vai interceptar
export const config = {
  matcher: ['/((?!_next/static|_next/image|favicon.ico).*)']
}
```

### O que o `matcher` faz

O matcher diz ao middleware quais URLs ele deve verificar. A expressão acima diz: "intercepte tudo, exceto arquivos estáticos do Next.js". Você não quer que o middleware rode em toda imagem e CSS, só nas páginas reais.

---

## 10. Variáveis de Ambiente — segredos do projeto

### O que são

Variáveis de ambiente são informações sensíveis que você **não coloca no código** — como chaves de API, credenciais do banco, senhas. Elas ficam em arquivos separados que não vão para o Git.

### Os arquivos

```
.env.local          ← suas variáveis locais (NUNCA sobe para o Git)
.env.example        ← exemplo sem os valores reais (sobe para o Git)
.env.production     ← opcional, para valores específicos de produção
```

**Sempre adicione `.env.local` no `.gitignore`** — ele já vem lá por padrão no Next.js, mas confirme.

### A regra do prefixo `NEXT_PUBLIC_`

| Variável | Acessível onde |
|---|---|
| `MINHA_VARIAVEL` | Apenas no servidor (Server Components, Route Handlers, Middleware) |
| `NEXT_PUBLIC_MINHA_VARIAVEL` | No servidor E no navegador (Client Components) |

**Regra de segurança:** Nunca coloque chaves secretas com prefixo `NEXT_PUBLIC_`. Qualquer pessoa que abrir o DevTools do navegador vai ver.

### Exemplo para o projeto

```env
# .env.local

# Supabase — públicas (cliente pode ver, são do anon key)
NEXT_PUBLIC_SUPABASE_URL=https://xxxxxxxxxxxx.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...

# Supabase — SECRETA (apenas servidor, nunca no cliente)
SUPABASE_SERVICE_ROLE_KEY=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

> ⚠️ A `SERVICE_ROLE_KEY` do Supabase bypassa todas as políticas RLS. **Jamais** coloque ela com `NEXT_PUBLIC_`. Use ela apenas em Route Handlers e funções server-side quando precisar de acesso administrativo.

### Como acessar no código

```ts
// No servidor (Server Component, Route Handler):
const url = process.env.NEXT_PUBLIC_SUPABASE_URL
const serviceKey = process.env.SUPABASE_SERVICE_ROLE_KEY

// No cliente (Client Component):
const url = process.env.NEXT_PUBLIC_SUPABASE_URL
// process.env.SUPABASE_SERVICE_ROLE_KEY → undefined (correto! não vaza)
```

---

## 11. Imagens, Fontes e Assets

### O componente `<Image>` do Next.js

**Nunca use `<img>` diretamente no Next.js.** Use o componente `<Image>` do pacote `next/image`. Ele automaticamente:
- Otimiza o tamanho da imagem
- Converte para formatos modernos (WebP, AVIF)
- Carrega com lazy loading
- Evita layout shift (aquele "pulo" quando a imagem carrega)

```tsx
import Image from 'next/image'

// Imagem local (da pasta public/)
<Image
  src="/logo.png"
  alt="Logo da academia"
  width={200}
  height={80}
/>

// Imagem de URL externa (Supabase Storage, por exemplo)
<Image
  src={aluno.avatar_url}
  alt={aluno.nome}
  width={48}
  height={48}
  className="rounded-full"
/>
```

Para imagens externas, você precisa configurar os domínios permitidos em `next.config.ts`:

```ts
// next.config.ts
const nextConfig = {
  images: {
    remotePatterns: [
      {
        protocol: 'https',
        hostname: '*.supabase.co', // permite imagens do Supabase Storage
      }
    ]
  }
}
```

### Fontes com `next/font`

O Next.js tem um sistema de fontes embutido que baixa a fonte uma vez e serve do próprio servidor, evitando requisições externas ao Google Fonts (mais rápido e mais privado).

```tsx
// app/layout.tsx
import { Inter, Roboto_Mono } from 'next/font/google'

const inter = Inter({
  subsets: ['latin'],
  variable: '--font-inter', // cria uma variável CSS
})

export default function RootLayout({ children }) {
  return (
    <html className={inter.variable}>
      <body>{children}</body>
    </html>
  )
}
```

### Arquivos estáticos (pasta `public/`)

Tudo que você colocar na pasta `public/` fica acessível pela URL diretamente:

```
public/
  logo.png        → acessível em /logo.png
  favicon.ico     → acessível em /favicon.ico
  icons/
    apple.png     → acessível em /icons/apple.png
```

---

## 12. Metadata e SEO

### Como configurar título e descrição de páginas

No App Router, você exporta um objeto `metadata` da sua `page.tsx` ou `layout.tsx`:

```tsx
// app/alunos/page.tsx
import type { Metadata } from 'next'

export const metadata: Metadata = {
  title: 'Alunos | Academia Manager',
  description: 'Gerencie os alunos da sua academia',
}

export default function PaginaAlunos() {
  return <div>...</div>
}
```

### Metadata dinâmica (baseada em dados)

Para páginas com parâmetros na URL (como o perfil de um aluno específico):

```tsx
// app/alunos/[id]/page.tsx
export async function generateMetadata({ params }: { params: { id: string } }) {
  const aluno = await buscarAluno(params.id)

  return {
    title: `${aluno.nome} | Academia Manager`,
  }
}
```

---

## 13. Desenvolvimento Local — o dia a dia

### Iniciando o projeto

```bash
# Cria um novo projeto Next.js com TypeScript, Tailwind e App Router
npx create-next-app@latest nome-do-projeto

# Entra na pasta
cd nome-do-projeto

# Inicia o servidor de desenvolvimento
npm run dev
```

O servidor sobe em `http://localhost:3000` por padrão.

### O hot reload

Quando você salva um arquivo, o Next.js atualiza o navegador automaticamente. Isso funciona para:
- Mudanças de UI: atualiza só o componente alterado (Fast Refresh)
- Mudanças de lógica: recarrega a página

### A estrutura inicial do projeto

```
meu-projeto/
├── src/
│   ├── app/
│   │   ├── layout.tsx       ← layout raiz
│   │   ├── page.tsx         ← página inicial (/)
│   │   └── globals.css      ← CSS global
│   └── components/
├── public/                  ← arquivos estáticos
├── next.config.ts           ← configurações do Next.js
├── tailwind.config.ts       ← configurações do Tailwind
├── tsconfig.json            ← configurações do TypeScript
├── package.json
└── .env.local               ← suas variáveis de ambiente (não vai pro Git)
```

### Aliases de importação (`@/`)

O Next.js configura automaticamente o alias `@/` apontando para a pasta `src/`. Isso evita imports com `../../../`:

```tsx
// ❌ Sem alias — confuso e frágil
import { Button } from '../../../components/ui/button'

// ✅ Com alias — sempre a partir da raiz do projeto
import { Button } from '@/components/ui/button'
```

### Comandos que você vai usar todo dia

```bash
npm run dev      # Inicia o servidor de desenvolvimento
npm run build    # Gera a versão de produção
npm run start    # Inicia o servidor com a build de produção (para testar localmente)
npm run lint     # Verifica erros de código com ESLint
```

### Inspecionando erros

- Erros de Server Components aparecem **no terminal** onde o `npm run dev` está rodando
- Erros de Client Components aparecem **no console do navegador** (F12)
- Erros de compilação TypeScript aparecem **no terminal** e **no editor**

---

## 14. Build e Produção — o que acontece quando você publica

### O que o `npm run build` faz

Quando você roda o build, o Next.js:

1. Compila todo o TypeScript para JavaScript
2. Analisa cada página e decide como ela vai ser servida
3. Otimiza imagens, CSS e JavaScript
4. Gera os arquivos finais na pasta `.next/`

### Os três tipos de renderização

O Next.js decide automaticamente como cada página vai funcionar em produção:

**Static (Estático)**
- A página é gerada uma vez no build e servida como arquivo HTML
- Mais rápido possível — sem servidor, sem banco de dados no momento da requisição
- Exemplo: página de landing, termos de uso

**Dynamic (Dinâmico)**
- A página é gerada no servidor a cada requisição
- Necessário quando os dados mudam frequentemente ou dependem do usuário logado
- Exemplo: dashboard com dados em tempo real, perfil do aluno

**Incremental Static Regeneration (ISR)**
- Começa estático, mas regenera de tempos em tempos
- Bom para dados que mudam, mas não em tempo real
- Exemplo: página de planos da academia (muda raramente)

### Como o Next.js decide

Se sua página usa `cookies()`, `headers()`, ou dados que dependem do usuário, o Next.js automaticamente a torna dinâmica. Se não usa nada disso, pode ficar estática.

Para forçar uma página a ser sempre dinâmica:

```tsx
// page.tsx
export const dynamic = 'force-dynamic'
```

### O que vai para produção

Após o build, a pasta `.next/` contém tudo. **Você não edita nada lá.** É gerado automaticamente e ignorado pelo Git.

---

## 15. Deploy na Vercel — o jeito mais simples

### Por que Vercel

A Vercel é a empresa que criou o Next.js. O deploy lá é:
- Gratuito para projetos pessoais/pequenos
- Automático: a cada `git push`, um novo deploy acontece
- Zero configuração: ela detecta que é Next.js e sabe o que fazer
- HTTPS automático
- CDN global (seu site fica rápido em qualquer país)

### Como fazer o deploy

1. Crie uma conta em [vercel.com](https://vercel.com)
2. Conecte com seu GitHub
3. Importe o repositório do projeto
4. Configure as variáveis de ambiente (as mesmas do `.env.local`)
5. Clique em Deploy

Pronto. A URL estará no ar em menos de 2 minutos.

### Ambientes na Vercel

A Vercel cria três ambientes automaticamente:

| Ambiente | Quando aparece | URL |
|---|---|---|
| **Production** | Quando você faz push na branch `main` | `seu-projeto.vercel.app` |
| **Preview** | Quando você abre um Pull Request | URL temporária única |
| **Development** | Seu `localhost:3000` local | — |

Cada ambiente pode ter variáveis de ambiente diferentes. Configuração financeira de teste no Preview, configuração real na Production.

### Variáveis de ambiente na Vercel

Na dashboard da Vercel, vá em **Settings → Environment Variables** e adicione as mesmas variáveis do seu `.env.local`. Não se esqueça de adicionar as variáveis de produção do Supabase (que podem ser diferentes das de desenvolvimento).

---

## 16. Erros comuns e como evitar

### ❌ Usar hooks em Server Components

```tsx
// ERRADO — vai quebrar
export default async function Pagina() {
  const [valor, setValor] = useState('') // ← hooks não existem em Server Components
  ...
}

// CORRETO — adicionar 'use client' ou mover o estado para um Client Component filho
'use client'
export default function Pagina() {
  const [valor, setValor] = useState('')
  ...
}
```

### ❌ Acessar `window` ou `document` no servidor

```tsx
// ERRADO — window não existe no servidor
export default function Pagina() {
  const largura = window.innerWidth // ← quebra no servidor
}

// CORRETO — dentro de useEffect (que só roda no cliente) ou verificar
useEffect(() => {
  const largura = window.innerWidth // ← seguro aqui
}, [])
```

### ❌ Importar um pacote de cliente em um Server Component

Algumas bibliotecas (ex: bibliotecas de gráficos com animação, libs que usam `window`) só funcionam no cliente. Se você importar em um Server Component, vai quebrar.

**Solução:** crie um Client Component que usa a biblioteca e importe esse componente no Server Component.

### ❌ Esquecer o `await` em Server Components

```tsx
// ERRADO — dados não vão estar disponíveis
export default async function Pagina() {
  const alunos = buscarAlunos() // ← esqueceu o await
  return <Lista itens={alunos} /> // alunos é uma Promise, não um array
}

// CORRETO
export default async function Pagina() {
  const alunos = await buscarAlunos() // ← correto
  return <Lista itens={alunos} />
}
```

### ❌ Passar objetos não serializáveis de Server para Client Components

Server Components e Client Components se comunicam por props. Essas props precisam ser serializáveis (convertíveis para JSON).

```tsx
// ERRADO — funções não são serializáveis
<ClientComponent onAction={() => console.log('oi')} /> // ← pode dar warning

// CORRETO — defina a função dentro do Client Component
```

### ❌ Colocar o `QueryClientProvider` dentro de um Server Component sem `'use client'`

```tsx
// ERRADO — QueryClientProvider é um Client Component
// Se o arquivo que o usa não tem 'use client', vai quebrar

// CORRETO — crie um arquivo separado para o Provider
// components/providers/QueryProvider.tsx
'use client'

import { QueryClient, QueryClientProvider } from '@tanstack/react-query'
import { useState } from 'react'

export function QueryProvider({ children }: { children: React.ReactNode }) {
  const [queryClient] = useState(() => new QueryClient())
  return <QueryClientProvider client={queryClient}>{children}</QueryClientProvider>
}
```

---

## 17. Boas práticas gerais

### Organização de arquivos

- Mantenha arquivos de página (`page.tsx`) simples — eles são o ponto de entrada, não o lugar de lógica complexa
- Lógica fica em hooks, lógica de dados fica em services
- Componentes grandes: quebre em componentes menores

### Performance

- Prefira Server Components para dados iniciais da página
- Use `loading.tsx` para dar feedback visual durante carregamentos
- Use `<Image>` sempre ao invés de `<img>`
- Não importe bibliotecas inteiras quando precisa de uma função só:

```tsx
// ❌ Importa a biblioteca inteira
import _ from 'lodash'
const resultado = _.chunk(array, 3)

// ✅ Importa só o que precisa
import chunk from 'lodash/chunk'
const resultado = chunk(array, 3)
```

### TypeScript

- Nunca use `any`. Se você não sabe o tipo, pesquise ou use `unknown`
- Crie interfaces para os dados que vêm do Supabase
- Use os tipos gerados automaticamente pelo Supabase CLI quando possível

### Segurança

- Sempre valide dados no servidor antes de salvar no banco
- Nunca confie em dados vindos do cliente sem validar (use Zod)
- Variáveis secretas: nunca com `NEXT_PUBLIC_`
- A `SERVICE_ROLE_KEY` do Supabase: apenas em Route Handlers e funções server-side

### Git

- Nunca faça commit do `.env.local`
- Mantenha um `.env.example` com as chaves mas sem os valores
- Use branches para features novas — não desenvolva direto na `main`

---

## 18. Glossário rápido

| Termo | O que significa |
|---|---|
| **App Router** | Sistema de rotas do Next.js 13+ baseado na pasta `app/` |
| **Server Component** | Componente que roda no servidor, sem JavaScript no navegador |
| **Client Component** | Componente que roda no navegador, declarado com `'use client'` |
| **SSR (Server Side Rendering)** | A página é gerada no servidor a cada requisição |
| **SSG (Static Site Generation)** | A página é gerada uma vez no build e servida como arquivo estático |
| **ISR** | Regeneração estática incremental — estático com atualização periódica |
| **Hydration** | Processo de "ligar" os eventos e estado JavaScript numa página já renderizada pelo servidor |
| **Route Handler** | Arquivo `route.ts` que cria um endpoint de API |
| **Middleware** | Código que intercepta requisições antes de chegar na página |
| **Layout** | Componente que envolve páginas e persiste na navegação |
| **Streaming** | Técnica de enviar partes da página ao navegador conforme ficam prontas |
| **Bundle** | Arquivo JavaScript final gerado pelo build que vai para o navegador |
| **Hot Reload / Fast Refresh** | Atualização automática do navegador ao salvar arquivos durante o desenvolvimento |
| **`.next/`** | Pasta gerada pelo build — nunca edite manualmente |
| **`public/`** | Pasta para arquivos estáticos acessíveis pela URL diretamente |
| **Alias `@/`** | Atalho para importações a partir da pasta `src/` |

---

## Para continuar aprendendo

- **Documentação oficial (em inglês):** [nextjs.org/docs](https://nextjs.org/docs) — a melhor referência, muito bem escrita
- **Documentação do Supabase com Next.js:** [supabase.com/docs/guides/getting-started/quickstarts/nextjs](https://supabase.com/docs/guides/getting-started/quickstarts/nextjs)
- **Tanstack Query com Next.js:** [tanstack.com/query/latest](https://tanstack.com/query/latest)

> 💡 **Dica final:** Quando travar em algum erro, cole a mensagem de erro exata no Google entre aspas. Erros do Next.js são muito comuns e alguém já passou pela mesma situação.
