# React 19 — Hooks, Providers e Contexto

## Conceitos principais

- **Componente** — bloco de UI reutilizável (função que retorna JSX)
- **JSX** — HTML dentro do JavaScript/TypeScript
- **Props** — dados passados para um componente pelo "pai"
- **Hook** — função especial que adiciona estado e comportamento ao componente
- **Provider** — componente que distribui dados para todos os filhos da árvore
- **`useState`** — estado local do componente
- **`useEffect`** — efeitos colaterais (fetch, subscriptions, timers)
- **`useCallback`** / **`useMemo`** — otimizações de performance

---

## Por que o React existe?

Antes do React, manipular o DOM manualmente era trabalhoso e propenso a bugs. Atualizar uma lista de pacientes, por exemplo, exigia encontrar o elemento no DOM, criar novos elementos, remover os antigos...

O React resolve isso com um conceito simples: **descreva como a UI deve parecer dado um estado**, e o React cuida de atualizar o DOM eficientemente.

```tsx
// Você descreve o que quer:
function PatientList({ patients }) {
  return (
    <ul>
      {patients.map(p => <li key={p.id}>{p.name}</li>)}
    </ul>
  );
}

// React atualiza o DOM automaticamente quando `patients` mudar
```

---

## JSX — HTML dentro do TypeScript

```tsx
// JSX parece HTML, mas é TypeScript transformado
function Card({ title, children }: { title: string; children: React.ReactNode }) {
  return (
    <div className="card">         {/* className em vez de class */}
      <h2>{title}</h2>             {/* {} = expressão JavaScript */}
      <div>{children}</div>       {/* children = conteúdo interno */}
    </div>
  );
}

// Usando o componente
function App() {
  const nome = "Zyntra";
  return (
    <Card title={nome}>            {/* passando props */}
      <p>Conteúdo aqui</p>        {/* isso vira {children} */}
    </Card>
  );
}
```

---

## Props — Passando Dados

```tsx
// Definindo as props com TypeScript
interface ButtonProps {
  label: string;
  onClick: () => void;         // função sem argumentos, sem retorno
  disabled?: boolean;          // opcional
  variant?: "primary" | "danger";
}

function Button({ label, onClick, disabled = false, variant = "primary" }: ButtonProps) {
  return (
    <button
      onClick={onClick}
      disabled={disabled}
      className={variant === "danger" ? "btn-danger" : "btn-primary"}
    >
      {label}
    </button>
  );
}

// Usando
<Button label="Confirmar" onClick={() => console.log("clicou!")} />
<Button label="Cancelar" onClick={handleCancel} variant="danger" disabled={isLoading} />
```

---

## `useState` — Estado Local

```tsx
"use client";
import { useState } from "react";

function Counter() {
  // [valorAtual, funçãoParaAtualizar] = useState(valorInicial)
  const [count, setCount] = useState(0);
  const [name, setName] = useState("Erik");
  const [isOpen, setIsOpen] = useState(false);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>+</button>
      <button onClick={() => setCount(prev => prev - 1)}>-</button>
      {/* Usar função no setState quando o novo valor depende do anterior */}
    </div>
  );
}
```

**Regra crucial:** nunca mute o estado diretamente:
```tsx
// ❌ Errado — não detecta a mudança
state.value = "novo";
array.push(item);

// ✅ Correto — cria novo objeto/array
setState({ ...state, value: "novo" });
setArray([...array, item]);
```

---

## `useEffect` — Efeitos Colaterais

```tsx
"use client";
import { useEffect, useState } from "react";

function Timer() {
  const [seconds, setSeconds] = useState(0);

  // Executa após o render
  useEffect(() => {
    const interval = setInterval(() => {
      setSeconds(prev => prev + 1);
    }, 1000);

    // Cleanup: executa quando o componente é desmontado
    return () => clearInterval(interval);
  }, []); // [] = executa apenas uma vez (ao montar)

  return <p>Tempo: {seconds}s</p>;
}
```

**Dependency array:**
```tsx
useEffect(() => { ... }, []);          // roda 1x ao montar
useEffect(() => { ... }, [userId]);    // roda toda vez que userId mudar
useEffect(() => { ... });              // roda após CADA render (cuidado!)
```

> **No Zyntra,** você raramente vai precisar de `useEffect` para buscar dados — o `useQuery` do Convex já cuida disso automaticamente.

---

## Providers — Distribuindo Dados Globalmente

Providers permitem "injetar" dados em toda a árvore de componentes sem passar props manualmente (prop drilling):

