# 💅 Styled Components: O Design no Código

## Conceitos Principais

- **Styled Tags:** `styled.div` – Tags HTML reais que já vêm com estilos.
- **Dinamismo (Props):** `background: ${props => props.active ? 'blue' : 'gray'}` – Mudar a aparência com base no estado.
- **Encapsulamento:** O CSS de um botão não "vaza" para outro botão do projeto.
- **Temas:** Facilidade imensa para criar "Modo Escuro" (Dark Mode).

## Explicação Simplificada

Imagine que você está escrevendo o CSS fora de um arquivo `.css` e colocando ele **dentro** do JavaScript. O **Styled Components** une a beleza do CSS com o poder do JS. 

Ao invés de criar `class="botao-azul"`, você cria um componente `<BoutonAzul />` e define o estilo dele diretamente.

## Por que isso existe?

- Para evitar o famoso "CSS Global" que vira bagunça.
- Para podermos passar informações (Props) do React diretamente para o estilo. No BlockForge, por exemplo, a largura da Sidebar ou as cores dos blocos na barra lateral podem mudar conforme você interage com elas!

## Exemplos de uso no BlockForge

No arquivo `src/components/ui/Sidebar/Sidebar.jsx`, temos componentes que mudam a cor se estiverem ativos:

```jsx
const SidebarItem = styled.div`
  padding: 10px;
  background: ${props => props.selected ? 'rgba(255, 255, 255, 0.2)' : 'transparent'};
  border-radius: 8px;
  cursor: pointer;
  transition: all 0.2s ease-in-out;

  &:hover {
    background: rgba(255, 255, 255, 0.1);
  }
`;
```

## Problemas Comuns

- **Classes Dinâmicas:** Ele gera classes estranhas no navegador (ex: `sc-hSqwXP`). Não se assuste, isso é para garantir o isolamento!
- **Componentes Repetidos:** Criar estilos demais pode poluir seu arquivo. **Separe-os se ficarem muito grandes.**

## Boas Práticas

- **GlobalStyle:** Use `createGlobalStyle` apenas para reset de CSS e fontes. Para todo o resto, use componentes isolados.
- **Herança:** Você pode herdar estilos de outros componentes: `const DangerButton = styled(CommonButton)`.
