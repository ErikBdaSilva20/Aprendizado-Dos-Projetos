# TypeScript — Tipagem Estática

## Conceitos principais

- **Tipo** — define o "formato" de uma variável ou dado
- **Interface** — descreve a forma de um objeto
- **`type`** — alias para descrever tipos, similar à interface
- **Genérico (`<T>`)** — tipo flexível que se adapta ao que você passa
- **`optional` (`?`)** — campo que pode ou não existir
- **`union` (`|`)** — aceita um tipo OU outro
- **`as`** — conversão forçada de tipo (type casting)
- **`typeof`** / **`keyof`** — utilitários para extrair tipos dinamicamente

---

## Por que TypeScript existe?

JavaScript puro deixa você cometer erros silenciosos:

```js
// JavaScript puro — nenhum erro até executar
const user = { name: "Erik", age: 25 };
console.log(user.emal); // ← typo! Retorna undefined sem avisar
user.doSomething();     // ← só vai falhar em runtime
```

Com TypeScript, o editor avisa **antes de executar**:

```ts
// TypeScript — erro aparece enquanto você digita
const user = { name: "Erik", age: 25 };
console.log(user.emal);      // ❌ Erro: Property 'emal' does not exist
user.doSomething();          // ❌ Erro: Property 'doSomething' does not exist
```

---

## Tipos Básicos

```ts
// Tipos primitivos
const nome: string = "Erik";
const idade: number = 25;
const ativo: boolean = true;

// Arrays
const lista: string[] = ["a", "b", "c"];
const numeros: number[] = [1, 2, 3];

// Null e undefined
const talvez: string | null = null;
const opcional: string | undefined = undefined;
```

---

## Interface — Descrevendo Objetos

```ts
// Define o "contrato" de um objeto
interface User {
  id: string;
  name: string;
  email: string;
  role: "admin" | "doctor" | "receptionist"; // só aceita esses valores
  phone?: string; // ← o "?" = campo opcional
  createdAt: number;
}

// Usando a interface
function greetUser(user: User): string {
  return `Olá, ${user.name}`;
}
```

---

## Type vs Interface

São quase iguais. No projeto você vai ver os dois:

```ts
// Interface — mais usada para objetos e classes
interface IAppointmentRepository {
  findById(id: string): Promise<Appointment | null>;
  save(appointment: Appointment): Promise<string>;
}

// Type — mais flexível, pode fazer unions e intersections
type AppointmentStatus = "pending" | "confirmed" | "cancelled" | "completed";
type Id<T extends string> = string & { __type: T }; // exemplo de genérico
```

> **Convenção do projeto:** interfaces de repositórios começam com `I` (ex: `IAppointmentRepository`).

---

## Genéricos `<T>` — Tipos Flexíveis

Genéricos são como "variáveis de tipo". Permitem criar funções e interfaces reutilizáveis:

```ts
// Sem genérico — duplicação de código
interface ResultadoStrings {
  data: string[];
  total: number;
}
interface ResultadoNumbers {
  data: number[];
  total: number;
}

// Com genérico — reutilizável
interface PaginatedResult<T> { // T = qualquer tipo
  data: T[];
  total: number;
}

// Usando
const resultado: PaginatedResult<Appointment> = {
  data: [...appointments],
  total: 50,
};
```

Isso está no projeto em:
```ts
// src/modules/appointments/domain/ports/IAppointmentRepository.ts
export interface PaginatedResult<T> {
  data: T[];
  total: number;
}
```

---

## Promise e async/await

Como quase tudo no projeto é assíncrono (banco, API), você precisa entender isso:

```ts
// Sem async/await (difícil de ler)
function buscarUsuario(id: string): Promise<User> {
  return ctx.db.get(id).then(user => {
    if (!user) throw new Error("Not found");
    return user;
  });
}

// Com async/await (o projeto usa isso)
async function buscarUsuario(id: string): Promise<User> {
  const user = await ctx.db.get(id); // "espera" a promise resolver
  if (!user) throw new Error("Not found");
  return user;
}
```

