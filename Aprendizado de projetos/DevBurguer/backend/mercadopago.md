# 💳 Mercado Pago (Integration)

O **Mercado Pago** é a nossa "maquininha de cartão virtual". O projeto se comunica com a API do Mercado Pago para gerar **Preferências de Pagamento**. Isso permite que o cliente pague por PIX ou Cartão dentro de uma página oficial segura.

## 🧠 Conceitos Principais

- **Preferência de Pagamento**: Um objeto que enviamos ao Mercado Pago dizendo: "O cliente João quer pagar R$ 50,00 por esses lanches".
- **Access Token**: Uma chave secreta que identifica que o dinheiro deve ir para a conta do DevBurguer.
- **Payer**: Quem paga (os dados do cliente).
- **Items**: Os lanches que o cliente colocou no carrinho, com seus preços unitários.
- **Back URLs**: Para onde o cliente vai *depois* que o pagamento terminar (ex: `/complete` no FrontEnd).
- **External Reference**: Um ID que enviamos para o Mercado Pago e eles nos devolvem, para sabermos a que pedido aquele pagamento se refere.

---

## 💡 Explicação Simplificada

O **Mercado Pago** é o **Caixa do Shopping**.
1.  O cliente termina de escolher os lanches e clica em "Finalizar Pedido".
2.  O DevBurguer (Backend) envia uma **Comanda** (Preferência) ao Shopping (Mercado Pago).
3.  O Shopping manda pro cliente uma **Fatura** (Página de Checkout) para ele pagar com PIX ou Cartão.
4.  Depois que ele paga, o Shopping avisa: "Ei, lanchonete! O cliente João pagou. Pode preparar o lanche!" (Isso acontece via Webhooks ou redirecionamento).

---

## ❓ Por que usamos o Mercado Pago?

- **Confiança**: Todo mundo conhece o Mercado Livre/Mercado Pago no Brasil.
- **Facilidade**: Oferece PIX, Cartão de Crédito e Boleto de uma só vez.
- **Segurança**: Nós não tocamos nos dados do cartão do cliente! O Mercado Pago cuida de tudo.

---

## 🛠️ Exemplos de Uso no Projeto

### Gerando a Preferência (`src/app/controllers/mercadopago/CreatePaymentPreferenceController.js`)

Onde construímos o pedido de pagamento:
```js
const body = {
  items: products.map((product) => ({
    id: String(product.id),
    title: product.name,
    quantity: product.quantity,
    unit_price: Number(product.price), // Valor em reais
  })),
  back_urls: {
    success: 'http://localhost:5173/complete',
    failure: 'http://localhost:5173/cart',
    pending: 'http://localhost:5173/complete',
  },
  auto_return: 'approved',
};

const result = await preference.create({ body });
return response.json(result.init_point); // URL para o cliente pagar!
```

---

## ✅ Desafios para Estudantes

1.  **Onde o token do Mercado Pago está guardado?** Procure no `.env` do backend pela variável `MERCADOPAGO_ACCESS_TOKEN`.
2.  **O que o FrontEnd faz com o `init_point`?** O backend devolve uma URL. O FrontEnd usa um `window.location.href = init_point` para levar o cliente até lá. Procure esse fluxo na pasta `FrontEnd/src/pages/Checkout/index.jsx`.

---

## 📚 Links Úteis
- [Documentação Mercado Pago Developers (Node.js SDK)](https://www.mercadopago.com.br/developers/pt/docs/sdks-library/server-side/nodejs)
- [O que é uma Preferência de Pagamento?](https://www.mercadopago.com.br/developers/pt/docs/checkout-pro/checkout-customization/preference-settings)
- [Diferença entre Checkout Pro e Checkout Transparente](https://www.mercadopago.com.br/developers/pt/docs/checkout-pro/landing)
