# Arquitetura do Markdown Reader — Aprendizado Técnico (Este projeto)

Este documento detalha o funcionamento interno desta própria aplicação, seguindo a metodologia do `LearnAgent`. O objetivo é transformar a base de código em uma fonte de estudo para desenvolvedores.

## Conceitos Principais

- **GitHub REST API (Trees)**: Uso do endpoint `/git/trees/:sha?recursive=1` para obter toda a estrutura de arquivos em uma única chamada. Isso reduz latência e facilita o mapeamento de pastas.
- **Recursividade em UI**: A `Sidebar` utiliza componentes que chamam a si mesmos para renderizar pastas e subpastas de forma infinita.
- **Roteamento Dinâmico (Catch-all)**: Uso de `[...slug]` no Next.js para aceitar qualquer profundidade de URL (ex: `/learn/pasta/subpasta/arquivo.md`).
- **Contexto de Renderização**: Utilização de `react-markdown` com plugins (`remark-gfm`) para transformar o texto bruto (Raw) do GitHub em HTML estilizado.

## Explicação Simplificada

Imagine que o projeto é um "tradutor" que liga o GitHub (o servidor de arquivos) ao Navegador (o leitor).

1. Ele pergunta ao GitHub: "Quais arquivos você tem?".
2. O GitHub devolve uma lista plana.
3. O código transforma essa lista em uma "árvore" (como os arquivos no seu computador).
4. Quando você clica em um arquivo, ele busca o texto puro desse arquivo e o "desenha" na tela com cores bonitas e formatação.

## Por que isso existe

- **Qual problema isso resolve?** Ler documentações complexas diretamente no GitHub às vezes é cansativo pela falta de uma navegação lateral persistente e focada apenas no conteúdo.
- **Por que usar essa abordagem?** Utilizar a API de Trees (`recursive=1`) é muito superior a fazer várias chamadas individuais por pasta, pois economiza tempo de carregamento e limites da API (Rate Limit).

## Exemplos de Uso (Lógica da Árvore)

Aqui está como os dados brutos da API são transformados em uma estrutura aninhada:

```typescript
// Exemplo simplificado de normalização de árvore (src/services/githubApi.ts)
tree.forEach((item) => {
  const parts = item.path.split('/');
  let currentPath = '';

  for (let i = 0; i < parts.length - 1; i++) {
    // Constrói ou acessa os diretórios pais
    currentPath = currentPath ? `${currentPath}/${parts[i]}` : parts[i];
    if (!map.has(currentPath)) {
      const dirNode = { name: parts[i], type: 'dir', children: [] };
      map.set(currentPath, dirNode);
      // Adiciona ao pai ou à raiz
    }
  }
});
```

## Problemas Comuns

- **Rate Limit da API**: Sem um `GITHUB_TOKEN`, o GitHub bloqueia chamadas frequentes (apenas 60/hora). Por isso, o uso de variáveis de ambiente é crítico.
- **Caracteres Especiais em URLs**: Nomes de pastas com espaços ou acentos precisam ser tratados com `encodeURIComponent` para que o fetch do "Raw Content" não falhe.
- **Cache do Next.js**: Ao usar `force-dynamic`, garantimos que novas atualizações no GitHub apareçam imediatamente sem precisar de um novo "build" do projeto.

## Boas Práticas

- **Uso de `<details>` nativo**: Para a Sidebar, em vez de gerenciar vários estados de `isOpen`, utilizamos a tag HTML nativa `<details>`, que é mais leve e acessível.
- **Desacoplamento de CSS**: Uso de CSS Modules para garantir que os estilos do Markdown (que são globais no CSS original) não "vazem" e estraguem o resto da interface do site.
- **Tratamento de Erros**: Sempre verificar se a resposta da API é válida antes de tentar fazer o parse do JSON.

## Anotações Pessoais

- Estudar mais sobre `generateStaticParams` para gerar as páginas de documentação de forma estática (ISR) e melhorar ainda mais a velocidade.
- O uso de `react-syntax-highlighter` com o tema Dracula deu uma cara muito profissional ao projeto.

---

_Documentação gerada pelo LearnAgent para o projeto MarkdownReader._
