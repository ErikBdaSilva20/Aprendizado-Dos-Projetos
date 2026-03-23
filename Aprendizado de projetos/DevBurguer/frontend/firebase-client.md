# 🔥 Firebase SDK (Frontend Auth)

O **Firebase Client SDK** é o nosso "Recepção do Google". É ele quem cuida do **Login**, do **Cadastro** e do **Esqueci minha Senha** do cliente no navegador. Diferente do `firebase-admin` (que fica no backend para segurança), este SDK fica no frontend para falar diretamente com o usuário e pegar o **Token (JWT)**.

## 🧠 Conceitos Principais

- **Authentication**: O sistema que gerencia usuários.
- **Login Google (OAuth)**: Como logar clicando apenas num botão azul do Google, sem senha.
- **Email/Password Auth**: Logar do jeito antigo (e-mail e senha).
- **ID Token (JWT)**: A "Pulseira VIP" que ganhamos ao logar e que o Axios leva pro backend.
- **persistence (local)**: Como o Firebase lembra que você já logou, mesmo se fechar a aba do navegador.

---

## 💡 Explicação Simplificada

O **Firebase Client** é o **Recepcionista da Lanchonete**.
1.  Você chega e dá seu e-mail e senha.
2.  O **Recepcionista (SDK)** liga para o Google Central para conferir.
3.  O Google devolve uma **Pulseira VIP (Token)**.
4.  O Recepcionista te dá a pulseira e diz: "Seja bem-vindo!".
5.  Agora você guarda a pulseira no bolso (localStorage) e mostra pro Motoboy (Axios) toda vez que quiser um lanche.

---

## ❓ Por que usamos o Firebase?

- **Zero Banco de Senhas**: Nós não guardamos senhas de ninguém no nosso banco de dados. Isso tira uma responsabilidade enorme das nossas costas (segurança!).
- **Redes Sociais**: É muito rápido colocar botões de Facebook, Google ou Apple.
- **Métricas de Usuários**: Você pode ver quantos usuários novos entraram por dia direto no Painel do Firebase Console.

---

## 🛠️ Examples de Uso no Projeto

### Inicialização (`src/config/firebase.js`)

Conectando-se ao seu projeto no Google:
```js
import { initializeApp } from "firebase/app";
import { getAuth } from "firebase/auth";

const firebaseConfig = {
  apiKey: "sua-chave-aqui",
  authDomain: "seu-projeto.firebaseapp.com",
  projectId: "seu-projeto-id",
  // ... outras configs do .env
};

const app = initializeApp(firebaseConfig);
export const auth = getAuth(app);
```

### Fazendo Login no Componente (`src/pages/Login/index.jsx`)

Logando o usuário:
```jsx
import { signInWithEmailAndPassword } from 'firebase/auth';
import { auth } from '../../config/firebase';

const onSubmit = async (data) => {
  const result = await signInWithEmailAndPassword(auth, data.email, data.password);
  const token = await result.user.getIdToken(); // Pegamos a Pulseira VIP!
  
  // Agora mandamos o token pro nosso Backend conferir as regras (isAdmin, etc).
};
```

---

## ✅ Desafios para Estudantes

1.  **Onde eu crio esse projeto do Firebase?** (Dica: procure no site do Firebase Console). Como eu pego essas chaves para colocar no meu `.env`?
2.  **Login com Google**: Tente encontrar no código onde o login por Google acontece. É diferente do login por e-mail? (Procure por `GoogleAuthProvider`).

---

## 📚 Links Úteis
- [Documentação Oficial do Firebase Auth (Web)](https://firebase.google.com/docs/auth/web/start?hl=pt-br)
- [O que é um fluxo OAuth2?](https://auth0.com/intro-to-iam/what-is-oauth-2/)
- [Guia de Configuração do Firebase no React](https://blog.logrocket.com/user-authentication-firebase-react-apps/)
