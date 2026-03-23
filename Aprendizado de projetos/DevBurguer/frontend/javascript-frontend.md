# 💻 JavaScript Moderno no Frontend (React)

No frontend, o JavaScript é usado para dar vida aos componentes do React. Usamos muito as versões mais novas do JS (ES6+) para tornar o código do `FrontEnd/src` mais fácil de ler e manter.

## 🧠 Conceitos Principais

- **Spread Operator (`...rest`)**: Pegar todas as propriedades que sobraram e passar para outro componente (muito usado no `Button` e `Input`).
- **Destructuring em Props**: Pegar o `children` ou os dados passados para o componente logo no começo.
- **Array Methods (`map`, `filter`, `reduce`)**: Usamos o `map` para percorrer a lista de lanches do backend e criar um card para cada um.
- **Template Literals (Crases ``)**: Usado no **Styled Components** para escrever CSS e no **Axios** para passar o ID do produto na URL (`/products/${id}`).
- **Módulos JS (`import`/`export`)**: Como conectamos as pastas `pages`, `components` e `services`.

---

## 💡 Exemplos Reais no Projeto

### Mapeando Lanches (Array `map`)
```jsx
// Na home do site:
{ products.map( product => (
  <CardProduct key={product.id} product={product} />
)) }
```

### Espalhando Propriedades (`...rest`)
```jsx
// No componente de Botão:
export function Button({ children, ...rest }) {
  return <ContainerButton {...rest}>{children}</ContainerButton>;
}
// Isso faz o botão aceitar 'onClick', 'type', 'disabled' sem precisarmos escrever um por um!
```

---

## ✅ Desafios para Estudantes

1.  **Mude o Filtro**: No cardápio (`Menu`), tente achar o código que filtra os lanches por Categoria. Como o JavaScript sabe qual lanche mostrar e qual esconder? (Dica: procure por `.filter`).
2.  **Onde o "Spread" é usado no formulário?** Veja o `...register('email')`. O que o React Hook Form está fazendo com esses três pontinhos?

---

## 📚 Links Úteis
- [JavaScript for React (Guia Visual)](https://www.freecodecamp.org/news/javascript-concepts-before-learning-react/)
- [Entendendo o Map, Filter e Reduce](https://www.alura.com.br/artigos/map-filter-reduce-javascript)
- [Destructuring no JavaScript Cheat Sheet](https://codetoimg.com/cheat-sheets/js-destructuring)
