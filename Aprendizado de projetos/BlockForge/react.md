# ⚛️ React: O Organizador da Casa

## Conceitos Principais

- **Componentes:** `Block`, `Scene`, `Sidebar` - Pedaços independentes que montam o site.
- **Props:** No BlockForge, passamos `position` e `type` do componente `Scene` para o `Block`.
- **`useEffect`:** Rodar código de "efeito colateral" (como ouvir teclado ou carregar blocos de uma API).
- **`useRef`:** Criar uma "caixa" para guardar o acesso direto a um objeto do Three.js sem redesenhar o componente inteiro.
- **`useCallback`:** Fazer com que uma função seja guardada na memória para não ser recriada toda hora no 3D.

## Explicação Simplificada

O React é o "capataz" da obra. Ele decide **quando** redesenhar a tela. Se a lista de blocos no Zustand mudar, o React avisa todos os componentes de bloco: "Ei, redesenhem-se, pois algo mudou!".

Ele é o responsável por toda a parte da interface do usuário (UI) — os menus, botões, a sidebar — e por colocar o "olho" (Canvas) do Three.js na página.

## Por que isso existe?

Para que seu projeto não vire um "espaguete" de código. Com o React, podemos criar um componente `<Block />` **UMA VEZ** e chamá-lo mil vezes com posições diferentes. Isso traz organização e escala.

## Exemplos de uso no BlockForge

No arquivo `src/components/scene/Scene/index.jsx`, usamos o **useEffect** global de teclado para detectar o `Shift` e liberar a câmera:

```javascript
useEffect(() => {
  const handleKeyDown = (e) => { 
    if (e.key === 'Shift') setShiftPressed(true); 
  };
  window.addEventListener('keydown', handleKeyDown);
  // Limpeza (Crucial no React!)
  return () => window.removeEventListener('keydown', handleKeyDown);
}, []);
```

E renderizamos os blocos de forma dinâmica:

```jsx
{blocks.map((block) => (
  <Block
    key={block.id}
    id={block.id}
    position={block.position}
    type={block.type}
  />
))}
```

## Problemas Comuns

- **Loops Infindáveis:** Se você atualizar o estado dentro do `useEffect` sem passar o array de dependências `[]` certo, o site vai travar.
- **Estado Local vs Global:** No BlockForge, quase tudo é global (Zustand). Evite criar estados locais (`useState`) para dados que outros componentes precisam saber.

## Boas Práticas

- **Limpeza (`return`):** Sempre remova os listeners de eventos (`window.removeEventListener`) no final do seu `useEffect` para não gastar memória.
- **Memo:** Se tiver muitos componentes, use `React.memo` para eles não redesenharem sem necessidade.
