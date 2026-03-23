# 🗄️ Banco de Dados e Mongoose

No seu projeto Potigua, usamos **MongoDB**, um banco de dados NoSQL (Não Relacional) que guarda dados em formato de "documentos" (bastante parecidos com JSON).

## 📊 O Modelo de Dados (`Product.js`)

No MongoDB, as informações não são bagunçadas. Usamos o **Mongoose** para criar um "esquema" (schema) que define o que deve ter no nosso presente:

```js
// No server/models/Product.js
const productSchema = new mongoose.Schema({
  name: String,        // Nome do presente (ex: "AirFryer")
  link: String,        // Link para comprar (ex: "https://amazon.com/...")
  description: String, // Descrição curta
  image: String,       // A URL da imagem guardada na Cloudinary!
  createdAt: { type: Date, default: Date.now },
});
```

---

## 🔍 Como o Mongoose nos ajuda?

Em vez de escrever comandos complexos de banco de dados, o Mongoose nos permite usar funções do JavaScript:

- **Buscar tudo**: `Product.find()`
- **Buscar por um ID**: `Product.findById(id)`
- **Salvar novo**: `new Product({ ... }).save()`
- **Deletar por um ID**: `Product.findByIdAndDelete(id)`

---

## ☁️ Cloudinary: Armazenamento de Imagem

O banco de dados (MongoDB) é ótimo para guardar **texto**. Ele **NÃO** é bom para guardar **arquivos** (como fotos). Por isso:

1.  Enviamos a foto para a **Cloudinary** (um Google Drive profissional para programadores).
2.  A Cloudinary nos devolve um **Link** (URL).
3.  Guardamos esse **Link** no MongoDB dentro do campo `image`.

Dessa forma, o seu site carrega as imagens direto da nuvem da Cloudinary, o que é muito mais rápido e seguro!

---

## 🌍 MongoDB Atlas: O Banco na Nuvem

Você deve estar usando o **MongoDB Atlas**, que é onde o seu banco de dados mora na internet.
-   **MONGODB_URI**: É a "chave da sua casa", o endereço que seu servidor usa para entrar e guardar as coisas no banco.

---

## 🎯 Por que MongoDB?

- **Flexibilidade**: Se hoje o presente tem `nome`, `link` e `preço`, e amanhã quisermos adicionar `categoria`, é muito fácil mudar o código sem quebrar tudo o que já existe.
- **Formato Amigável**: Como ele usa um formato parecido com o JSON, o JavaScript (que é o que usamos no projeto) o entende perfeitamente.

---

💡 *O Banco de Dados é a memória duradoura da sua aplicação. É lá que o "estado" do seu projeto sobrevive, mesmo quando você desliga o computador.*
