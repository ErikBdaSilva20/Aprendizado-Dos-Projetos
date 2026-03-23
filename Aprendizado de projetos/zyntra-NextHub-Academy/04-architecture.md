# Arquitetura — DDD + Hexagonal + Clean

## Conceitos principais

- **DDD (Domain-Driven Design)** — o código é organizado ao redor do negócio, não da tecnologia
- **Hexagonal Architecture** — separa o núcleo do sistema (domínio) da infraestrutura (banco, UI)
- **Clean Architecture** — camadas com dependências que só apontam para dentro
- **Domain** — "o coração" — regras de negócio puras, sem dependência de framework
- **Application** — orquestra o domínio (Use Cases)
- **Infrastructure** — conecta com o mundo externo (Convex, APIs)
- **Port** — interface/contrato que define como se comunica com o exterior
- **Adapter** — implementação concreta de um Port

---

## Por que essa arquitetura existe?

Imagine um código onde React, Convex e regras de negócio estão misturados no mesmo arquivo. O que acontece?

- Difícil de testar: para testar uma regra, você precisa do banco rodando
- Difícil de trocar: se mudar de Convex para outro banco, refatora tudo
- Difícil de entender: qual é o código "importante" e qual é detalhe técnico?

A Hexagonal Architecture resolve isso: **o domínio não depende de nada externo**. Banco, framework e UI são "plugáveis".

---

## A estrutura de pastas do Zyntra

```
src/modules/
└── appointments/          ← módulo de consultas
    ├── domain/            ← regras de negócio puras
    │   ├── entities/
    │   │   └── Appointment.ts       ← a entidade central
    │   ├── value-objects/
    │   │   ├── AppointmentId.ts
    │   │   ├── AppointmentStatus.ts
    │   │   └── TimeSlot.ts
    │   ├── ports/
    │   │   └── IAppointmentRepository.ts  ← contrato do banco
    │   ├── errors/
    │   │   └── AppointmentStatusError.ts
    │   └── events/
    │       ├── AppointmentScheduled.ts
    │       └── AppointmentCancelled.ts
    ├── application/       ← orquestração (Use Cases)
    │   ├── use-cases/
    │   │   ├── CreateAppointmentUseCase.ts
    │   │   └── CancelAppointmentUseCase.ts
    │   └── dtos/
    │       └── CreateAppointmentDTO.ts
    └── infrastructure/    ← conexão com o mundo externo
        └── convex/
            └── hooks/
                └── useAppointments.ts   ← hook React + Convex
```

---

## Camada 1: Domain (O Coração)

O domínio contém as regras de negócio **sem depender de nada** — sem Convex, sem React, sem Next.js.

### Entidade — `Appointment.ts`

```ts
// src/modules/appointments/domain/entities/Appointment.ts

export class Appointment {
  private constructor(
    readonly id: AppointmentId,
    readonly patientId: PatientId,
    readonly doctorId: DoctorId,
    readonly timeSlot: TimeSlot,
    readonly visitType: string,
    private _status: AppointmentStatus,
  ) {}

  // Factory method — única forma de criar um Appointment
  static create(props: CreateAppointmentProps): Appointment {
    return new Appointment(
      AppointmentId.generate(),
      new PatientId(props.patientId),
      DoctorId.create(props.doctorId),
      TimeSlot.create(props.date, props.startTime, props.endTime),
      props.visitType,
      AppointmentStatus.pending(),
    );
  }

  // Regra de negócio: só pode confirmar se estiver pendente
  confirm(): void {
    if (this._status.value !== 'pending') {
      throw new AppointmentStatusError(`Cannot confirm appointment with status: ${this._status.value}`);
    }
    this._status = AppointmentStatus.confirmed();
  }

  // Regra de negócio: não pode cancelar uma consulta completa
  cancel(reason: string): void {
    if (this._status.value === 'completed') {
      throw new AppointmentStatusError('Cannot cancel a completed appointment');
    }
    if (!reason.trim()) throw new Error('Cancellation reason is required');
    this._status = AppointmentStatus.cancelled();
  }
}
```

**Por que `private constructor`?** Para forçar que o Appointment seja criado apenas via `Appointment.create()`, garantindo que todas as regras de negócio sejam aplicadas.

### Port (Interface) — `IAppointmentRepository.ts`

```ts
// src/modules/appointments/domain/ports/IAppointmentRepository.ts

// Define o "contrato" — o que o banco precisa saber fazer
// Não importa se é Convex, PostgreSQL, ou arquivo JSON
export interface IAppointmentRepository {
  findById(id: AppointmentId): Promise<Appointment | null>;
  findAll(filters?: AppointmentFilters): Promise<PaginatedResult<Appointment>>;
  findConflicts(doctorId: string, slot: TimeSlot): Promise<Appointment[]>;
  save(appointment: Appointment): Promise<string>;
  update(appointment: Appointment): Promise<void>;
}
```

> O domínio **conhece** a interface mas **nunca** a implementação. Isso é a inversão de dependência.

---

## Camada 2: Application (Use Cases)

Use Cases orquestram o domínio. Eles sabem **o que fazer**, não **como fazer**.

