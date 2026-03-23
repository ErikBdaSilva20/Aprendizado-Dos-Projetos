# Convex — Backend em Tempo Real

## Conceitos principais

- **Convex** — banco de dados + backend serverless em tempo real
- **Schema** — define a estrutura das tabelas
- **Query** — lê dados do banco (como um GET, sem efeitos colaterais)
- **Mutation** — escreve/altera dados no banco (como POST/PUT/DELETE)
- **`useQuery`** — hook React que assina uma query e atualiza automaticamente
- **`useMutation`** — hook React que retorna uma função para chamar uma mutation
- **`api`** — objeto gerado automaticamente com referências para todas as funções

---

## Por que o Convex existe?

O problema com apps tradicionais: você precisa de um servidor Node/Express, um banco SQL/NoSQL, uma API REST ou GraphQL, e ainda lidar com sincronização em tempo real (WebSockets). São 4 problemas separados.

**O Convex resolve todos ao mesmo tempo:**
- Banco de dados embutido (NoSQL reativo)
- Funções serverless (queries e mutations) que rodam no servidor da Convex
- Sincronização automática em tempo real (quando alguém muda algo, todos os clients veem)
- TypeScript de ponta a ponta (tipos gerados automaticamente)

---

## O Schema — `convex/schema.ts`

Define todas as tabelas do banco do Zyntra. É como o "modelo" do banco:

```ts
// convex/schema.ts
import { defineSchema, defineTable } from 'convex/server';
import { v } from 'convex/values'; // v = validators de tipo

export default defineSchema({
  users: defineTable({
    email: v.string(),
    name: v.string(),
    role: v.union(v.literal('admin'), v.literal('doctor'), v.literal('receptionist')),
    doctorId: v.optional(v.id('doctors')), // referência para outra tabela
    createdAt: v.number(),
  }).index('by_email', ['email']), // índice para busca rápida por email

  appointments: defineTable({
    patientId: v.id('patients'), // v.id('tabela') = foreign key
    doctorId: v.id('doctors'),
    date: v.string(),
    startTime: v.string(),
    status: v.union(
      v.literal('pending'),
      v.literal('confirmed'),
      v.literal('cancelled'),
      v.literal('completed')
    ),
  })
    .index('by_doctor', ['doctorId'])
    .index('by_date', ['date']),
});
```

### Validadores disponíveis (`v.algo`)

| Validador | Tipo |
|---|---|
| `v.string()` | texto |
| `v.number()` | número |
| `v.boolean()` | verdadeiro/falso |
| `v.id('tabela')` | referência para outra tabela |
| `v.optional(v.string())` | campo que pode não existir |
| `v.union(v.literal('a'), v.literal('b'))` | só aceita 'a' ou 'b' |
| `v.array(v.string())` | lista de textos |

---

## Queries — Lendo dados

Uma query é uma função que **lê** dados. Ela nunca altera nada.

```ts
// convex/appointments.ts
import { query } from './_generated/server';
import { v } from 'convex/values';

export const list = query({
  args: {
    date: v.optional(v.string()), // argumento opcional
    status: v.optional(v.string()),
  },
  handler: async (ctx, args) => {
    // ctx.db = acesso ao banco
    let appointments = await ctx.db
      .query('appointments')       // qual tabela
      .withIndex('by_date', q =>   // qual índice usar
        q.eq('date', args.date!)   // filtro
      )
      .collect();                  // executa e retorna array

    return appointments;
  },
});
```

### Métodos de consulta no `ctx.db`

```ts
// Buscar todos com filtro
await ctx.db.query('tabela').collect();

// Usando índice para filtro eficiente
await ctx.db.query('tabela')
  .withIndex('by_status', q => q.eq('status', 'active'))
  .collect();

// Buscar um registro por ID
await ctx.db.get(id); // retorna o registro ou null

// Limitar quantidade
await ctx.db.query('tabela').order('desc').take(20);

// Filtro adicional (após índice)
.filter(q => q.eq(q.field('campo'), valor))
```

---

## Mutations — Escrevendo dados

Uma mutation é uma função que **altera** dados (inserir, atualizar, deletar):

```ts
// convex/appointments.ts
import { mutation } from './_generated/server';

export const create = mutation({
  args: {
    patientId: v.id('patients'),
    doctorId: v.id('doctors'),
    date: v.string(),
    startTime: v.string(),
    endTime: v.string(),
    visitType: v.string(),
  },
  handler: async (ctx, args) => {
    // Inserir novo registro
    const id = await ctx.db.insert('appointments', {
      ...args,
      status: 'pending',
      createdAt: Date.now(),
    });

    return id; // retorna o ID gerado
  },
});

export const updateStatus = mutation({
  args: {
    id: v.id('appointments'),
    status: v.union(v.literal('confirmed'), v.literal('cancelled')),
  },
  handler: async (ctx, { id, status }) => {
    // Atualizar campos específicos
    await ctx.db.patch(id, { status });

    // Para deletar:
    // await ctx.db.delete(id);
  },
});
```

