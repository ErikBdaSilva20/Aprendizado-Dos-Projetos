# Formulários — Zod e React Hook Form

## Conceitos principais

- **React Hook Form (`useForm`)** — biblioteca para lidar com formulários sem re-renders excessivos.
- **Zod (`z.object()`)** — biblioteca para criar schemas de validação (regras para os dados).
- **Resolver (`zodResolver`)** — conecta o React Hook Form com o Zod, para que as regras do Zod validem os campos do form automaticamente.
- **Controlled vs Uncontrolled** — formulários no React podem ser baseados em estado (`useState`) ou em refs (`useForm` prefere refs para performance).
- **`Controller` / `FormField`** — componentes para lidar com inputs de UI complexos (como selects, date pickers do Shadcn/UI).

---

## Por que usar Zod + React Hook Form?

Criar formulários usando `useState` é terrível:
1. Você precisa de um estado diferente para cada campo (`nome`, `email`, `senha`, etc).
2. O componente vai "re-renderizar" a cada caractere digitado.
3. Tratamento de mensagens de erro ("Este campo é vazio", "Email inválido") exige código repetitivo e espalhado.

O **Zod** resolve a regra de validação ("O que é válido?").
O **React Hook Form** resolve a performance ("Como coletar sem travar a tela?").

Juntos, eles são o padrão ouro de formulários no React e a base do Zyntra e Shadcn/UI.

---

## Passo 1: O Schema do Zod (O "Contrato" de Validação)

A primeira coisa ao criar um formulário é definir suas regras. O Zod é incrível porque as regras geram automaticamente o tipo TypeScript.

```ts
// src/modules/appointments/application/dtos/CreateAppointmentDTO.ts
import { z } from 'zod';

export const CreateAppointmentSchema = z.object({
  patientId: z.string().nonempty({ message: "Selecione um paciente" }),
  doctorId: z.string({ required_error: "Selecione um médico" }),
  
  // Formatamos regex no frontend (YYYY-MM-DD):
  date: z.string().regex(/^\d{4}-\d{2}-\d{2}$/, "Data inválida (YYYY-MM-DD)"),
  
  // Limites customizados:
  startTime: z.string(),
  endTime: z.string(),
  
  // Union type (valores exatos permitidos):
  visitType: z.enum(['Checkup', 'First Time', 'Follow Up', 'Emergency']),
  
  // Opcionais (não precisam ser preenchidos):
  notes: z.string().optional(),
});

// A magia! Você não precisa re-digitar a interface:
export type CreateAppointmentDTO = z.infer<typeof CreateAppointmentSchema>;
```

---

## Passo 2: Configurando o `useForm` com o Resolver

Quando for criar o componente de UI, conectamos o Zod usando o **zodResolver**.

```tsx
"use client";
import { useForm } from "react-hook-form";
import { zodResolver } from "@hookform/resolvers/zod";
import { CreateAppointmentSchema, CreateAppointmentDTO } from "@/modules/appointments/application/dtos/CreateAppointmentDTO";

export function AppointmentForm() {
  const form = useForm<CreateAppointmentDTO>({
    resolver: zodResolver(CreateAppointmentSchema), // ← Aqui a mágica acontece!
    defaultValues: { // ← Valores iniciais vazios
      patientId: "",
      doctorId: "",
      date: "",
      startTime: "",
      endTime: "",
      visitType: "Checkup",
      notes: "",
    },
  });

  // Função chamada se o formulário estiver VÁLIDO
  const onSubmit = async (data: CreateAppointmentDTO) => {
    console.log("Valores válidos e prontos para enviar:", data);
    // Aqui chama o UseCase ou Convex Mutation
  };

  return (
    // o form.handleSubmit valida tudo com Zod, e se passar, roda onSubmit()
    <form onSubmit={form.handleSubmit(onSubmit)}>
      ... inputs aqui ...
    </form>
  );
}
```

---

## Passo 3: Usando Componentes Shadcn/UI (FormFields)

No Zyntra, geralmente usamos componentes visuais como os do Shadcn/UI (`Form`, `FormField`, `FormItem`, etc.). Eles facilitam mostrar as mensagens de erro abaixo de cada campo do Zod.

