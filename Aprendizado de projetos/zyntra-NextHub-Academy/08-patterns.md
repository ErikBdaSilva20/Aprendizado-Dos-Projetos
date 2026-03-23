# Patterns — Use Cases, Repositories e DTOs

## Conceitos Principais do BackEnd

O projeto Zyntra usa de "Design Patterns" (Padrões de Projetos) táticos vindo do Domain-Driven Design (DDD). Isso dita como as classes e camadas são programadas no nível de linha por linha.

- **DTO (Data Transfer Object)** — Objetos vazios que carregam dados.
- **Repository Pattern** — Camufla a conexão com banco de dados usando interfaces.
- **Use Case (Ou Interactor)** — É o executor. A "história de usuário" tornada código.

---

## O Problema que isso resolve?

Se a interface, a regra de negócio e a query do Convex estivessem todas numa função `const salvar()` dentro da sua UI, seria o temido _"Spaghetti Code"_ ou "God Function" (função de deus).

Você precisaria carregar tudo do banco para mudar o botão de um Modal. Testar essa aplicação? Um pesadelo. Os padrões organizam tudo em camadas puras que cooperam. O fluxo da informação tem regras de ida e de vinda.

---

## Pattern 1: DTO (Data Transfer Object)

Um DTO é uma caixa inerte. DTOs nunca têm métodos, nunca processam, eles apenas agem de pombo correio movendo strings e numbers pra cima ou pra baixo.

Existem DTOs para ENTRADA e SAÍDA.

No Zyntra, eles geralmente andam acompanhados pela biblioteca de validação Zod.

```ts
// src/modules/appointments/application/dtos/AppointmentDTO.ts
// Esse é um DTO de saída do Use Case para o Front end ler simples propriedades.
export type AppointmentDTO = {
  id: string;
  patientId: string;
  doctorId: string;
  date: string;
  startTime: string;
  endTime: string;
  visitType: string;
  status: string;
  notes?: string;
  createdAt: string; // convertido pra string de propósito
};
```

**Benefício tático:** A Interface Web não conhece mais classes malucas do servidor. Ela só recebe os dados no formato que consegue ler direto (como textos para renderização rápida).

---

## Pattern 2: O Padrão 'Repository'

Um repositório é como um "buraco negro amigável". É uma classe onde a aplicação guarda Entidades para buscar depois, sem precisar saber ONDE (Convex, SQL, Postgres, arquivo txt, não importa).

No DDD Hexagonal, usamos as "Portas/Ports" para definir a forma do buraco negro primeiramente.

```ts
// src/modules/appointments/domain/ports/IAppointmentRepository.ts
import { Appointment } from '../entities/Appointment'; // ↑ Entidade bruta.

// A Porta: "Alguém que quer salvar algo neste app, DEVE CUMPRIR esses cinco contratos."
export interface IAppointmentRepository {
  findById(id: AppointmentId): Promise<Appointment | null>;
  findAll(filters?: any): Promise<any>;
  findConflicts(doctorId: string, slot: TimeSlot): Promise<Appointment[]>;
  save(appointment: Appointment): Promise<string>;
  update(appointment: Appointment): Promise<void>;
}
```

E no fim, quem constrói mesmo esse buraco negro se chama "Adapter", na Infrastructure:

```ts
// src/modules/settings/infrastructure/convex/useSettingsRepository.ts
export function useSettingsRepository() {
  const updateProfileMutation = useMutation(api.settings.updateUserProfile);
  
  // Isso é um Adapter local de UseQuery + Mutations virando um Repository local clássico.
  const repo: ISettingsRepository = {
    updateUserProfile: async (input: UpdateProfileInput) => {
      // Magia invisível do Convex entra apenas AQUI
      await updateProfileMutation(input);
    },
  }
}
```

**Benefício Tático:** O uso dos hooks injeta as chamadas serverless na mesma camada lógica do react e devolve como um `IRepository`, tornando simples construir o próximo Pattern.

---

## Pattern 3: Use Case (Application)

Use Cases ditam o que "pode ser feito" no seu sistema de fato. Por exemplo: "O Administrador quer cancelar uma consulta e exige motivar sua causa na UI".

- UseCase só processa lógica com Entities (camada 1)
- UseCase usa os DTOs do Zod (camada 2)
- UseCase guarda salvamentos no Repository (camada 3)

### Anatomia Clássica de um UseCase:

```ts
// src/modules/appointments/application/use-cases/CancelAppointmentUseCase.ts
import { CancelAppointmentDTO, CancelAppointmentSchema } from '../dtos/CancelAppointmentDTO';

export class CancelAppointmentUseCase {
  
  // 1. Injetação do buraco negro por onde persistimos. (Dependency Injection)
  constructor(
    private readonly repo: IAppointmentRepository,
  ) {}

  // 2. Método Principal DTO In -> Processamento -> Void
  async execute(id: string, dto: CancelAppointmentDTO): Promise<void> {

    // 3. Validação do pedido inicial por um portão de segurança:
    const validated = CancelAppointmentSchema.parse(dto);
    const appointmentId = new AppointmentId(id);

    // 4. Pedimos ao repositório pela Entidade Bruta.
    const appointment = await this.repo.findById(appointmentId);
    if (!appointment) throw new Error('Appointment not found');

    // 5. Chamamos as regras do negócio do Domínio puras 
    //    (Dentro da entidade rola checkagem de proibições).
    appointment.cancel(validated.reason);

    // 6. Finalmente, persistimos o resultado.
    await this.repo.update(appointment);
  }
}
```

Sempre que a sua aplicação receber um novo Módulo, como "Módulo De Faturamento (Billing)", a pasta criará os mesmos padrões `IBillingRepository`, `CreateInvoiceUseCase` e os formados pelo `InvoiceDTO`. Tudo previsível!

---

## Como as três camadas engrenam juntas na tela React:

Se eu quero cancelar uma Consulta do Médico por um Front-End Next.js agora, fazemos a Dança:

```tsx
function BotaoDeCancelar() {
  // Chamamos o Repository da Infrastructure (Que é feito por um react Hook Custom)
  const repoHook = useAppointmentsRepositoryConvex(); // ← O Adapter
  
  const click = () => {
    // Eu injeto a instância de meu Adapter como argumento do meu UseCase de cancelamento.
    const useCase = new CancelAppointmentUseCase(repoHook.repository); 
    
    // Entrego o ID (A url atual por exemplo) e os dados brutos pro executor rodar as lógicas limpas.
    useCase.execute("id_fake_123", { reason: "Médico pegou trânsito" }); 
  }
  return <button onClick={click}>Deletar Turno</button>
}
```

O Zyntra utiliza `classes` nos UseCases propositalmente para encorajar a **Inversão de Dependências (D.I)**.

## Resumo em Frases:
> 1. Formata a requisição (Zod DTO).
> 2. Protege as intenções de estado impuro no UseCase.
> 3. Altera a infraestrutura com as Queries e Mutations invisíveis pro UseCase, em formato de `IRepository`.