---

## Usando no React — `useQuery` e `useMutation`

```tsx
"use client";

import { useQuery, useMutation } from "convex/react";
import { api } from "@/convex/_generated/api"; // ← auto-gerado pelo Convex

export function AppointmentsList() {
  // useQuery: assina a query, atualiza automaticamente quando dado muda
  const appointments = useQuery(api.appointments.list, {
    status: "pending",  // argumentos da query
  });

  // useMutation: retorna uma função para chamar a mutation
  const updateStatus = useMutation(api.appointments.updateStatus);

  // appointments é undefined enquanto carrega
  if (appointments === undefined) return <div>Carregando...</div>;

  return (
    <ul>
      {appointments.map(apt => (
        <li key={apt._id}>
          {apt.date}
          <button onClick={() => updateStatus({ id: apt._id, status: "confirmed" })}>
            Confirmar
          </button>
        </li>
      ))}
    </ul>
  );
}
```

### Estados do `useQuery`

| Valor | Significado |
|---|---|
| `undefined` | ainda carregando |
| `null` | query retornou nulo explicitamente |
| `[]` | lista vazia (carregou, mas sem dados) |
| `[...]` | dados disponíveis |

---

## O objeto `api` — como funciona

O arquivo `convex/_generated/api.ts` é gerado automaticamente pelo Convex ao rodar `npx convex dev`. Ele mapeia todas as funções:

```ts
// Gerado automaticamente — não edite este arquivo!
api.appointments.list      // → convex/appointments.ts → export const list
api.appointments.create    // → convex/appointments.ts → export const create
api.dashboard.getStats     // → convex/dashboard.ts → export const getStats
api.settings.getUserProfile // → convex/settings.ts → export const getUserProfile
```

> **Importante:** sempre que você criar uma nova função no Convex, o `api` é atualizado automaticamente enquanto `npx convex dev` está rodando.

---

## Registro real no banco

Todo registro no Convex recebe automaticamente dois campos extras:
- `_id` — ID único gerado pelo Convex (tipo `Id<'tabela'>`)
- `_creationTime` — timestamp de criação em milissegundos

```ts
// Exemplo de appointment retornado pelo banco:
{
  _id: "jd7abc123...",        // ← gerado pelo Convex
  _creationTime: 1742745600,  // ← gerado pelo Convex
  patientId: "abc...",
  doctorId: "xyz...",
  date: "2026-03-23",
  status: "pending",
  createdAt: 1742745600,      // ← o que o código inseriu
}
```

---

## Exemplo real do projeto — `useAppointments.ts`

```ts
// src/modules/appointments/infrastructure/convex/hooks/useAppointments.ts
"use client";

import { useQuery, useMutation } from "convex/react";
import { api } from "@/convex/_generated/api";
import { toast } from "sonner"; // notificação toast

export function useAppointments(filters?) {
  // assina a lista de appointments com filtros
  const appointments = useQuery(api.appointments.list, {
    doctorId: filters?.doctorId,
    status: filters?.status,
    limit: filters?.limit ?? 20,
  });

  const updateStatusMutation = useMutation(api.appointments.updateStatus);

  const confirm = async (id) => {
    try {
      await updateStatusMutation({ id, status: "confirmed" });
      toast.success("Consulta confirmada.");
    } catch (e) {
      toast.error(e.message);
    }
  };

  return {
    appointments: appointments ?? [], // nunca retorna undefined
    isLoading: appointments === undefined,
    confirm,
  };
}
```

---

## Problemas comuns

### ❌ "Could not find public function for 'auth:isAuthenticated'"
**Causa:** o `npx convex dev` não está rodando, então as funções não foram sincronizadas.  
**Solução:** rode `npx convex dev` em um terminal separado.

### ❌ Dados não atualizam na tela
**Causa:** você pode estar usando `fetch` em vez de `useQuery`.  
**Solução:** `useQuery` do Convex é reativo — use-o sempre para leitura.

### ❌ Typescript reclama do tipo de `_id`
**Causa:** o tipo de ID no Convex é `Id<'tabela'>`, não `string`.  
**Solução:** use `import type { Id } from "@/convex/_generated/dataModel"` e tipagem `Id<"appointments">`.

---

## Boas práticas

- Sempre use `?? []` ou `?? null` ao usar `useQuery` para evitar trabalhar com `undefined`
- Índices no schema são obrigatórios para queries eficientes — nunca faça `.collect()` sem index em tabelas grandes
- Queries nunca devem ter efeitos colaterais (não chamam `ctx.db.insert` dentro de queries)
- Use `ctx.db.patch()` para atualizar campos específicos — `ctx.db.replace()` substitui o documento inteiro
