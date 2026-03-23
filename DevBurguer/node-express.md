# 🌐 Servidor com Node.js e Express

O **Node.js** é o motor que roda JavaScript no computador (não no navegador), enquanto o **Express** é a "ferramenta" que nos ajuda a construir as rotas do nosso servidor.

## 🛠️ Como o Servidor Funciona?

O arquivo central é o `server/index.js`. Nele, tudo o que o backend faz está configurado:

1.  **Conexão com o Banco**: O Node.js usa o `mongoose` para se conectar ao MongoDB Atlas.
2.  **Configuração de Middleware**: Funções que processam o pedido (req) antes dele chegar na rota final:
    -   `express.json()`: Converte o corpo (body) de um pedido para JSON.
    -   `cors()`: Permite que o frontend (que roda em um endereço diferente) consiga fazer pedidos ao backend.
3.  **Definição de Rotas**: Cada caminho (`/`) no servidor é uma "estrada" para uma funcionalidade.

---

## 🛣️ Entendendo as Rotas (URLs)

As rotas são as portas de entrada para o seu servidor. Cada uma tem o seu papel:

### 📥 1. Buscar Produtos (`GET /api/products`)
Quando o frontend precisa mostrar a lista de presentes:

```js
app.get('/api/products', async (req, res) => {
  const products = await Product.find();
  res.json(products); // Devolve a lista para o React
});
```

### 📤 2. Criar Produto (`POST /api/products`)
A parte mais complexa! Aqui o servidor recebe:
-   O nome, link e descrição do presente.
-   Uma **imagem** enviada via formulário.

O `multer` e o `CloudinaryStorage` entram em ação aqui para salvar a imagem no Cloud e o link no banco.

### 🗑️ 3. Deletar Produto (`DELETE /api/products/:id`)
Quando o administrador quer remover um presente. O servidor:
-   Deleta o registro do banco de dados.
-   **OPCIONAL**: Deleta a imagem da Cloudinary para não ocupar espaço à toa.

---

## 🔐 Variáveis de Ambiente (`.env`)

Você nunca deve colocar suas senhas e chaves de acesso diretamente no código (`index.js`). No seu projeto, usamos o arquivo `.env`:

```env
MONGODB_URI=mongodb+srv://...
CLOUDINARY_API_KEY=...
ADMIN_USER=...
```

O comando `dotenv.config()` lê esse arquivo e coloca essas informações no `process.env`. Assim seu código fica seguro!

---

## 🚀 Por que Node.js + Express?

- **Rápido e Leve**: Perfeito para lidar com muitas conexões simultâneas.
- **Ecossistema Gigantesco**: Através do `npm`, temos milhões de pacotes prontos para usar.
- **Uma Linguagem só**: Você aprende JavaScript e consegue fazer tanto o Frontend quanto o Backend!

---

💡 *O backend é como a cozinha de um restaurante: o cliente não vê, mas é lá que tudo é preparado e organizado.*
