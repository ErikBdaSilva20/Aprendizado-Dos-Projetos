# Autenticação — Convex Auth

## Conceitos principais

- **Convex Auth** — biblioteca oficial da Convex para lidar com login/cadastro
- **Provider** — método de login (Email/Senha, Google, GitHub, Magic Link)
- **Session** — controle de quem está logado e por quanto tempo
- **`useAuthActions`** — hook para chamar funções de login/logout no cliente
- **`getAuthUserId`** — função para verificar quem está logado no servidor

---

## Por que usar Convex Auth?

Criar um sistema de autenticação do zero envolve:
1. Criar tabelas de usuários e senhas (criptografadas com bcrypt)
2. Gerenciar tokens JWT (Access Token, Refresh Token)
3. Criar rotas de login, cadastro, esqueci a senha, resetar senha
4. Lidar com cookies seguros e CORS
5. Implementar OAuth (Google, Apple, etc.)

O **Convex Auth** já faz **tudo isso** de forma nativa e integrada com o banco de dados do projeto. Você não precisa gerenciar JWTs nem criar rotas complexas.

---

## Como está configurado no Zyntra?

O arquivo central da autenticação é o `convex/auth.ts`:

```ts
// convex/auth.ts
import { convexAuth, getAuthUserId } from '@convex-dev/auth/server';
import { Password } from '@convex-dev/auth/providers/Password';
import { query } from './_generated/server';

export const { auth, signIn, signOut, store } = convexAuth({
  providers: [Password], // ← Usando login por email/senha
  callbacks: {
    // Isso roda automaticamente quando alguém cria uma conta
    async createOrUpdateUser(ctx, args) {
      if (args.existingUserId) {
        return args.existingUserId;
      }

      const email = typeof args.profile.email === 'string' ? args.profile.email : '';
      const name = email.split('@')[0] ?? 'User';

      // Cria o registro na tabela "users" padrão
      return await ctx.db.insert('users', {
        email,
        name,
        role: 'admin', // ← Primeiro usuário vira admin por padrão
        createdAt: Date.now(),
      });
    },
  },
});

// Helper para saber se está logado
export const isAuthenticated = query({
  args: {},
  handler: async (ctx) => {
    const userId = await getAuthUserId(ctx); // ← Pega o ID seguro do token
    return userId !== null;
  },
});
```

---

## Usando Autenticação no Frontend (Componentes "use client")

Para fazer login, cadastro e logout na interface, usamos o hook `useAuthActions`:

```tsx
"use client";
import { useAuthActions } from "@convex-dev/auth/react";
import { useState } from "react";

export function LoginForm() {
  const { signIn } = useAuthActions(); // ← Extrai a função signIn
  const [email, setEmail] = useState("");
  const [password, setPassword] = useState("");

  const handleLogin = async (e) => {
    e.preventDefault();
    try {
      // Chama o login do Convex Auth
      await signIn("password", { email, password, flow: "signIn" });
      // Redireciona ou mostra sucesso
    } catch (error) {
      console.error("Erro ao logar:", error);
    }
  };

  return (
    <form onSubmit={handleLogin}>
      <input type="email" value={email} onChange={e => setEmail(e.target.value)} />
      <input type="password" value={password} onChange={e => setPassword(e.target.value)} />
      <button type="submit">Entrar</button>
    </form>
  );
}
```

### Para deslogar

```tsx
"use client";
import { useAuthActions } from "@convex-dev/auth/react";

export function LogoutButton() {
  const { signOut } = useAuthActions();

  return (
    <button onClick={() => signOut()}>
      Sair da conta
    </button>
  );
}
```

---

## Protegendo as Páginas e Queries (Middleware / Backend)

Como garantir que só usuários logados possam ver os dados?

### 1. No Next.js (Protegendo Rotas)
No Zyntra, geralmente usamos componentes layouts ou useEffects para checar se o usuário está logado, mas o melhor lugar para checar é nas **próprias funções do Convex**.

### 2. No Convex (Protegendo Dados)
**Nunca** confie apenas no frontend. Sempre verifique no backend quem está pedindo os dados:

```ts
// convex/dashboard.ts
import { query } from "./_generated/server";
import { getAuthUserId } from "@convex-dev/auth/server";

export const getSecretStats = query({
  handler: async (ctx) => {
    // 1. Pega o ID do usuário que fez a requisição
    const userId = await getAuthUserId(ctx);

    // 2. Se não tem ID, bloqueia na hora
    if (!userId) {
      throw new Error("Não autorizado");
    }

    // 3. Opcional: Busca o usuário completo para checar "roles"
    const user = await ctx.db.get(userId);
    if (user?.role !== "admin") {
      throw new Error("Apenas administradores podem ver isso");
    }

    // 4. Se chegou aqui, tá liberado!
    return { revenue: 1000000 };
  },
});
```

---

## Entendendo os Tokens

O Convex Auth usa cookies HTTP-only (seguros, que JavaScript não consegue ler) para guardar o token da sessão. 
Quando você faz uma mutation ou query usando os hooks do React (`useQuery`), **o Convex injeta automaticamente o token na requisição**.

É por isso que as chamadas `await getAuthUserId(ctx)` magicamente sabem quem você é.

---

## Problemas comuns

### ❌ "Could not find public function for 'auth:isAuthenticated'"
**Causa:** O servidor local do Convex não está rodando.
**Solução:** Abra o terminal e rode `npx convex dev`.

### ❌ Você cria um usuário mas ele não aparece na tabela
**Causa:** O callback `createOrUpdateUser` não foi acionado ou deu erro.
**Solução:** Verifique os logs do Convex Dashobard.

### ❌ "Uncaught Error: Unauthenticated" ao chamar uma mutation
**Causa:** Você está chamando uma função protegida sem estar logado.
**Solução:** Faça o login primeiro, ou adicione tratamento de erro no frontend.

---

## Boas práticas

- **Sempre verifique `getAuthUserId(ctx)`** no topo das suas mutations e queries que manipulam dados sensíveis.
- **Não passe o userId do frontend via parâmetro** — isso é perigoso (alguém poderia passar o ID de outra pessoa). Sempre use `getAuthUserId(ctx)` no servidor para garantir quem é o autor da requisição.
- Misture Server Components no Next.js com `ConvexAuthNextjsServerProvider` para lidar com redirects automáticos sem flashes na tela.
