# 📝 React Hook Form & Yup (Forms)

O **React Hook Form** é a nossa "Caneta Inteligente" para preencher formulários, e o **Yup** é o nosso "Professor de Gramática" que corrige se escrevemos algo errado na ficha. Eles trabalham juntos para tornar o Login e o Cadastro de produtos super fáceis e sem erros chatos no navegador.

## 🧠 Conceitos Principais

- **Register**: Uma função que "conecta" o campo de texto `<input />` ao React Hook Form.
- **HandleSubmit**: O que acontece quando o botão "Enviar" é apertado. O formulário só envia se tudo estiver OK!
- **Resolvers**: O "trutor" que faz o Yup falar a mesma língua que o React Hook Form (`@hookform/resolvers/yup`).
- **Form State**: Como o React sabe se o botão de "Enviar" deve estar cinza enquanto o usuário digita algo errado?
- **Validation Schema**: As regras (ex: "E-mail precisa ser @", "Senha precisa de 6 letras").

---

## 💡 Explicação Simplificada

O **React Hook Form** é o **Secretário do Cartório**.
1.  Você começa a preencher o formulário.
2.  Cada letra que você digita, o secretário (React Hook Form) anota, mas não fica gritando pro "Chefe" (Página) o tempo todo — ele só avisa se for importante.
3.  Quando você acaba, o secretário chama o **Yup (Corretor)** para conferir a ortografia.
4.  Só se o Corretor assinar embaixo, o Secretário leva os dados pro "Chefe" (Backend).

---

## ❓ Por que usar essa combinação?

- **Performance**: O React Hook Form não faz a sua tela ficar "travando" enquanto você digita no formulário.
- **Fácil Tratamento de Erros**: Mostrar "Campo obrigatório!" embaixo do input é super fácil com o `errors.fieldName`.
- **Padronização**: Todos os formulários (Login, Cadastro, Admin) seguem a mesma lógica, facilitando o aprendizado.

---

## 🛠️ Exemplos de Uso no Projeto

### Criando o Schema e o Form (`src/pages/Login/index.jsx`)

Configurando o formulário em segundos:
```jsx
const schema = yup.object().shape({
  email: yup.string().email('Digite um e-mail válido!').required('E-mail é obrigatório!'),
  password: yup.string().min(6, 'Senha deve ter no mínimo 6 letras!').required(),
});

const { register, handleSubmit, formState: { errors } } = useForm({
  resolver: yupResolver(schema),
});

const onSubmit = (data) => {
  console.log("Formulário limpinho e pronto para o backend!", data);
};
```

### O HTML do Formulário Estilizado

Mostrando erros na tela:
```jsx
<form onSubmit={handleSubmit(onSubmit)}>
  <input {...register('email')} />
  <p className="error">{errors.email?.message}</p>
  
  <input type="password" {...register('password')} />
  <p className="error">{errors.password?.message}</p>
  
  <button type="submit">Entrar</button>
</form>
```

---

## ✅ Desafios para Estudantes

1.  **Onde mudar a mensagem de erro?** Procure no `pages/Register/index.jsx`. Troque a mensagem de "Este campo é obrigatório!" por algo mais engraçado e teste no site.
2.  **O que o `...register('campo')` está fazendo?** (Dica: ele é um atalho que passa várias props de uma vez só!).

---

## 📚 Links Úteis
- [Documentação Oficial do React Hook Form](https://react-hook-form.com/)
- [Documentação Oficial do Yup (GitHub)](https://github.com/jquense/yup)
- [Guia de Validação com React Hook Form e Yup](https://www.alura.com.br/artigos/react-hook-form-yup-validacao-de-formularios)
