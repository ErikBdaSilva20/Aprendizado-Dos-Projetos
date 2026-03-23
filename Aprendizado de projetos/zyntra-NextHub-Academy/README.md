# 📚 Learn/zyntra — Base de Conhecimento do Projeto

> Essa pasta é um mapa de estudo gerado a partir do código real do projeto **Zyntra Boilerplate**.  
> Cada arquivo documenta uma tecnologia ou conceito com exemplos tirados diretamente daqui.

---

## 🗺️ O que é esse projeto?

**Zyntra** é um sistema de gestão de clínicas médicas. Ele permite:
- Gerenciar consultas (agendamentos)
- Cadastrar médicos e pacientes
- Controlar faturamento e cobranças
- Notificações e configurações da clínica

A stack é **moderna e robusta**, usando:

| Tecnologia | Papel no Projeto |
|---|---|
| Next.js 16 | Framework de front-end + roteamento |
| React 19 | UI/componentes |
| Convex | Backend-as-a-service (banco + API em tempo real) |
| TypeScript | Tipagem estática em todo o código |
| Tailwind CSS 4 | Estilização |
| Shadcn/ui | Componentes prontos |
| Zod | Validação de dados |
| React Hook Form | Formulários |
| Convex Auth | Autenticação |
| Hexagonal Architecture + DDD | Padrão arquitetural |

---

## 📁 Estrutura desta Base de Conhecimento

```
zyntra-NextHub-Academy/
├── README.md              ← você está aqui
├── 01-nextjs.md           ← Next.js: App Router, layouts, rotas
├── 02-convex.md           ← Convex: banco, queries, mutations
├── 03-typescript.md       ← TypeScript: tipos, interfaces, genéricos
├── 04-architecture.md     ← Arquitetura: DDD + Hexagonal + Clean
├── 05-react.md            ← React 19: hooks, providers, contexto
├── 06-auth.md             ← Autenticação com Convex Auth
├── 07-zod-forms.md        ← Validação com Zod + React Hook Form
└── 08-patterns.md         ← Padrões: Use Cases, Repository, DTOs
```

---

## 🧭 Por onde começar?

1. **Completo iniciante no projeto?** → Comece pelo [`01-nextjs.md`](./01-nextjs.md)
2. **Quer entender como os dados funcionam?** → [`02-convex.md`](./02-convex.md)
3. **Confuso com a estrutura de pastas?** → [`04-architecture.md`](./04-architecture.md)
4. **Não entende TypeScript?** → [`03-typescript.md`](./03-typescript.md)

---

## 🏗️ Visão Geral da Arquitetura

```
┌──────────────────────────────────────────────────────┐
│                    Next.js (App Router)              │
│  src/app/(private)/dashboard/...                     │
│  Páginas, Layouts, Componentes de UI                 │
└──────────────────────┬───────────────────────────────┘
                       │ usa hooks
┌──────────────────────▼───────────────────────────────┐
│              Módulos (src/modules/...)                │
│  ┌──────────┐  ┌─────────────┐  ┌─────────────────┐  │
│  │  domain  │  │ application │  │ infrastructure  │  │
│  │ entities │  │  use-cases  │  │  convex hooks   │  │
│  │  ports   │  │    DTOs     │  │   (adapters)    │  │
│  └──────────┘  └─────────────┘  └────────┬────────┘  │
└────────────────────────────────────────── │ ──────────┘
                                            │ chama
┌───────────────────────────────────────────▼──────────┐
│                    Convex Backend                     │
│  convex/schema.ts    → Define o banco de dados        │
│  convex/appointments.ts → queries e mutations        │
│  convex/auth.ts      → autenticação                  │
└──────────────────────────────────────────────────────┘
```

---

> **Regra do LearnAgent:** Se algo gerou dúvida durante o estudo → documente aqui!

---

> [!TIP]
> Use os links rápidos na tabela acima para navegar entre os tópicos.

[⬅️ Voltar para a Central de Aprendizado](../README.md)
