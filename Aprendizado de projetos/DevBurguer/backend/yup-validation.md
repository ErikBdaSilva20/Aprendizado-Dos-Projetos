# ✅ Yup (Validation - Backend)

O **Yup** é o nosso "Controlador de Qualidade". Ele garante que os dados enviados pelo cliente no carrinho ou pelo administrador no produto não estejam incompletos ou errados antes de chegarmos no banco de dados.

## 🧠 Conceitos Principais

- **Schema Validation**: Uma regra que diz: "O nome deve ser um texto, o preço um número, e o e-mail deve ter um `@`".
- **Cast**: Tentar converter um dado (ex: um texto "10" para um número `10`).
- **Synchronous / Asynchronous Validation**: Validar os dados de uma vez ou esperar por uma resposta (ex: verificar se o e-mail já existe).
- **Tratamento de Erros**: O que responder ao cliente se ele esquecer a senha? (Ex: "Campo obrigatório").

---

## 💡 Explicação Simplificada

O **Yup** é o **Filtro da Torneira**.
1.  O cliente envia um formulário de cadastro.
2.  Antes da água (dados) chegar no copo (Banco de Dados), ela passa pelo **Filtro (Yup)**.
3.  Se a água vier com areia (senha curta, e-mail sem `@`), o filtro trava tudo e joga a sujeira fora (Retorna erro 400).

---

## ❓ Por que usar o Yup?

- **Previsibilidade**: O banco de dados nunca vai receber um "lixo" que causaria um erro fatal.
- **Mensagens Amigáveis**: Você pode personalizar o que o sistema responde (ex: "Senha deve ter no mínimo 6 caracteres!").
- **Facilidade**: É muito mais limpo do que escrever 20 `if` e `else` para conferir cada campo um por um.

---

## 🛠️ Exemplos de Uso no Projeto

### Criando um Schema de Usuário (`src/app/controllers/userController.js`)

Aqui definimos as regras do jogo:
```js
import * as Yup from 'yup';

class UserController {
  async store(request, response) {
    const schema = Yup.object().shape({
      name: Yup.string().required('O nome é obrigatório!'),
      email: Yup.string().email('E-mail inválido!').required(),
      password: Yup.string().required().min(6, 'Mínimo de 6 caracteres!'),
      admin: Yup.boolean(),
    });

    try {
      await schema.validateSync(request.body, { abortEarly: false });
    } catch (err) {
      return response.status(400).json({ errors: err.errors });
    }

    // Se passou aqui, os dados estão limpos!
  }
}
```

---

## ✅ Desafios para Estudantes

1.  **O que o `abortEarly: false` faz?** Tente tirar essa linha do código e envie um formulário com 3 erros. O sistema avisará os 3 erros de uma vez ou só o primeiro?
2.  **Validando Produtos**: Olhe o `productController.js`. Quais são os campos obrigatórios para criar um Burguer? (Dica: procure por `.required()`).

---

## 📚 Links Úteis
- [Documentação Oficial do Yup (GitHub)](https://github.com/jquense/yup)
- [O que é Schema Validation?](https://www.freecodecamp.org/news/schema-validation-padrão-de-projeto-para-validar-objetos-em-javascript-92896f6a73c0/)
- [Boas Práticas de Validação de Dados na API](https://blog.geekhunter.com.br/validacao-de-dados-emt-api-rest/)
