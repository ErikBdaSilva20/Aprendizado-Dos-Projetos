# 🟨 JavaScript Moderno: O Alicerce

## Conceitos Principais

- **Desestruturação:** `{ blocks, set } = useBlockStore()` – Atalho para declarar variáveis.
- **Spread Operador (`...`):** `[...blocks, newBlock]` – Criar cópias de listas sem alterar a original.
- **Arrow Functions:** `const fn = () => { ... }` – Jeito moderno de declarar funções.
- **Async/Await:** Carregar blocos de uma API (ver `minecraftApi.js`) de forma que o site não trave.
- **UUID:** `uuidv4()` – Gerar um ID único para cada bloco (cada cubo é único).

## Explicação Simplificada

JavaScript é a linguagem que faz as coisas "acontecerem" no site. No BlockForge, não estamos apenas mostrando texto ou fotos; estamos calculando posições no espaço 3D, salvando o estado de centenas de blocos e tratando eventos de clique, arraste e scroll do mouse.

## Por que isso existe?

O JavaScript moderno (ES6+) foi criado para que pudéssemos escrever aplicativos grandes e complexos sem códigos bagunçados. Com ferramentas como o **Spread Operator**, podemos dizer ao React "aqui está uma nova lista de blocos" sem bagunçar a lista antiga que está sendo mostrada na tela no momento.

## Exemplos de uso no BlockForge

No arquivo `src/store/blockStore.js`, usamos o **Math.abs** e **some** para saber se tem um bloco "perto" do mouse:

```javascript
const blockExists = blocks.some(b => 
  Math.abs(b.position[0] - pos[0]) < 0.1 && 
  Math.abs(b.position[1] - pos[1]) < 0.1 && 
  Math.abs(b.position[2] - pos[2]) < 0.1
);
```

E usamos **uuidv4** para cada bloco ter seu próprio "CPF":

```javascript
import { v4 as uuidv4 } from 'uuid';

const newBlock = { id: uuidv4(), position, type }; 
// Exemplo de ID: e9b4-f3a2-11ef-9800...
```

## Problemas Comuns

- **Referência:** Se você fizer `const b = blocks`, mudar `b` vai mudar `blocks` também. **Sempre use `[...blocks]`**.
- **`null` ou `undefined`:** No 3D, se uma posição vier vazia, o JavaScript vai dar erro. **Sempre use valores padrão.**

## Boas Práticas

- **Funções Puras:** Divirta-se criando funções que apenas fazem o cálculo (ex: `isInsideWorld`) e retornam true/false. Elas são mais fáceis de testar!
- **Nomeação:** Dê nomes claros: `addBlock` em vez de `addB`. No futuro, você agradecerá.
