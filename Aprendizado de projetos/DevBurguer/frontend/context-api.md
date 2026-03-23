# 📦 Context API (Global State Management)

O **Context API** do React é o nosso "Cérebro Global". Ele permite que uma informação (como o que tem no carrinho do cliente) seja acessada em qualquer página sem precisar passar por mil componentes ("Prop Drilling").

## 🧠 Conceitos Principais

- **Provider**: Um componente que "provê" o dado para todo mundo lá embaixo de uma árvore de pastas.
- **Consumer (useContext)**: O componente que "consome" ou lê aquela informação.
- **CartContext**: Gerencia os produtos que o cliente adicionou, a quantidade e o cálculo do total.
- **UserContext**: Gerencia se o usuário está logado, qual o nome dele e se é Administrador.
- **Persistência**: Guardar os dados no `localStorage` do navegador para que não sumam quando o usuário atualizar a página (`F5`).

---

## 💡 Explicação Simplificada

O **Context API** é a **Rádio da Lanchonete**.
1.  **O Locutor (Provider)** está na sala central da rádio.
2.  **A Notícia (Dada/Estado)** é: "O Burguer de Bacon entrou no carrinho!".
3.  **O rádio (useContext)** está em todas as mesas (páginas).
4.  Qualquer mesa pode ouvir a notícia e mostrar na tela: "Você tem 1 item no carrinho!", sem precisar correr até o balcão.

---

## ❓ Por que usamos o Context API?

- **Simplificação**: Evita o "Prop Drilling" (passar props de pai para filho, neto, bisneto só para chegar num botão).
- **Código Limpo**: A lógica do carrinho fica isolada em um arquivo só (`CartContext.jsx`), facilitando o conserto de bugs.
- **Performance**: É ótimo para gerenciar estados globais sem complexidades de bibliotecas maiores (como Redux) para este tipo de projeto.

---

## 🛠️ Exemplos de Uso no Projeto

### O Cérebro do Carrinho (`src/hooks/CartContext.jsx`)

Aqui criamos e exportamos a lógica:
```jsx
const CartContext = createContext({});

export const CartProvider = ({ children }) => {
  const [cartProducts, setCartProducts] = useState([]);

  // Adicionar ao carrinho:
  const putProductInCart = async (product) => {
    // ... logica de adicionar
  };

  return (
    <CartContext.Provider value={{ putProductInCart, cartProducts }}>
      {children}
    </CartContext.Provider>
  );
};
```

### Usando o Carrinho em um Componente (`src/components/CardProduct/index.jsx`)

Lendo a função do contexto:
```jsx
import { useCart } from '../../hooks/CartContext'; // Hook customizado

function CardProduct({ product }) {
  const { putProductInCart } = useCart(); // Pegamos a função aqui!

  return <button onClick={() => putProductInCart(product)}>Adicionar</button>;
}
```

---

## ✅ Desafios para Estudantes

1.  **Onde o site aprende sobre o Usuário logado?** Procure o arquivo `src/hooks/UserContext.jsx`. Como ele salva o usuário no computador? (Dica: procure por `localStorage.setItem`).
2.  **O "Tudo em Volta"**: Abra o arquivo `src/main.jsx`. Como envolvemos o aplicativo inteiro com esses provedores para que todos tenham acesso ao Contexto? (Dica: veja se lá existem os `<AppProvider>`, `<UserProvider>`, etc).

---

## 📚 Links Úteis
- [Documentação Oficial do Context API](https://react.dev/reference/react/createContext)
- [Quando usar Context vs Props?](https://www.alura.com.br/artigos/react-context-api)
- [O que é Prop Drilling?](https://blog.geekhunter.com.br/prop-drilling-react/)
