# 🟦 TypeScript: O JS com Superpoderes

Embora o seu projeto ainda use principalmente **JavaScript (JS)** puro (arquivos `.js` e `.jsx`), você deve ter visto o **TypeScript (TS)** nas discussões ou no `package.json`. Vamos entender por que ele é o futuro de quem quer trabalhar em grandes empresas!

## ❓ O que é o TypeScript?

É uma linguagem que se baseia no JavaScript, mas adiciona **Tipagem Estática**.

### Exemplos de Diferença:

**No JavaScript (Flexível):**
```js
let idade = 25;
idade = "Vinte e cinco"; // O JS deixa você mudar de número para texto sem avisar nada!
```

**No TypeScript (Rígido):**
```ts
let idade: number = 25;
idade = "Vinte e cinco"; // ❌ O TS dá ERRO logo de cara! "Não pode colocar texto em um número!"
```

---

## 🎯 Por que usar TypeScript?

1.  **Menos Bugs**: 15% dos bugs em JavaScript são resolvidos só por usar TypeScript, pois ele evita que você passe dados errados para o lugar errado.
2.  **Autocomplete Inteligente**: O seu VS Code te ajuda MUITO mais. Quando você digita `produto.`, o TS já te mostra na lista: `nome`, `link`, `imagem`. Você não precisa ficar indo e voltando entre os arquivos para lembrar o que tem lá dentro.
3.  **Refatoração Segura**: Quer mudar o nome de um campo? Se mudar em um lugar, o TypeScript aponta todos os outros lugares que "quebraram" por causa dessa mudança.

---

## 🛠️ Onde o TypeScript está no seu Projeto?

Dê uma olhada no seu `package.json`:
- `@types/react`
- `@types/react-dom`

Isso são os **tipos** das bibliotecas. Mesmo escrevendo em JS, o seu editor usa o TypeScript por trás dos panos para te dizer: "Ei, no `useState` você precisa passar um valor inicial!".

---

## 💡 Como Migrar no Futuro?

Se você quiser transformar um arquivo `.jsx` em `.tsx` (a versão do TypeScript para React):
- Você precisaria criar uma **Interface** para os seus dados:

```tsx
interface Produto {
  id: string;
  name: string;
  link: string;
  image: string;
}

function CardProduto({ produto }: { produto: Produto }) {
  return <h1>{produto.name}</h1>;
}
```

Agora, o React **SABE** que `produto` TEM que vir com esses 4 campos. Se você esquecer um, o código nem compila!

---

💡 *O TypeScript é chato no começo porque ele reclama de tudo. Mas depois que você se acostuma, você nunca mais quer voltar pro JavaScript puro!*
