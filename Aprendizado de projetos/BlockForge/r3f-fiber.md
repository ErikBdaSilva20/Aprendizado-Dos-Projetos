# ⚛️ React Three Fiber (R3F)

## Conceitos Principais

- **Canvas:** O componente principal que cria todo o ambiente 3D.
- **Declarative Hooks:** Usar Three.js como se fossem tags HTML (`<mesh />`, `<boxGeometry />`).
- **`useFrame`:** O "coração" das animações.
- **`useThree`:** Acesso direto ao estado global do Three.js dentro do React.

## Explicação Simplificada

O Three.js sozinho é "imperativo" (você ordena cada passo: crie isso, agora adicione aqui). O **React Three Fiber** (ou R3F) é do tipo "declarativo": você apenas descreve o que quer que exista na tela como se fosse uma árvore de componentes React, e ele se encarrega de sincronizar tudo.

Ele transforma as classes do Three.js em tags minúsculas no React. Por exemplo, `new THREE.Mesh()` vira `<mesh />`.

## Por que isso existe?

- Para que você possa usar o poder do Three.js com a facilidade do ecossistema React.
- Facilita a criação de mundos dinâmicos que mudam conforme o estado (`useState`, `useStore`) do app.

## Exemplos de uso no BlockForge

No arquivo `src/components/scene/Lights/index.jsx`, declaramos as luzes do mundo assim:

```jsx
export default function Lights() {
  return (
    <>
      <ambientLight intensity={0.6} />
      <directionalLight position={[10, 20, 10]} intensity={1} />
    </>
  );
}
```

E no `Scene/index.jsx`, criamos o mundo inteiro dentro da tag `<Canvas>`:

```jsx
<Canvas 
  camera={{ position: [12, 12, 12], fov: 50 }}
>
  <SceneContent />
</Canvas>
```

## Problemas Comuns

- **Loops Infinitos:** Atualizar o estado do React dentro do renderizador 3D pode travar sua aplicação se não for feito com cuidado.
- **Hierarquia:** Nem tudo no R3F é renderizado como DOM. Ele vive no seu próprio mundo (fora do HTML comum).

## Boas Práticas

- Sempre use **@react-three/drei**: Uma biblioteca auxiliar com componentes prontos (como `OrbitControls`) que facilitam 90% do trabalho duro.