**Tipando o retorno:**
```ts
// Promise<Appointment | null> = "vai retornar um Appointment ou null"
async function findById(id: string): Promise<Appointment | null> {
  return await ctx.db.get(id);
}
```

---

## Type Casting com `as`

Às vezes você sabe o tipo de algo mas o TypeScript não sabe. Use `as`:

```ts
// No projeto — converte string para Id do Convex
await updateUserRoleMutation({ id: id as Id<"users">, newRole });
//                                   ↑ força a interpretação como Id<"users">

// Variáveis de ambiente (string | undefined → string)
const url = process.env.NEXT_PUBLIC_CONVEX_URL as string;
```

> ⚠️ Use `as` com cuidado — você está "mentindo" pro TypeScript. Use só quando tem certeza do tipo.

---

## Exemplos reais do projeto

### UseCase tipado
```ts
// src/modules/appointments/application/use-cases/CreateAppointmentUseCase.ts
export class CreateAppointmentUseCase {
  constructor(
    private readonly appointmentRepo: IAppointmentRepository, // interface como tipo
    private readonly doctorRepo: IDoctorRepository,
  ) {}

  async execute(dto: CreateAppointmentDTO): Promise<AppointmentDTO> {
    //           ↑ recebe DTO tipado     ↑ retorna DTO tipado
    const validated = CreateAppointmentSchema.parse(dto);
    const appointment = Appointment.create(validated);
    const id = await this.appointmentRepo.save(appointment);

    return {
      id,
      patientId: appointment.patientId.value, // TypeScript sabe que existe
      status: appointment.status.value,
    };
  }
}
```

### Hook tipado com Convex
```ts
// src/modules/appointments/infrastructure/convex/hooks/useAppointments.ts
import { Id } from "@/convex/_generated/dataModel";

export function useAppointments(filters?: {
  doctorId?: Id<"doctors">;   // tipo específico do Convex
  patientId?: Id<"patients">;
  status?: string;
}) {
  const appointments = useQuery(api.appointments.list, {
    doctorId: filters?.doctorId, // ?. = "acessa só se filters existir"
  });

  return {
    appointments: appointments ?? [], // ?? = "se undefined, usa []"
    isLoading: appointments === undefined,
  };
}
```

---

## Operadores Úteis

```ts
// Optional chaining ?. — acessa sem erro se for null/undefined
const name = user?.profile?.name; // não quebra se user for null

// Nullish coalescing ?? — fallback para null/undefined
const list = appointments ?? []; // se undefined ou null, usa []

// Non-null assertion ! — avisa que não é null (você garante)
const date = args.date!; // você garante que date existe aqui

// Logical AND && — acessa condicionalmente
const doctor = user.role === "doctor" && user.doctorId;
```

---

## Problemas comuns

### ❌ "Type 'string' is not assignable to type 'Id<"users">'"
**Causa:** o Convex usa tipos de ID específicos, não string simples.  
**Solução:** use `id as Id<"users">` ou declare o tipo corretamente.

### ❌ "Property 'X' does not exist on type 'undefined'"
**Causa:** `useQuery` retorna `undefined` enquanto carrega.  
**Solução:** sempre trate o `undefined`: `if (!data) return ...` ou `data ?? []`.

### ❌ "Object is possibly null"
**Causa:** o TypeScript sabe que algo pode ser nulo.  
**Solução:** adicione verificação: `if (!user) throw new Error(...)` ou use `?.`.

---

## Boas práticas

- Sempre tipar parâmetros de funções e retornos de `async function`
- Prefira `interface` para contratos de objetos, `type` para unions e aliases simples
- Nunca use `any` — isso desativa a tipagem completamente
- Use `Id<"tabela">` do Convex para IDs, não `string`
- Prefira `?? []` a `|| []` para arrays — `||` trata `0` e `""` como falsy