```tsx
import { Form, FormField, FormItem, FormLabel, FormControl, FormMessage } from "@/app/_components/ui/form";
import { Input } from "@/app/_components/ui/input";

// Dentro do seu componente, enrolamos no <Form>
return (
  <Form {...form}>
    <form onSubmit={form.handleSubmit(onSubmit)} className="space-y-4">
      
      {/* Campo para o Paciente */}
      <FormField
        control={form.control}        // ← Liga com o React Hook Form
        name="patientId"              // ← O nome exato que está no Zod
        render={({ field }) => (       // ← `field` traz valor e funções onBlur, onChange
          <FormItem>
            <FormLabel>ID do Paciente</FormLabel>
            <FormControl>
              <Input placeholder="Digite o ID..." {...field} />
            </FormControl>
            <FormMessage />           {/* Mostra mensagem do Zod de erro se houver */}
          </FormItem>
        )}
      />
      
      {/* Botão para enviar */}
      <button type="submit" disabled={form.formState.isSubmitting}>
        Salvar
      </button>

    </form>
  </Form>
);
```

---

## Por que usar `FormField` em vez de inputs `onChange` nativos?

Porque o React Hook Form trabalha de forma não-controlada (uncontrolled) por padrão para inputs nativos (passando refs para eles sem re-render). 

Porém, componentes "customizados" da interface, como Seletores Avançados (SelectUI, DatePicker, Switches), exigem que o React controle o estado de forma reativa. O componente `<FormField>` com o `render={({ field })}` encapsula esse poder complexo (é um Wrapper pro `<Controller />`) para conectar o input visual com o estado lógico interno do formulário.

## Como o Zod também defende o BackEnd do Zyntra

Os schemas (ex: `CreateAppointmentSchema`) não servem só para a UI.

Lembre-se da separação de camadas do Hexagonal no seu sistema:

```ts
// src/modules/appointments/application/use-cases/CreateAppointmentUseCase.ts
export class CreateAppointmentUseCase {
  async execute(dto: CreateAppointmentDTO): Promise<AppointmentDTO> {
    
    // O Use Case no servidor/Backend TAMBÉM verifica se os dados são válidos!
    const validated = CreateAppointmentSchema.parse(dto);

    // Agora é seguro prosseguir criando.
    const appointment = Appointment.create(validated);
```

Isso impede que alguém faça um ataque manipulando requisições, pois o Zod bloqueia parâmetros soltos ou quebrados na entrada do Use Case.

---

## Problemas comuns

### ❌ O Form diz que é inválido mas não mostra a mensagem de erro
**Causa:** Você esqueceu de renderizar o `<FormMessage />` dentro do `<FormItem>` ou esqueceu de empacotar num `<FormField>`.
**Solução:** Sempre que quiser exibir erros do Zod na tela, ele deve ser lido de `form.formState.errors` ou mais recomendavelmente pelos wrappers do Shadcn/UI.

### ❌ Select ou DatePicker não atualiza o valor
**Causa:** Componentes complexos não funcionam usando a prop `ref` nativa de HTML que o rhf empurra com `...register("name")`.
**Solução:** Sempre use a sintaxe de `<FormField>` e injete a sub-prop `onValueChange={field.onChange}` caso o Wrapper do input precise.

### ❌ Re-renderizações infinitas na digitação
**Causa:** Você está chamando hooks ou mutando states paralelamente na hora do `onChange` dentro do formulário e misturou as lógicas.
**Solução:** Use apenas o `onChange` que vem no `{...field}` do React Hook Form.

---

## Boas Práticas

- Sempre escreva seus schemas Zod em um arquivo separado na pasta `dtos/` e importe ele tanto no backend quanto no Form. Padrão DRY (Don't Repeat Yourself).
- Use `isSubmitting` do `form.formState` para criar botões visuais que exibem barras de carregamento (spinners) sozinho com um clique.
- Se a mensagem de erro da biblioteca `zod` for feia pra ler, ensine pra ela strings estáticas com `z.string().min(5, { message: "Senha precisa de 5 letras." })`.
