# 📊 Recharts (Charts - Admin Dashboard)

O **Recharts** é a nossa "Ferramenta de Dashboards". No Painel Administrativo, ele desenha aqueles gráficos bonitos de pizza e barras para o dono da lanchonete ver quanto dinheiro o DevBurguer está rendendo por dia e quais são os lanches favoritos dos clientes.

## 🧠 Conceitos Principais

- **Charts**: Gráficos como Pizza (PieChart), Barra (BarChart) ou Linha (LineChart).
- **ResponsiveContainer**: Um truque que faz o gráfico aumentar e diminuir sozinho se você mexer no tamanho da janela (celular vs computador).
- **Tooltip**: Aquela janelinha preta que mostra os detalhes quando você passa o mouse por cima de uma barra do gráfico.
- **XAxis / YAxis**: Os "eixos" do gráfico (data embaixo, valor do lado).
- **Data Map**: Como a lista de pedidos que buscamos no banco (JSON) se transforma num gráfico colorido.

---

## 💡 Explicação Simplificada

O **Recharts** é o o **Bloco de Anotações do Dono**.
1.  O Backend envia uma lista de 100 pedidos com seus valores.
2.  O **Recharts** pega cada número e desenha um pilar (barra).
3.  Quanto mais caro o pedido, mais alto fica o pilar.
4.  O dono bate o olho e já sabe: "Opa, no sábado vendemos 3 vezes mais do que na segunda-feira!".

---

## ❓ Por que usamos o Recharts?

- **Simplicidade**: Você escreve o gráfico como se fosse um componente React normal.
- **Interatividade**: Já vem com animações de entrada e efeitos ao passar o mouse.
- **Leveza**: Não pesa no navegador como outras bibliotecas de gráficos antigas do passado.

---

## 🛠️ Exemplos de Uso no Projeto

### Criando o Gráfico de Receita (`src/pages/Admin/Dashboard/index.jsx`)

Um exemplo de BarChart:
```jsx
import { BarChart, Bar, XAxis, YAxis, Tooltip, ResponsiveContainer } from 'recharts';

function Dashboard() {
  const data = [
    { name: 'Segunda', total: 400 },
    { name: 'Terça', total: 700 },
    { name: 'Quarta', total: 200 },
  ];

  return (
    <ResponsiveContainer width="100%" height={300}>
      <BarChart data={data}>
        <XAxis dataKey="name" stroke="#888888" />
        <YAxis stroke="#888888" />
        <Tooltip />
        <Bar dataKey="total" fill="#9758a6" radius={[4, 4, 0, 0]} />
      </BarChart>
    </ResponsiveContainer>
  );
}
```

---

## ✅ Desafios para Estudantes

1.  **Mude a cor do gráfico**: Procure o arquivo `src/pages/Admin/Dashboard/index.jsx`. Mude o `fill` da `Bar` para `red` (ou outra cor) e veja o dashboard no navegador.
2.  **O que o `dataKey` faz?** Como o gráfico sabe que o nome do dia deve ficar embaixo e o valor da venda deve ser a altura da barra? (Dica: olhe como o objeto `data` foi escrito).

---

## 📚 Links Úteis
- [Documentação Oficial do Recharts](https://recharts.org/en-us/guide)
- [Exemplos de Gráficos de Pizza](https://recharts.org/en-us/examples/PieChartWithCustomizedLabel)
- [Dicas de Design para Gráficos Usáveis](https://medium.com/design-pills/designing-effective-data-visualizations-6775f0a0e98c)
