# Tecnologias Utilizadas — Markdown Reader

Este guia detalha o ecossistema tecnológico do projeto sob a perspectiva do desenvolvedor.

## Conceitos Principais

- **Next.js 15+ (App Router)**: Framework React com foco em Server Components, melhorando a performance inicial e o SEO (embora este projeto seja uma ferramenta interna, o Next.js facilita o roteamento dinâmico).
- **TypeScript (Strict Mode)**: Garante que os dados da API do GitHub (que podem ser incertos) sejam tipados e validados, evitando erros em tempo de execução.
- **Tailwind CSS & CSS Modules**: Uma mistura poderosa. Tailwind é usado para o "layout" global e CSS Modules para componentes que precisam de isolamento total (Ex: Sidebar e Markdown).
- **GitHub API (Rest V3)**: API oficial do GitHub para acesso a dados públicos e privados.

## Explicação Simplificada

O projeto é construído como uma "Página da Web Moderna". 
- O **Next.js** é o motor que controla tudo. 
- O **TypeScript** é a lista de regras que as peças devem seguir. 
- O **Tailwind** é a tinta que usamos para pintar as paredes rapidamente. 
- O **React-Markdown** é a ferramenta que transforma texto chato em páginas bonitas com links e imagens.

## Por que isso existe

- **Qual problema isso resolve?** Next.js resolve a complexidade de criar rotas dinâmicas (`/learn/[...slug]`) sem precisar de bibliotecas de terceiros como `react-router`.
- **Por que usar TypeScript?** Como lidamos com dados aninhados (Recursive Trees), é muito fácil se perder nos objetos. O TS nos dá auto-complete e avisa se esquecermos de tratar um campo opcional.

## Exemplos de Uso (Configuração de Markdown)

```typescript
// Configuração do MarkdownViewer (src/components/markdown/MarkdownViewer.tsx)
import ReactMarkdown from 'react-markdown';
import remarkGfm from 'remark-gfm';

<ReactMarkdown 
  remarkPlugins={[remarkGfm]} // Suporte para tabelas e links do GitHub
  components={{
    code({ node, inline, className, children, ...props }) {
      // Aqui configuramos o realce de sintaxe
    }
  }}
>
  {content}
</ReactMarkdown>
```

## Problemas Comuns

- **Flicker (Piscada) na Sidebar**: Ao navegar entre páginas no Next.js, a sidebar pode "piscar" ou resetar o scroll se não for tratada como um Layout persistente. Por isso, ela está no `layout.tsx` do roteamento.
- **Hydration Errors**: Ocorrem quando o servidor gera um HTML e o cliente tenta "hidratar" com algo diferente (ex: usando `Math.random()` ou datas dinâmicas sem cuidado).

## Boas Práticas

- **Uso de Ícones Símbolos**: `lucide-react` é excelente por ser leve e fácil de customizar com CSS (stroke, stroke-width).
- **Framer Motion para Micro-animações**: Usado para suavizar o carregamento e as transições de expansão.

## Anotações Pessoais

- O aprendizado sobre `Remark-GFM` foi fundamental. Sem ele, as tabelas e checkboxes do Markdown não funcionavam adequadamente.

---
*Documentação gerada pelo LearnAgent.*
