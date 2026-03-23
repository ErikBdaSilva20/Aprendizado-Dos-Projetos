# 📚 Bibliotecas Essenciais

Para não precisarmos "reinventar a roda", usamos **Packages** (pacotes) ou **Libraries** (bibliotecas) criados por outros programadores.
Dê uma olhada no seu `package.json`: tem um monte delas lá. Vamos entender as mais importantes:

## 🔌 Axios: O Mensageiro (Frontend)

O Axios é o carteiro da sua aplicação. Ele pega os dados do seu React e leva até o seu servidor (API).

```js
// No Frontend (Exemplo de GET)
import axios from 'axios';

const buscarPresentes = async () => {
  const response = await axios.get('http://localhost:5000/api/products');
  console.log(response.data); // Exibe os presentes no console
};
```

### Por que usar Axios e não o `fetch` nativo?
O Axios é mais esperto: ele já converte para JSON sozinho, lida com erros de forma mais organizada e permite configurar uma URL padrão para todas as rotas.

---

## 🎨 Styled-Components: CSS no JavaScript

Diferente do CSS tradicional, o **Styled Components** permite criar componentes que já têm estilo embutido.

```js
const Titulo = styled.h1`
  color: #333;
  font-size: 24px;
`;
```

### O que isso resolve?
Ele resolve o problema de conflitos de classes (quando você coloca `class="botao"` em dois lugares e eles se misturam). No Styled-Components, o estilo é **único** para aquele componente.

---

## 🖼️ React-Icons: Ícones Rápidos

Em vez de baixar imagens de ícones (como o "Sair", "Editar", "Lixeira"), usamos o **React Icons**. Ele transforma os ícones em componentes React que você pode mudar de cor e tamanho como se fossem texto!

```jsx
import { FiTrash2 } from 'react-icons/fi'; // Ícone de Lixeira

<FiTrash2 size={20} color="red" />
```

---

## 📁 Multer: Lidar com Arquivos (Backend)

O formulário que o navegador envia no frontend não chega arrumadinho no servidor se tiver uma imagem. O **Multer** é o guarda na porta do backend que recebe esses "arquivos pesados" e os organiza para a gente usar no `req.file`.

---

## 📦 Vite: O Construtor (Build Tool)

O **Vite** não é uma biblioteca que você usa *dentro* do código, mas sim a ferramenta que **transforma** seu código JSX e arquivos modernos em um site de verdade que o navegador entende.
-   Ele é ultra-rápido porque não refaz o site todo toda vez que você salva o arquivo; ele só muda o pedacinho que você alterou!

---

💡 *As bibliotecas são os tijolos. Saber escolhê-las e usá-las te economiza muito tempo e esforço.*