```tsx
// Sem Provider — "prop drilling" (ruim)
function App() {
  const user = { name: "Erik" };
  return <Dashboard user={user} />;
}
function Dashboard({ user }) {
  return <Sidebar user={user} />;
}
function Sidebar({ user }) {
  return <UserMenu user={user} />; // Passou por 2 níveis sem usar
}

// Com Provider (como o projeto usa)
const UserContext = createContext(null);

function App() {
  const user = { name: "Erik" };
  return (
    <UserContext.Provider value={user}>
      <Dashboard /> {/* sem prop user! */}
    </UserContext.Provider>
  );
}
function UserMenu() {
  const user = useContext(UserContext); // acessa direto, onde quer que seja
  return <span>{user.name}</span>;
}
```

### Providers do Zyntra

```tsx
// src/app/layout.tsx — todos os providers ficam aqui
<ConvexAuthNextjsServerProvider>   {/* auth no servidor */}
  <html>
    <body>
      <ConvexClientProvider>       {/* banco em tempo real */}
        <TooltipProvider>          {/* tooltips globais */}
          {children}
        </TooltipProvider>
      </ConvexClientProvider>
    </body>
  </html>
</ConvexAuthNextjsServerProvider>
```

---

## Custom Hooks — Lógica Reutilizável

Custom hooks são funções que começam com `use` e encapsulam lógica reutilizável:

```tsx
// Hook customizado do projeto
// src/modules/appointments/infrastructure/convex/hooks/useAppointments.ts
"use client";

export function useAppointments(filters?) {
  const appointments = useQuery(api.appointments.list, filters);
  const updateStatus = useMutation(api.appointments.updateStatus);

  const confirm = async (id) => {
    try {
      await updateStatus({ id, status: "confirmed" });
      toast.success("Consulta confirmada.");
    } catch (e) {
      toast.error(e.message);
    }
  };

  // Retorna dados + ações encapsuladas
  return {
    appointments: appointments ?? [],
    isLoading: appointments === undefined,
    confirm,
  };
}

// Uso na página (muito limpo!)
function AppointmentsPage() {
  const { appointments, isLoading, confirm } = useAppointments({ status: "pending" });

  if (isLoading) return <Spinner />;
  return appointments.map(apt => (
    <Card key={apt._id}>
      <button onClick={() => confirm(apt._id)}>Confirmar</button>
    </Card>
  ));
}
```

---

## Renderização Condicional

```tsx
function AppointmentCard({ appointment }) {
  // if/else com early return
  if (!appointment) return null;

  return (
    <div>
      {/* Operador && — renderiza só se verdadeiro */}
      {appointment.notes && <p>Notas: {appointment.notes}</p>}

      {/* Operador ternário — if/else inline */}
      {appointment.status === "confirmed"
        ? <span className="green">Confirmada</span>
        : <span className="yellow">Pendente</span>
      }

      {/* Múltiplos casos */}
      {appointment.status === "completed" && <Badge>Concluída</Badge>}
      {appointment.status === "cancelled" && <Badge>Cancelada</Badge>}
    </div>
  );
}
```

---

## Listas e Keys

```tsx
function PatientList({ patients }) {
  return (
    <ul>
      {patients.map(patient => (
        // key é obrigatória! Use o ID único, nunca o índice do array
        <li key={patient._id}>
          {patient.name} — {patient.email}
        </li>
      ))}
    </ul>
  );
}
```

---

## React 19 — O que é novo

O projeto usa React 19, que trouxe algumas novidades importantes:

- **`use` hook** — lê Promises e Context diretamente em componentes
- **Actions** — tratamento de formulários simplificado
- **`useOptimistic`** — atualiza a UI antes da resposta do servidor (UX melhorada)
- **React Compiler** — otimização automática (configurado em `babel-plugin-react-compiler`)

---

## Problemas comuns

### ❌ "Hooks can only be called inside a function component"
**Causa:** você chamou um hook fora de um componente ou dentro de um `if`.  
**Solução:** hooks devem estar **sempre no nível mais alto** da função do componente.

### ❌ Componente não atualiza quando estado muda
**Causa:** você mutou o estado diretamente.  
**Solução:** sempre use `setState(novoValor)`, nunca `state.campo = valor`.

### ❌ "Maximum update depth exceeded" (loop infinito)
**Causa:** `useEffect` atualiza um estado que está no seu `[]` de dependências.  
**Solução:** revise as dependências do `useEffect`.

---

## Boas práticas

- Nomeie componentes com `PascalCase` e hooks com `camelCase` começando em `use`
- Componentes pequenos e focados — se ficar grande, quebre em componentes menores  
- Use custom hooks para extrair lógica dos componentes
- Prefira `useQuery` do Convex a `useEffect + fetch` para dados do banco
- Evite prop drilling — use Context ou passe pelo hook customizado
