# 🚀 Trilha de Aprendizado: Zyntra (BlockForge 3D)

Bem-vindo ao seu guia completo de estudos para o projeto **BlockForge 3D**. Esta pasta foi organizada para transformar este repositório em sua sala de aula durante esta semana de estudos intensivos.

## 🧭 Como Navegar
Cada arquivo abaixo foca em uma tecnologia específica utilizada no projeto. A recomendação é seguir a ordem sugerida para construir uma base sólida antes de avançar para os conceitos de 3D.

### 1. Fundamentos
- [**JavaScript Moderno**](./javascript.md): O motor por trás de toda a lógica.
- [**React (v19)**](./react.md): A biblioteca que organiza nossa interface e componentes.

### 2. Estilização e Estado
- [**Styled Components**](./styled-components.md): Como estilizamos nossa aplicação de forma dinâmica.
- [**Zustand**](./zustand.md): Onde guardamos a "memória" do nosso mundo (blocos, seleções, pincel).

### 3. O Mundo 3D (O Coração do Projeto)
- [**Three.js**](./three-js.md): A biblioteca base para renderização 3D no navegador.
- [**React Three Fiber (@rtf)**](./r3f-fiber.md): Como usamos Three.js "dentro" do React de forma declarativa.

---

## 📅 Sugestão de Cronograma (7 Dias)

| Dia | Foco | Arquivo(s) |
| :--- | :--- | :--- |
| **Segunda** | Fundamentos e Ferramenta | `javascript.md` + Entender `package.json` |
| **Terça** | Componentização | `react.md` + `styled-components.md` |
| **Quarta** | Gerenciamento de Estado | `zustand.md` (Estudar `blockStore.js`) |
| **Quinta** | Introdução ao 3D | `three-js.md` (Conceitos de Cenas, Câmeras) |
| **Sexta** | 3D no React | `r3f-fiber.md` (Cenas e Malhas/Meshes) |
| **Sábado** | Lógica de Voxel | Analisar `src/components/scene/` |
| **Domingo** | Revisão e Prática | Tentar criar um novo tipo de bloco ou atalho |

---

> [!TIP]
> **Dica do Professor:** Não tente decorar tudo. O segredo está em entender o **porquê** de cada ferramenta existir. Sempre que ler um conceito, procure onde ele está sendo usado no código do `src/`.
