# 🌐 Axios (API Communication)

O **Axios** é o nosso "Motoboy de Dados". Ele é o responsável por levar os dados de Login, por exemplo, até o servidor (Backend) e trazer a resposta: "Logado com sucesso!". No DevBurguer, configuramos o Axios com uma "Base URL" para que não precisemos ficar escrevendo o link do servidor toda hora.

## 🧠 Conceitos Principais

- **Base URL**: O endereço fixo do nosso servidor (ex: `http://localhost:3001`).
- **HTTP Methods**: Qual a ação do motoboy? `POST` (levar dados), `GET` (buscar dados), `PUT` (editar), `DELETE` (apagar).
- **Interceptors**: Um "pedágio" que o motoboy passa. Toda vez que ele vai pro backend, ele leva a "Pulseira VIP" (Token JWT) no bolso (headers).
- **Instância**: Uma configuração do Axios que já vem com o endereço do servidor "salvo na memória".
- **Await / Async**: Esperar o motoboy voltar do backend antes de fazer outra coisa (ex: carregar os favoritos).

---

## 💡 Explicação Simplificada

O **Axios** é o **Delivery do Backend**.
1.  Você quer buscar a lista de hambúrgueres.
2.  O FrontEnd chama o **Motoboy do Axios** (`api.get('/products')`).
3.  O Motoboy vai até a lanchonete (Backend), pega a bandeja de lanches (Dados JSON) e traz de volta.
4.  O Motoboy também já sabe o caminho de cor, pois configuramos a **Base URL** (O endereço da lanchonete).

---

## ❓ Por que usamos o Axios?

- **Fácil Tratamento de Erros**: O Axios avisa você na hora se o backend estiver fora do ar ou se a senha estiver errada.
- **Interceptors**: Podemos colocar o Token de segurança em todas as chamadas de uma vez só, sem trabalho repetido.
- **Promessas (Promises)**: O Axios funciona perfeitamente com o JavaScript moderno, tornando o código limpo e fácil de ler.

---

## 🛠️ Exemplos de Uso no Projeto

### Criando a Instância (`src/services/api.js`)

Aqui é onde o endereço do backend é "decorado":
```js
import axios from 'axios';

const api = axios.create({
  baseURL: 'http://localhost:3001', // O endereço do nosso Backend!
});

// Interceptor: Coloca o token de login em todas as chamadas!
api.interceptors.request.use(async (config) => {
  const userData = await localStorage.getItem('devburguer:userData');
  const token = userData && JSON.parse(userData).token;

  if (token) {
    config.headers.Authorization = `Bearer ${token}`; // Leva o crachá no bolso!
  }

  return config;
});

export default api;
```

### Buscando dados no Componente (`src/pages/Home/index.jsx`)

Chamando o motoboy para buscar os produtos:
```jsx
import api from '../../services/api';

async function loadProducts() {
  const { data } = await api.get('/products'); // motoboy buscou!
  setProducts(data); // desenha na tela!
}
```

---

## ✅ Desafios para Estudantes

1.  **Onde mudar o endereço do servidor?** Procure o arquivo `.env` na raiz da pasta `FrontEnd`. O que acontece se você mudar a `VITE_API_URL` lá? (Dica: o site perde o sinal e não carrega mais nada!).
2.  **O que o `config.headers.Authorization` faz?** Veja como o backend e o frontend se conversam. Como o backend sabe quem é o usuário olhando apenas para esse "header"? (Dica: veja o arquivo de Backend sobre Firebase Admin).

---

## 📚 Links Úteis
- [Documentação Oficial do Axios](https://axios-http.com/ptbr/docs/intro)
- [Como usar Interceptors no Axios?](https://blog.logrocket.com/how-to-use-axios-interceptors/)
- [O que são verbos HTTP (GET, POST, etc)?](https://developer.mozilla.org/pt-BR/docs/Web/HTTP/Methods)
