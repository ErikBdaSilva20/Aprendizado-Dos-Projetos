# 🧊 Three.js: O Motor 3D

## Conceitos Principais

- **Cena (Scene):** O universo onde tudo acontece.
- **Câmera (Camera):** O ponto de vista do usuário.
- **Renderizador (Renderer):** O que desenha os pixels na tela (Canvas).
- **Malha (Mesh):** Um objeto visível (Geometria + Material).
- **Vetores (Vectors):** Coordenadas [x, y, z] no espaço.

## Explicação Simplificada

Imagine que você está dirigindo um filme. A **Cena** é o seu set de gravação. A **Câmera** é por onde o público vê o filme. O **Mesh** (como um bloco de grama) é um ator no palco. O Three.js é a tecnologia que permite que o navegador use a placa de vídeo (GPU) para desenhar esses objetos em 3D de forma super rápida.

## Por que isso existe?

O navegador, por padrão, só entende 2D (texto, imagens). Para desenhar algo em 3D como o BlockForge, precisaríamos de uma linguagem muito complexa chamada WebGL. O **Three.js** simplifica essa linguagem, permitindo que criemos mundos 3D usando JavaScript de forma intuitiva.

## Exemplos de uso no BlockForge

No arquivo `src/components/scene/Scene/index.jsx`, usamos constantes do Three.js para controlar a câmera:

```javascript
import * as THREE from 'three';

// Usado para configurar os botões do mouse na câmera
mouseButtons={{
  LEFT: THREE.MOUSE.ROTATE,    // Girar
  MIDDLE: THREE.MOUSE.PAN,     // Mover lateralmente
  RIGHT: THREE.MOUSE.ROTATE,   // Também girar
}}
```

## Problemas Comuns

- **Performance:** Colocar milhares de objetos individuais pode travar o site. (No futuro, usamos técnicas como "Instancing").
- **Coordenadas:** No BlockForge, o **Y** é a altura, enquanto **X** e **Z** são o chão. É fácil se confundir no começo!

## Boas Práticas

1. **Reuso:** Reutilize geometrias e materiais sempre que possível para economizar memória.
2. **Eixos:** Sempre lembre que [0, 0, 0] é o centro do mundo.