```ts
// src/modules/appointments/application/use-cases/CreateAppointmentUseCase.ts

export class CreateAppointmentUseCase {
  constructor(
    private readonly appointmentRepo: IAppointmentRepository, // ← interface, não implementação!
    private readonly doctorRepo: IDoctorRepository,
  ) {}

  async execute(dto: CreateAppointmentDTO): Promise<AppointmentDTO> {
    // 1. Valida os dados de entrada (Zod)
    const validated = CreateAppointmentSchema.parse(dto);

    // 2. Verifica conflitos de horário
    const slot = TimeSlot.create(validated.date, validated.startTime, validated.endTime);
    const conflicts = await this.appointmentRepo.findConflicts(validated.doctorId, slot);
    if (conflicts.length > 0) throw new Error('Time slot is not available');

    // 3. Cria a entidade (com regras de negócio do domínio)
    const appointment = Appointment.create(validated);

    // 4. Persiste no banco (via interface — não sabe que é Convex!)
    const id = await this.appointmentRepo.save(appointment);

    // 5. Retorna um DTO (dados simples, sem lógica)
    return { id, status: appointment.status.value, ... };
  }
}
```

---

## Camada 3: Infrastructure (Adapters)

A infraestrutura implementa os contratos do domínio. Aqui é onde o Convex aparece:

```ts
// src/modules/settings/infrastructure/convex/useSettingsRepository.ts
"use client";

import { useQuery, useMutation } from "convex/react";
import type { ISettingsRepository } from "@/modules/settings/domain/ports/ISettingsRepository";

export function useSettingsRepository() {
  // Hooks do Convex — infraestrutura
  const userProfile = useQuery(api.settings.getUserProfile);
  const updateProfileMutation = useMutation(api.settings.updateUserProfile);

  // Implementa a interface do domínio
  const repo: ISettingsRepository = {
    getUserProfile: async () => userProfile ?? null,
    updateUserProfile: async (input) => {
      await updateProfileMutation(input); // chama o Convex aqui
    },
  };

  // Retorna Use Cases prontos para uso na UI
  return {
    userProfile,
    executeProfileUpdate: async (input) =>
      new UpdateProfileUseCase(repo).execute(input), // injeta o repo no UseCase
  };
}
```

**O fluxo completo:**
```
UI (componente React)
  → chama useSettingsRepository()
    → Use Case recebe o repo (Convex)
      → Use Case chama repo.save()
        → repo faz ctx.db.insert() no Convex
```

---

## Value Objects — Tipos com Regras

Value Objects são tipos que possuem **regras de validação** embutidas:

```ts
// src/modules/appointments/domain/value-objects/AppointmentStatus.ts

export class AppointmentStatus {
  private constructor(readonly value: 'pending' | 'confirmed' | 'cancelled' | 'completed') {}

  static pending() { return new AppointmentStatus('pending'); }
  static confirmed() { return new AppointmentStatus('confirmed'); }
  static cancelled() { return new AppointmentStatus('cancelled'); }
  static completed() { return new AppointmentStatus('completed'); }
}
```

Por que não usar string direta? Com `AppointmentStatus`, você **nunca** cria um status inválido — o TypeScript não deixa.

---

## DTOs — Transferindo Dados Entre Camadas

DTO (Data Transfer Object) = objeto simples para mover dados sem lógica:

```ts
// src/modules/appointments/application/dtos/CreateAppointmentDTO.ts
import { z } from 'zod';

// Zod valida os dados de entrada
export const CreateAppointmentSchema = z.object({
  patientId: z.string().min(1),
  doctorId: z.string().min(1),
  date: z.string().regex(/^\d{4}-\d{2}-\d{2}$/),  // formato YYYY-MM-DD
  startTime: z.string(),
  endTime: z.string(),
  visitType: z.string().min(1),
  notes: z.string().optional(),
});

export type CreateAppointmentDTO = z.infer<typeof CreateAppointmentSchema>;
// ↑ extrai o tipo TypeScript do schema Zod automaticamente
```

---

## Regras das dependências

```
domain ← application ← infrastructure ← UI
```

- **Domain** não importa nada externo (zero dependências de framework)
- **Application** importa apenas o domain
- **Infrastructure** importa domain, application e Convex/React
- **UI** importa infrastructure (os hooks)

Se você ver um `import` apontando para a direção errada (ex: domain importando Convex), **isso é uma violação arquitetural**.

---

## Problemas comuns

### ❌ "Não sei onde colocar meu código novo"
Use esta lógica:
- É uma regra de negócio? → `domain/`
- Orquestra várias coisas? → `application/use-cases/`
- Conecta com Convex/API? → `infrastructure/`
- É componente visual? → `app/_components/` ou dentro do módulo

### ❌ "O Use Case está muito acoplado ao Convex"
**Causa:** o Use Case importa `useQuery` diretamente.  
**Solução:** o Use Case deve receber apenas a **interface** (`IAppointmentRepository`), não a implementação.

---

## Resumo visual

```
┌─────────────────────────────────────────────────────────┐
│  UI Layer (React Components)                            │
│  src/app/(private)/dashboard/appointments/page.tsx      │
└───────────────────────────┬─────────────────────────────┘
                            │ chama hook
┌───────────────────────────▼─────────────────────────────┐
│  Infrastructure Layer (Adapters)                        │
│  useAppointments.ts — usa Convex, implementa a interface│
└───────────────────────────┬─────────────────────────────┘
                            │ injeta repo no UseCase
┌───────────────────────────▼─────────────────────────────┐
│  Application Layer (Use Cases)                          │
│  CreateAppointmentUseCase.ts — orquestra tudo           │
└───────────────────────────┬─────────────────────────────┘
                            │ usa entidades e interfaces
┌───────────────────────────▼─────────────────────────────┐
│  Domain Layer (Business Rules)                          │
│  Appointment.ts, IAppointmentRepository.ts              │
│  Zero dependências externas!                            │
└─────────────────────────────────────────────────────────┘
```
