# ⚛️ Dominando React (Frontend)

O React é a biblioteca que cuida de toda a interface do seu site. No projeto Potigua, usamos a versão 19, a mais moderna.

## 🧱 Peças do Quebra-cabeça: Componentes

A ideia do React é dividir a página em pequenos pedaços chamados **Componentes**.

### 1. **Componentes Funcionais**
Toda função que retorna algo parecido com HTML (JSX) é um componente:

```jsx
// Exemplo no src/components/CardProduto/index.jsx
function CardProduto({ produto }) {
  return (
    <div>
      <h3>{produto.name}</h3>
      <p>{produto.description}</p>
    </div>
  );
}
```

### 2. **JSX: Misturando HTML e JS**
Diferente do HTML comum, no React você pode colocar lógica do JavaScript diretamente no meio da marcação:

```jsx
// Usando { } para colocar código JS no HTML
<h1>{usuario.nome}</h1>
```

---

## 🎣 Os "Hooks" (Ganchos) do React

Os Hooks são funções especiais que permitem "encaixar" lógica no ciclo de vida de um componente.

### `useState`: O Estado da sua Tela
Toda vez que algo mudar na tela (um texto, uma imagem, uma lista), o React precisa saber disso. O `useState` cuida disso:

```jsx
const [produtos, setProdutos] = useState([]);

// Quando setProdutos é chamado, o React redesenha a lista na tela automaticamente!
```

### `useEffect`: Efeitos Colaterais
Usamos o `useEffect` para carregar dados assim que o site abre:

```jsx
useEffect(() => {
  // Chamamos a nossa API para buscar os presentes
  axios.get('/api/products').then(res => setProdutos(res.data));
}, []); // O [] significa: "só rode isso UMA vez quanod a página abrir"
```

---

## 🛣️ Rotas: `react-router-dom`

Seu site tem várias páginas: a vitrine (`/`), o login (`/login`) e o painel de adm (`/admin`).
O `react-router-dom` gerencia para onde o usuário vai sem precisar recarregar o navegador.

Confira no arquivo `src/App.jsx` como as rotas estão definidas!

---

## 💅 CSS no React: `styled-components`

Em vez de criar arquivos `.css` separados, usamos **Styled Components**. Isso permite criar "tags HTML" que já vêm estilizadas, garantindo que o CSS de um botão não interfira em outro lugar.

```jsx
// Exemplo de como criamos um botão estilizado
const Botao = styled.button`
  background-color: #ff69b4;
  color: white;
  padding: 10px;
  border-radius: 5px;
`;
```

---

## 🎯 Por que React?

- **Reutilização**: Um mesmo componente de "Botão" ou "Card" pode ser usado em várias páginas.
- **Velocidade**: O React só altera na tela o que realmente mudou, economizando processamento.
- **Comunidade**: É a tecnologia mais usada no mundo para criar interfaces modernas.

---

💡 *O React transforma a UI em dados. No final das contas, você manipula dados e a interface reage sozinha.*
