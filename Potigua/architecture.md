# 🏗️ Arquitetura do Projeto

Entender a estrutura de como as coisas se conectam é o primeiro passo para não se perder no código.

## 💎 Estrutura Global

O projeto Potigua é uma aplicação **Full-Stack Cliente-Servidor**. Ele é dividido em duas partes principais que conversam entre si:

1.  **Frontend (Pasta `src/`)**: É o que o usuário vê. O código que roda no navegador do cliente (seu Chrome, Firefox, celular). É construído com **React**.
2.  **Backend (Pasta `server/`)**: É o cérebro da aplicação. O código que roda no servidor (um computador longe, como no Render). É construído com **Node.js** e **Express**.

### Por que separar em dois (Frontend e Backend)?

Para permitir que a interface (o que o usuário mexe) seja independente da lógica e do banco de dados (o que guarda os dados). Isso facilita a manutenção e escalabilidade.

---

## 🛠️ O Fluxo da Informação

Quando um usuário acessa o site, o seguinte acontece:

1.  O **Frontend** carrega e pede os "Produtos" (presentes) ao servidor.
2.  O **Backend** recebe esse pedido, vai no **Banco de Dados (MongoDB)** buscar os presentes.
3.  O **Backend** envia de volta uma lista (em formato JSON) para o **Frontend**.
4.  O **Frontend** recebe esses dados e os "desenha" na tela usando componentes React.

---

## 📂 Organização das Pastas

### Frontend (`/src`)
- `components/`: Pequenas peças de UI reutilizáveis (botões, cards de produtos).
- `pages/`: As visualizações completas para cada rota (Home, Login, Admin).
- `styles/`: Onde definimos como o site deve parecer (cores, fontes, temas).
- `App.jsx`: O componente principal que "embrulha" tudo e cuida das rotas.
- `main.jsx`: O ponto de entrada onde o React é iniciado no HTML.

### Backend (`/server`)
- `models/`: Aqui definimos a estrutura dos dados (como o `Product.js`).
- `index.js`: O arquivo principal do servidor, onde as rotas (URLs da API) são configuradas.
- `.env`: (Onde ficam seus segredos, como as chaves do Banco de Dados e Cloudinary).

---

## ☁️ O Papel de Terceiros

- **Cloudinary**: Para não pesarmos o nosso servidor guardando imagens, enviamos as fotos dos presentes para a Cloudinary na nuvem. Só guardamos o "link" dessa foto no banco de dados.
- **Render**: É a plataforma onde "hospedamos" o nosso projeto para que qualquer pessoa na internet possa acessar (conhecido como *Deploy*).

---

## 💡 Exercício de Observação
Dê uma olhada no seu arquivo `src/App.jsx`. Nele você verá o uso do `react-router-dom`, que é responsável por mudar a página sem recarregar o navegador inteiro. Isso é o que torna a experiência do site tão fluida!

---

💡 *Entender a arquitetura te dá a segurança de saber EXATAMENTE onde mexer quando quiser mudar algo.*
