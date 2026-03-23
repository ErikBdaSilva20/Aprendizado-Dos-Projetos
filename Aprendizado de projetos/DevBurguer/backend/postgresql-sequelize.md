# 🐘 PostgreSQL & Sequelize (Banco Relacional)

O **PostgreSQL** é o banco de dados principal, onde guardamos informações estruturadas: Usuarios, Produtos e Categorias. O **Sequelize** é um **ORM** (Object-Relational Mapping), que nos permite falar com o banco usando JavaScript em vez de SQL puro.

## 🧠 Conceitos Principais

- **Banco de Dados Relacional**: Pense em tabelas (estilo Excel) que se conectam por chaves (IDs).
- **Model**: Uma classe que define a estrutura de uma tabela (quais colunas ela tem, como `name`, `price`, `email`).
- **Migrations**: O "controle de versão" das tabelas. Elas criam, alteram ou excluem colunas de forma segura.
- **Relacionamentos**: Como um Produto pertence a uma Categoria (`belongsTo`) e uma Categoria tem muitos Produtos (`hasMany`).
- **Validations**: Regras para o que pode ou não entrar no banco (ex: e-mail obrigatório).

---

## 💡 Explicação Simplificada

O **PostgreSQL** é como o **arquivo de aço** do escritório. O **Sequelize** é a **secretária bilíngue** que:
1.  Traduz o seu JavaScript para a língua do banco (**SQL**).
2.  Garante que você não coloque o nome do produto no lugar do preço (**Tipagem**).
3.  Organiza o histórico de como as gavetas (tabelas) foram criadas (**Migrations**).

---

## ❓ Por que usar o Sequelize?

- **Abstração**: Você não precisa escrever SQL complexo (ex: `SELECT * FROM products` vira `Product.findAll()`).
- **Segurança**: Ele protege automaticamente contra ataques de "SQL Injection".
- **Facilidade de Manutenção**: Se amanhã quisermos trocar de PostgreSQL para MySQL, o Sequelize cuida de quase tudo!

---

## 🛠️ Exemplos de Uso no Projeto

### Definição de Model (`src/app/models/Product.js`)

Aqui definimos o que um Produto tem:
```js
class Product extends Model {
  static init(sequelize) {
    super.init(
      {
        name: Sequelize.STRING,
        price: Sequelize.INTEGER,
        path: Sequelize.STRING,
        offer: Sequelize.BOOLEAN,
        url: {
          type: Sequelize.VIRTUAL, // Não vai pro banco, é gerado pelo código
          get() {
            return `http://localhost:3001/product-file/${this.path}`;
          },
        },
      },
      { sequelize }
    );
    return this;
  }
}
```

### Usando o Sequelize no Controller (`src/app/controllers/productController.js`)

Criando um novo produto de forma simples:
```js
const product = await Product.create({
  name,
  price,
  category_id,
  path: filename,
  offer,
});
```

---

## 📁 Estrutura de Migrations

Elas ficam em `src/database/migrations`. Cada arquivo tem um `up` (o que vai ser feito) e um `down` (como desfazer).
Exemplo: `20250101-create-users.js`.

---

## ✅ Desafios para Estudantes

1.  **Olhe o model `User.js`**: Veja como o campo `admin` é definido. Ele é `true` ou `false` por padrão?
2.  **Rode as migrations**: Entenda o comando `npx sequelize-cli db:migrate`. O que ele faz exatamente no seu banco de dados local?

---

## 📚 Links Úteis
- [Documentação do Sequelize](https://sequelize.org/)
- [O que é um Banco Relacional?](https://www.hostgator.com.br/blog/o-que-e-banco-de-dados-relacional/)
- [Diferença entre SQL e NoSQL](https://www.alura.com.br/artigos/sql-vs-nosql-quais-as-principais-diferencas)
