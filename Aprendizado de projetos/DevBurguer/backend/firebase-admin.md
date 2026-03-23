# 🔥 Firebase Admin SDK (Backend Security)

O **Firebase Admin SDK** no backend serve para validar se o usuário que está tentando fazer um pedido ou ver o dashboard admin é realmente quem diz ser. Ele lê o **Token (JWT)** enviado pelo FrontEnd e pergunta ao Google: "Este token é verdadeiro?".

## 🧠 Conceitos Principais

- **Token (JWT)**: Uma "pulseira VIP" que o usuário ganha ao logar. Ela diz quem é a pessoa e por quanto tempo vale.
- **Middleware de Autenticação**: Um código que roda *antes* da rota. Ele bloqueia quem não tem pulseira (token).
- **Service Account**: Um arquivo secreto (`serviceAccountKey.json`) que permite ao servidor acessar o Firebase com plenos poderes.
- **Diferença de SDKs**: No Front usamos o SDK de cliente (para logar); no Back usamos o Admin SDK (para fiscalizar).

---

## 💡 Explicação Simplificada

O **Firebase Admin** é o **Segurança da Casa**.
1.  O cliente chega com um **crachá** (Token) que pegou na recepção (FrontEnd via Firebase Auth).
2.  O segurança (Backend) olha o crachá e liga para o **Google** (Admin SDK) para confirmar se é original.
3.  Se o Google disser "OK", o backend deixa o cliente passar para a cozinha (Fazer pedido).
4.  Além disso, o backend olha no próprio banco (PostgreSQL) se aquele CPF é de um **Administrador** da lanchonete.

---

## ❓ Por que usamos o Firebase Admin?

- **Segurança de Elite**: O Google cuida de toda a criptografia dos tokens por nós.
- **Menos Trabalho**: Não precisamos criar um sistema de "Esqueci minha senha", "Login Social", etc., do zero!
- **Escalabilidade**: Agenta milhões de usuários sem sobrecarregar o nosso servidor Node.js.

---

## 🛠️ Exemplos de Uso no Projeto

### Inicialização (`src/config/firebase-admin.js`)

Aqui é onde o backend se apresenta ao Google:
```js
import admin from 'firebase-admin';
import serviceAccount from '../../serviceAccountKey.json' assert { type: 'json' };

admin.initializeApp({
  credential: admin.credential.cert(serviceAccount),
});

export default admin;
```

### O Middleware de Auth (`src/app/middlewares/auth.js`)

Onde a mágica da segurança acontece:
```js
async function authMiddleware(request, response, next) {
  const authToken = request.headers.authorization;
  if (!authToken) return response.status(401).json({ error: 'Token not provided' });

  const [, token] = authToken.split(' '); // Pega o token após "Bearer"

  try {
    const decodedToken = await admin.auth().verifyIdToken(token); // Pergunta ao Google
    request.userId = decodedToken.uid; // Guarda o ID do usuário na requisição
    request.userEmail = decodedToken.email;

    return next(); // "Passa pro próximo!"
  } catch (err) {
    return response.status(401).json({ error: 'Invalid Token' }); // "Cai fora!"
  }
}
```

---

## ✅ Desafios para Estudantes

1.  **Onde o arquivo `serviceAccountKey.json` deve ficar?** Por que NUNCA devemos subir esse arquivo no GitHub?
2.  **O que é o `adminMiddleware`?** Olhe o arquivo `src/app/middlewares/admin.js`. Como ele sabe se o usuário pode apagar um produto ou não? (Dica: ele usa o `request.userId` para buscar o flag `admin` no PostgreSQL).

---

## 📚 Links Úteis
- [Documentação do Firebase Admin SDK](https://firebase.google.com/docs/admin/setup?hl=pt-br)
- [O que é um Token JWT?](https://blog.geekhunter.com.br/o-que-e-jwt/)
- [Práticas Recomendadas de Segurança no Node.js](https://expressjs.com/pt-br/advanced/best-practice-security.html)
