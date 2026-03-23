# ⚛️ React & Vite (Frontend)

O **React 19** é a nossa biblioteca principal para criar as telas que o usuário vê (UI). Já o **Vite** é o motor que roda tudo — ele compila seu código rapidinho e atualiza a tela instantaneamente quando você salva um arquivo (HMR - Hot Module Replacement).

## 🧠 Conceitos Principais

- **Components**: Pequenos blocos de código (como o botão, o header) que se encaixam para formar a tela.
- **JSX**: Uma forma de escrever HTML-como-JS que o React usa para facilitar as coisas.
- **Props**: "Propriedades" que passamos para um componente para mudar seu comportamento (ex: passar a cor para o botão).
- **Hooks**: Funções especiais do React (como `useState` e `useEffect`) que nos ajudam a guardar dados e fazer coisas em certos momentos.
- **Virtual DOM**: O React cria uma cópia da tela na memória para atualizar apenas o que for realmente necessário, sendo super rápido.

---

## 💡 Explicação Simplificada

O **React** é como brincar de **LEGO**.
1.  **Botão** é uma peça verde.
2.  **Cardápio** é a base do brinquedo.
3.  Você encaixa o botão verde 10 vezes no cardápio e, se quiser mudar a cor de todos para azul, muda em um lugar só (no código do componente)!
4.  O **Vite** é a **Luva de Super-Herói** que carrega todas as peças do balde (arquivos) e monta o brinquedo na mesa (navegador) na velocidade da luz.

---

## ❓ Por que usamos o React 19?

- **Reutilização**: Escreve o código uma vez e usa em várias páginas.
- **Estado Dinâmico**: O carrinho de compras se atualiza na hora, sem recarregar a página! Isso deixa o "sentimento" do site muito mais moderno.
- **Ecossistema Gigante**: Milhões de bibliotecas prontas para gráficos, notificações, animações.

---

## 🛠️ Exemplos de Uso no Projeto

### Componente de Botão (`src/components/Button/index.jsx`)

Um componente reutilizável e estilizado:
```jsx
import { ContainerButton } from './styles';

export function Button({ children, ...rest }) {
  return (
    <ContainerButton {...rest}>
      {children}
    </ContainerButton>
  );
}
```

### Usando o Botão em outra página (`src/pages/Login/index.jsx`)

Aqui consumimos o componente:
```jsx
// No topo do arquivo
import { Button } from '../../components/Button';

// Dentro do componente de Login
<Button type="submit">Entrar no DevBurguer</Button>
```

### O Ponto de Entrada (`src/main.jsx`)

Onde o React "cola" o App no navegador:
```jsx
import React from 'react';
import ReactDOM from 'react-dom/client';
import { RouterProvider } from 'react-router-dom';
import { router } from './routes';
import GlobalStyles from './styles/globalStyles';

ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <GlobalStyles />
    <RouterProvider router={router} />
  </React.StrictMode>
);
```

---

## ✅ Desafios para Estudantes

1.  **Onde mudar o título da página?** Procure o arquivo `index.html` na raiz da pasta `FrontEnd`. Como ele se conecta com o JavaScript? (Dica: procure a tag `script`).
2.  **Qual a diferença entre `.js` e `.jsx`?** Você notou que quase todos os arquivos de componente no projeto terminam em `.jsx`? Por que isso é importante para o Vite e o React? (Dica: JS é JavaScript puro; JSX é JavaScript com XML/HTML).

---

## 📚 Links Úteis
- [Documentação Oficial do React (Novo Site!)](https://react.dev/)
- [Guia Rápido do Vite](https://pt.vite.dev/guide/)
- [Curso de React para Iniciantes (MDN)](https://developer.mozilla.org/pt-BR/docs/Learn/Tools_and_testing/Client-side_JavaScript_frameworks/React_getting_started)
