# 🖼️ Cloudinary & Multer (Storage)

O **Cloudinary** é o nosso "HD externo na nuvem" para guardar fotos de lanches e categorias. O **Multer** é o middleware do Express que nos permite receber esses arquivos (que não são texto normal) quando o usuário faz um "Upload".

## 🧠 Conceitos Principais

- **Upload de Arquivos**: O navegador envia um formulário do tipo `multipart/form-data`.
- **Multer Storage**: Onde o arquivo vai parar antes de ser processado (memória ou HD).
- **Cloudinary Storage Service**: Um serviço de terceiros (SaaS) que otimiza fotos, cria miniaturas e entrega imagens super rápidas.
- **Middleware de arquivo**: Um código que "pega" o arquivo da requisição HTTP e coloca na pasta certa ou envia para a nuvem.
- **Buffer**: O pedaço de dado binário da foto enquanto ela está sendo transferida.

---

## 💡 Explicação Simplificada

O **Multer** é o **Motoboy** que traz a foto do cliente. O **Cloudinary** é o **Álbum de Fotos Digital**.
1.  O administrador anexa uma foto de "Burguer de Picanha" no Painel Admin.
2.  O **Multer** no backend intercepta essa encomenda e guarda temporariamente.
3.  O nosso código envia essa foto para o **Cloudinary**.
4.  O Cloudinary devolve uma **URL** (um link) da foto. Nós guardamos esse link no banco PostgreSQL para mostrar no FrontEnd depois!

---

## ❓ Por que usamos o Cloudinary?

- **Espaço Livre**: Não ocupamos o HD do nosso servidor com gigabytes de fotos.
- **Transformação**: O Cloudinary pode cortar e redimensionar a foto na hora, apenas mudando o link.
- **Otimização**: Ele converte fotos pesadas (como `.jpg` de 5MB) para formatos leves (como `.webp` de 200KB) sozinho!

---

## 🛠️ Exemplos de Uso no Projeto

### Configuração do Multer (`src/config/multer.cjs`)

Onde configuramos como receber a foto:
```js
import multer from 'multer';
import { CloudinaryStorage } from 'multer-storage-cloudinary';
import { v2 as cloudinary } from 'cloudinary';

const storage = new CloudinaryStorage({
  cloudinary: cloudinary,
  params: {
    folder: 'devburguer',
    allowed_formats: ['jpg', 'png', 'jpeg', 'webp'],
  },
});

export const upload = multer({ storage });
```

### Usando na Rota (`src/routes.js`)

Aqui indicamos que a rota `/products` espera um arquivo chamado `file`:
```js
routes.post('/products', upload.single('file'), ProductController.store);
```

### Salvando no Controller (`src/app/controllers/productController.js`)

Onde pegamos o caminho do arquivo (path):
```js
const { path } = request.file; // Pegamos o 'link' que o Cloudinary gerou!
await Product.create({ name, price, path, category_id });
```

---

## ✅ Desafios para Estudantes

1.  **Onde as credenciais do Cloudinary estão guardadas?** Verifique o `.env` do backend (`CLOUDINARY_CLOUD_NAME`).
2.  **O que acontece se eu tentar enviar um arquivo `.txt`?** Olhe no arquivo de configuração do Multer. Como ele sabe que arquivos de texto não são permitidos? (Dica: `allowed_formats`).

---

## 📚 Links Úteis
- [Documentação Oficial do Cloudinary para Node.js](https://cloudinary.com/documentation/node_integration)
- [Repositório do Multer (GitHub)](https://github.com/expressjs/multer)
- [O que é um middleware multpart/form-data?](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/POST)
- [Multer Storage Cloudinary library](https://www.npmjs.com/package/multer-storage-cloudinary)
