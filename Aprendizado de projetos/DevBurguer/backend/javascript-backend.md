# 💻 JavaScript Moderno no Backend (Node.js)

O Node.js permite que nosso servidor DevBurguer use as funcionalidades mais novas da linguagem. Aqui estão as que mais aparecem no código da pasta `BackEnd/src`.

## 🧠 Conceitos Principais

- **ES Modules (`import`/`export`)**: No projeto, usamos `import` em vez de `require` (definido no `package.json` como `"type": "module"`).
- **Async / Await**: ESSENCIAL para falar com o banco de dados. Sem isso, o código "atropelaria" a resposta do banco antes dela chegar.
- **Destructuring**: Pegar campos de um objeto de forma rápida (`const { name, price } = request.body`).
- **Arrow Functions**: Uma forma mais curta e elegante de escrever funções.
- **Spread Operator (`...`)**: Usado para "espalhar" dados de um objeto em outro (ex: atualizar um produto).

---

## 💡 Exemplos Reais no Projeto

### Esperando o Banco de Dados (Async/Await)
```js
// Ao buscar usuários:
const users = await User.findAll(); // O código para aqui até o banco responder!
```

### Pegando Dados da Requisição (Destructuring)
```js
// No controller:
const { name, email, password, admin } = request.body;
```

---

## ✅ Desafios para Estudantes

1.  **O que acontece se eu esquecer o `await`?** Tente tirar o `await` de um `User.create` no seu código. O que o JavaScript vai te devolver? (Dica: ele vai te devolver uma `Promise` em vez dos dados do usuário!).
2.  **Por que usamos `export default`?** Veja como o `app.js` é exportado e importado no `server.js`.

---

## 📚 Links Úteis
- [Guia Modern JavaScript (ES6+)](https://www.freecodecamp.org/news/modern-javascript-es6-features-you-should-know/)
- [Entendendo Async/Await no Node.js](https://blog.geekhunter.com.br/async-await-javascript/)
- [O que é o Event Loop do Node.js?](https://www.alura.com.br/artigos/node-js-o-que-e-o-event-loop)
