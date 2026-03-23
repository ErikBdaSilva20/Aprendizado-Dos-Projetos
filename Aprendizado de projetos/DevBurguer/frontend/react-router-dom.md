# 🛣️ React Router DOM (Navigation)

O **React Router DOM** é o "Mapa de Trilhas" do DevBurguer. Ele controla o que o usuário vê na URL do navegador e qual página o React deve desenhar na tela, sem precisar recarregar o site inteiro (SPA - Single Page Application).

## 🧠 Conceitos Principais

- **Router**: O componente principal que cuida de tudo.
- **Routes / Route**: Onde definimos os caminhos (ex: `/login` mostra o formulário; `/` mostra a home).
- **Link / useNavigate**: Como mudamos de página sem causar um `F5` no navegador.
- **Private Routes**: Caminhos "atrás de uma porta trancada" que só podem ser vistos por quem logou (é aqui que o middleware de auth acontece no Front!).
- **URL Parameters**: Como passar um ID de produto (`/admin/editar-produto/12345`) pela URL.

---

## 💡 Explicação Simplificada

O **React Router** é a **Porta Giratória** do Shopping.
1.  Você clica em "Login".
2.  A porta gira e você está na "Sala de Login".
3.  O teto e o chão do shopping (Layout) continuam os mesmos, mas as paredes (Páginas) mudam.
4.  O navegador ainda acha que você está no mesmo prédio, mas a plaquinha na entrada do shopping (URL) mudou para `/login`.

---

## ❓ Por que usamos o React Router 7?

- **SPA (Single Page Application)**: O site nunca dá aquela "piscada branca" chata ao mudar de link.
- **HOC (High Order Components)**: Poder proteger várias páginas de uma vez só!
- **Data Loading**: No Router 7, você pode carregar os dados do lanche enquanto a página está mudando, tornando o site muito mais fluido.

---

## 🛠️ Exemplos de Uso no Projeto

### Definindo as Rotas (`src/routes/index.jsx`)

Onde o mapa completo é desenhado:
```jsx
import { createBrowserRouter } from 'react-router-dom';
import { Home, Login, Cart, Admin } from '../pages';
import { PrivateRoute } from './PrivateRoute';

export const router = createBrowserRouter([
  { path: '/', element: <Home /> },
  { path: '/login', element: <Login /> },
  {
    path: '/admin',
    element: (
      <PrivateRoute isAdmin>
        <Admin />
      </PrivateRoute>
    ),
  },
]);
```

### O Guarda das Rotas (`src/routes/PrivateRoute.jsx`)

Quem decide se o usuário pode entrar ou deve voltar pro Login:
```jsx
export function PrivateRoute({ children, isAdmin }) {
  const user = JSON.parse(localStorage.getItem('devburguer:userData'));

  if (!user) return <Navigate to="/login" />;
  if (isAdmin && !user.admin) return <Navigate to="/" />; // "Você não é o dono aqui!"

  return children;
}
```

---

## ✅ Desafios para Estudantes

1.  **Mude de página sem clicar**: Procure no código onde usamos o `useNavigate`. Em qual momento o navegador pula para a Home sozinho? (Dica: procure no `Login/index.jsx` depois do login com sucesso).
2.  **Onde as rotas do ADMIN começam?** Veja como as rotas `/admin`, `/admin/pedidos` e `/admin/produtos` são organizadas no arquivo de rotas. Elas usam um arquivo separado?

---

## 📚 Links Úteis
- [Documentação Oficial do React Router](https://reactrouter.com/)
- [O que é uma SPA?](https://www.alura.com.br/artigos/spa-single-page-applications-o-que-sao)
- [Navegação de Programação no React Router](https://www.freecodecamp.org/news/how-to-use-usenavigate-react-router-dom/)
