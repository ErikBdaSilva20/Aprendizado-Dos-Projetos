# 🚀 Node.js e Express (Backend)

O **Node.js** é o ambiente de execução que nos permite usar JavaScript no servidor. Já o **Express** é um framework minimalista que fornece ferramentas essenciais para construir APIs (como rotas, tratamento de erros e middlewares).

## 🧠 Conceitos Principais

- **Servidor HTTP**: Um programa que escuta requisições (pedidos) vindas do navegador e envia respostas.
- **Rotas**: Caminhos específicos (como `/products` ou `/users`) que executam lógicas diferentes.
- **Middleware**: Uma espécie de "filtro" que intercepta a requisição antes de chegar na rota final (ex: verificação de login).
- **Controllers**: Arquivos de código que contêm toda a lógica da regra de negócio (o que fazer quando alguém chama a rota).
- **ES Modules**: O uso de `import` e `export` para organizar os arquivos de forma modular.

---

## 💡 Explicação Simplificada

Imagine que o servidor é um restaurante (o DevBurguer!). O **Express** é o **gerente**, que recebe os clientes, entende o que eles querem e direciona para a pessoa certa:
1.  **Rotas** são os caminhos do balcão (ex: "Fila de Pedidos", "Fila de Pagamentos").
2.  **Middleware** é o segurança na porta que verifica se você tem o "ticket" (autenticação) para entrar.
3.  **Controllers** são os cozinheiros, que pegam os ingredientes (dados do banco) e preparam o lanche (a resposta final).
4.  **REsponse (JSON)** é o lanche entregue ao cliente.

---

## ❓ Por que usamos o Express?

- **Velocidade**: Fácil de configurar e muito performático.
- **Ecossistema**: Milhares de plugins (como o `cors` para segurança e o `multer` para imagens).
- **Padronização**: Torna o backend previsível e fácil de manter.

---

## 🛠️ Exemplos de Uso no Projeto

### Ponto de Entrada (`src/server.js`)

Aqui é onde o servidor "nasce" e começa a escutar:
```js
// server.js
import app from './app.js';

const PORT = process.env.PORT || 3001;
app.listen(PORT, () => console.log(`🚀 Servidor rodando na porta ${PORT}`));
```

### Configuração do App (`src/app.js`)

Onde configuramos os middlewares globais e as rotas:
```js
import express from 'express';
import routes from './routes.js';

class App {
  constructor() {
    this.app = express();
    this.middlewares();
    this.routes();
  }
  // ... ativa o JSON e o CORS
}
```

### Exemplo de Controller (`src/app/controllers/productController.js`)

Onde a lógica acontece de verdade:
```js
class ProductController {
  async index(request, response) {
    const products = await Product.findAll();
    return response.json(products); // Envia os produtos em formato JSON
  }
}
```

---

## ✅ Desafios para Estudantes

1.  **Onde as rotas estão registradas?** Encontre o arquivo `src/routes.js` e veja como os verbos `GET`, `POST`, `PUT` e `DELETE` são usados.
2.  **O que o `--watch` faz?** Olhe o arquivo `package.json` no script `dev`. Por que isso é útil durante o desenvolvimento? (Dica: ele reinicia o servidor sozinho quando você salva o código!)

---

## 📚 Links Úteis
- [Documentação Oficial do Express](https://expressjs.com/pt-br/)
- [Guia de Node.js para Iniciantes](https://developer.mozilla.org/pt-BR/docs/Learn/Server-side/Node_server_without_framework)
