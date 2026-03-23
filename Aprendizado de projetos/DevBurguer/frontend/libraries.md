# 🛠️ Bibliotecas Auxiliares (Frontend)

O DevBurguer usa várias "Ajudantes" (Libraries) que economizam centenas de horas de trabalho de design e lógica. Aqui mostramos as 3 principais que você verá no código.

---

## 🔔 React Toastify (Notificações)

Pense no **Toastify** como o **Garçom Educado**.
- Ele mostra aquele "balãozinho" verde no canto da tela quando você adiciona um lanche.
- Ele avisa com um balão vermelho se você errar a senha no login.
- **Por que usamos?** É muito mais bonito do que o `alert()` padrão do navegador.

### Exemplo no Projeto (`src/pages/Login/index.jsx`)
```jsx
import { toast } from 'react-toastify';

toast.success('Bem-vindo ao DevBurguer! 🍔');
toast.error('Opa! Algo deu errado...');
```

---

## 🎠 React Multi Carousel (Carrosséis)

Pense no **Carousel** como a **Vitrine Giratória**.
- Ele permite que o cliente "corra" as fotos dos lanches ou das categorias pro lado com o dedo (no celular) ou com o mouse.
- Ele se adapta sozinho para mostrar 1 foto no celular ou 4 fotos no computador.
- **Por que usamos?** É super suave (smooth) e aguentar o "swipe" do toque na tela.

### Exemplo no Projeto (`src/components/CategoryCarousel/index.jsx`)
```jsx
import Carousel from 'react-multi-carousel';

<Carousel responsive={responsive} infinite={true}>
  {categories.map((category) => (
    <Card key={category.id}>{category.name}</Card>
  ))}
</Carousel>
```

---

## 🎨 MUI - Material UI (Pre-made Components)

Pense no **MUI** como um **Catálogo de Peças de Carro Prontas**.
- Ele nos dá ícones, tabelas admin e cabeçalhos já desenhados e seguindo o padrão do Google.
- No DevBurguer, usamos principalmente os **Icons** (o carrinho, a lupa, o usuário) e as **Tabelas** do admin.
- **Por que usamos?** Desenhar uma tabela de pedidos cheia de botões do zero é muito difícil e demorado.

### Exemplo no Projeto (`src/components/Header/index.jsx`)
```jsx
import ShoppingCartIcon from '@mui/icons-material/ShoppingCart'; // O ícone do carrinho!
import PersonIcon from '@mui/icons-material/Person'; // O ícone do bonequinho de login!

<ShoppingCartIcon style={{ color: '#fff' }} />
```

---

## 🔘 React Select (Custom Dropdowns)

Pense no **Select** como o **Cardápio de Opções Personalizado**.
- Ele é usado no cadastro de produtos para escolher a categoria.
- Ele permite buscar o nome da categoria digitando no teclado.
- **Por que usamos?** O `<select />` padrão do navegador é feio e impossível de estilizar direito.

---

## ✅ Desafios para Estudantes

1.  **Troque o ícone**: Encontre o arquivo do `Header` e mude o ícone de `PersonIcon` para um ícone de `Home` do MUI Icons. (Dica: procure no site do Material UI Icons).
2.  **Mude a cor do Toast**: O Toastify permite mudar a animação de entrada ou a posição (ex: para baixo). Tente achar as `ToastContainerProps` no arquivo `App.jsx` ou `main.jsx`.

---

## 📚 Links Úteis
- [React Toastify Documentation](https://fkhadra.github.io/react-toastify/introduction)
- [React Multi Carousel (GitHub)](https://github.com/YIZHUANG/react-multi-carousel)
- [Material UI (MUI) - Icons Collection](https://mui.com/material-ui/material-icons/)
- [React Select Documentation](https://react-select.com/home)
