# 📦 Zustand: O Cérebro do Mundo (Estado Global)

## Conceitos Principais

- **Store:** O lugar onde toda a informação mora (lista de blocos, seleção, histórico).
- **`set` e `get`:** Como você altera ou lê os dados da memória central.
- **Hook:** Como você "pede" uma informação para o React exibir.
- **Histórico:** Implementar Undo/Redo (Desfazer/Refazer) é muito fácil com Zustand!

## Explicação Simplificada

Em um jogo de blocos, temos centenas de pequenos objetos. O React sofreria para passar a informação de "quem foi deletado" ou "quem está selecionado" de um componente para outro usando apenas `props`. 

O **Zustand** cria um balcão único de informações. Qualquer componente (como o botão de Deletar ou o bloco sendo pintado no 3D) pode ir nesse balcão, checar quem está selecionado e pedir para atualizar a lista de blocos global.

## Por que isso existe?

O React tem formas nativas de gerenciar estado (`useState`), mas para dados complexos e que mudam muito rápido (como mover um mouse no 3D pintando blocos), o Zustand é mais leve, performático e muito fácil de entender.

## Exemplos de uso no BlockForge

No arquivo `src/store/blockStore.js`, definimos toda a nossa lógica de mundo. Veja como guardamos os blocos:

```javascript
export const useBlockStore = create((set, get) => ({
  blocks: [], // Nossa lista de cubos no 3D
  
  // Ação para adicionar um bloco
  addBlock: (position, type) => {
    const { blocks } = get();
    const newBlocks = [...blocks, { id: uuidv4(), position, type }];
    set({ blocks: newBlocks });
  },
}));
```

Dentro de um componente, consumimos assim:

```javascript
const blocks = useBlockStore((state) => state.blocks);
```

## Problemas Comuns

- **Mutação Direta:** Nunca faça `state.blocks.push(block)`. No Zustand (e React), você sempre deve criar uma cópia nova: `[...blocks, newBlock]`.
- **Renders Excessivos:** Se você pedir o `state` inteiro, o React vai redesenhar seu componente por qualquer mudancinha. **Sempre filtre:** `useBlockStore(state => state.apenasOQueEuQuero)`.

## Boas Práticas

- **Past e Future:** No `blockStore.js`, usamos os arrays `past` e `future` para fazer o sistema de **Ctrl+Z** (Undo). É o padrão que os softwares profissionais usam!
- **Lógica na Store:** Tente deixar a matemática complexa de "onde o bloco deve entrar" dentro da Store, e não nos componentes.
