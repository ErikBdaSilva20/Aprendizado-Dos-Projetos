# 🍃 MongoDB & Mongoose (Banco Não-Relacional)

O **MongoDB** é o banco paralelo de nossa aplicação, usado especificamente para os **Pedidos** (`Orders`). O **Mongoose** é a biblioteca que facilita a interação rápida com o MongoDB, definindo "Schemas" (modelos) de dados.

## 🧠 Conceitos Principais

- **Banco de Dados Orientado a Documentos**: Pense em arquivos JSON (em vez de tabelas Excel) que podem crescer ou mudar conforme necessário.
- **Collection**: O que seriam as "tabelas" do SQL, no MongoDB são as coleções (ex: `orders`).
- **Document**: Cada registro unitário (ex: um pedido específico) é um documento único.
- **Schema**: A definição de quais campos o MongoDB espera que cada documento tenha (apesar de ser flexível!).
- **Stateless**: Se o Firestore (ou MongoDB) sofrer uma alteração, o código reflete na hora.

---

## 💡 Explicação Simplificada

O **PostgreSQL** é como um **Formulário de Candidatura**: Tudo deve estar no lugar certo e seguir um padrão fixo. O **MongoDB** é como uma **Pasta de Arquivos**: Você joga o documento inteiro dentro dela de uma vez!
1.  **Orders** no DevBurguer: Tem o usuário, os dados de cada produto do lanche, o ID do pagamento... Muita informação variada.
2.  É mais fácil guardar o "instantâneo" do pedido inteiro dentro de um arquivo único (no MongoDB) do que espalhar em 5 tabelas diferentes.

---

## ❓ Por que usamos o MongoDB?

- **Flexibilidade**: Se hoje um pedido tem 2 produtos e amanhã 10, o MongoDB não liga!
- **Velocidade de Leitura**: É muito rápido ler um documento JSON pronto do que fazer `JOINs` em várias tabelas SQL.
- **Armazenamento de Histórico**: Perfeito para registros que não mudam mais depois de criados (como recibos).

---

## 🛠️ Exemplos de Uso no Projeto

### Criando o Schema de Pedido (`src/app/schemas/Orders.js`)

Aqui definimos os campos de forma aninhada:
```js
const OrderSchema = new mongoose.Schema(
  {
    user: {
      id: { type: String, required: true },
      name: { type: String, required: true },
    },
    products: [
      {
        id: { type: Number, required: true },
        name: { type: String, required: true },
        price: { type: Number, required: true },
        category: { type: String, required: true },
        url: { type: String, required: true },
        quantity: { type: Number, required: true },
      },
    ],
    status: { type: String, required: true },
  },
  { timestamps: true } // Grava createdAt e updatedAt sozinho
);
```

### Criando um Pedido no Controller (`src/app/controllers/orderController.js`)

Salvando o objeto no MongoDB Atlas (nuvem):
```js
const order = {
  user: { id: request.userId, name: request.userName },
  products: productsDetailed,
  status: 'Pendente',
};
const orderResponse = await Order.create(order);
```

---

## ✅ Desafios para Estudantes

1.  **Por que não usamos PostgreSQL para os Pedidos também?** Pense no que aconteceria se você excluísse um produto do cardápio: o histórico dos pedidos antigos sumiria ou ficaria quebrado no SQL! No NoSQL, o dado fica guardado exatamente como era na hora da compra.
2.  **Onde o MongoDB está configurado no projeto?** Encontre o arquivo `src/database/index.js` e veja a função `mongo()`. O que ela usa para se conectar? (Dica: `.env`)

---

## 📚 Links Úteis
- [Documentação Oficial do MongoDB](https://www.mongodb.com/pt-br/docs/)
- [Guia do Mongoose para Node.js](https://mongoosejs.com/docs/guide.html)
- [O que é MongoDB? Noções Básicas](https://www.mongodb.com/pt-br/what-is-mongodb)
