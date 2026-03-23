# 💅 Styled Components (CSS-in-JS)

No DevBurguer, não usamos arquivos `.css` normais. Usamos o **Styled Components**, que nos permite escrever CSS diretamente dentro do JavaScript, criando "Componentes Estilizados".

## 🧠 Conceitos Principais

- **CSS-in-JS**: Estilo (design) e Lógica (comportamento) ficam no mesmo lugar.
- **Tagged Template Literals**: A sintaxe `styled.div` com crases que permite escrever CSS no JS.
- **Props dinâmicas**: Mudar a cor do botão baseado no que acontece no site (ex: se o carrinho está vazio, o botão fica cinza).
- **Global Styles**: Um lugar central para resetar o navegador e colocar fontes e cores padrão.
- **Nesting**: Escrever um estilo "dentro" do outro como se fosse Sass/Less.

---

## 💡 Explicação Simplificada

O **Styled Components** é a **Capa do Invisível**.
1.  Você cria uma `div` normal.
2.  Dá um "superpoder" pra ela usando o `styled.div`.
3.  Dentro do JS, você diz: "Você agora é o Container do Cardápio, tem borda preta e fundo branco!".
4.  O navegador entrega o componente pronto já com o design, sem precisar ficar procurando arquivo `.css` perdido nas pastas.

---

## ❓ Por que usar o Styled Components?

- **Sem Conflito de Nomes**: O CSS de um botão não vai vazar para outro componente por acidente.
- **Facilidade no Design**: Se você quer que o preço fique vermelho quando tem oferta, basta passar uma "prop" do React para o CSS!
- **Manutenção**: Se você deletar o arquivo do componente, o estilo vai embora junto, deixando o projeto limpo.

---

## 🛠️ Exemplos de Uso no Projeto

### Criando o Estilo (`src/components/CardProduct/styles.js`)

Escrita do CSS com lógica:
```js
import styled from 'styled-components';

export const Container = styled.div`
  background: #ffffff;
  padding: 20px;
  border-radius: 30px;
  box-shadow: 0px 30px 60px rgba(57, 57, 57, 0.1);
  display: flex;
  gap: 12px;
  
  img {
    width: 150px;
    height: 150px;
    border-radius: 20px;
  }
`;
```

### Aplicando Props no CSS

Mundando a cor do botão dinamicamente:
```js
export const MyButton = styled.button`
  background-color: ${ (props) => props.isActive ? '#9758a6' : '#efefef' };
  color: ${ (props) => props.isActive ? '#ffffff' : '#9758a6' };
`;
```

---

## ✅ Desafios para Estudantes

1.  **Onde mudar a cor global do site?** Procure o arquivo `src/styles/globalStyles.js`. Tente mudar o `background` do `body` e veja o que acontece.
2.  **Por que as pastas de componentes têm um `index.jsx` e um `styles.js`?** Como esses dois arquivos se conversam? Veja os `import` no `index.jsx`.

---

## 📚 Links Úteis
- [Documentação Oficial do Styled Components](https://styled-components.com/docs)
- [O que é CSS-in-JS? (Vantagens)](https://www.alura.com.br/artigos/css-in-js-styled-components-estilizando-no-react)
- [Sintaxe de Tagged Templates no JS](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript/Reference/Template_literals#tagged_templates)
