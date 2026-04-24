# Métricas Comerciais — Medidas DAX

## Visão Geral
Medidas DAX utilizadas na página de Desempenho Comercial — Visão Geral.
Todas as medidas estão armazenadas na tabela `_Medidas` no modelo Power BI.

---

## GMV Total
Soma do valor de todos os itens vendidos, excluindo frete.

```dax
GMV Total =
SUMX(
    order_items,
    order_items[price]
)
```

---

## Receita de Frete
Soma do valor de frete de todos os itens.

```dax
Receita Frete =
SUMX(
    order_items,
    order_items[freight_value]
)
```

---

## Receita Total
GMV somado à receita de frete — total movimentado pelo ecossistema.

```dax
Receita Total =
[GMV Total] + [Receita Frete]
```

---

## Volume de Pedidos
Contagem distinta de pedidos — evita duplicação por múltiplos itens.

```dax
Volume Pedidos =
DISTINCTCOUNT(orders[order_id])
```

---

## Total de Clientes Únicos
Contagem de clientes reais — usa `customer_unique_id` e não `customer_id`.

```dax
Total Clientes =
DISTINCTCOUNT(customers[customer_unique_id])
```

---

## Ticket Médio
Receita média por pedido.

```dax
Ticket Medio =
DIVIDE(
    [GMV Total],
    [Volume Pedidos],
    0
)
```

---

## Frete % GMV
Percentual do frete em relação ao GMV — indicador de eficiência logística.

```dax
Frete % GMV =
DIVIDE(
    [Receita Frete],
    [GMV Total],
    0
)
```

---

## Frete Médio por Pedido
Valor médio de frete por item vendido.

```dax
Frete Medio Pedido =
AVERAGEX(
    order_items,
    order_items[freight_value]
)
```

---

## Parcelamento Médio
Número médio de parcelas por transação.

```dax
Parcelamento Medio =
AVERAGEX(
    order_payments,
    order_payments[payment_installments]
)
```

---

## GMV Top Categoria
Valor de GMV da categoria com maior faturamento.

```dax
GMV Top Categoria =
MAXX(
    ALL(products[product_category_name_english]),
    [GMV Total]
)
```

---

## Frete % Preço por Categoria
Percentual do frete em relação ao preço do produto por categoria.
Exclui itens com preço zero para evitar distorção.

```dax
Frete % Preco Categoria =
DIVIDE(
    SUMX(
        FILTER(order_items, order_items[price] > 0),
        order_items[freight_value]
    ),
    SUMX(
        FILTER(order_items, order_items[price] > 0),
        order_items[price]
    ),
    0
)
```

---

## GMV com Tratamento de Branco
Versão do GMV que retorna zero em vez de branco para meses sem pedidos.
Necessária para manter continuidade da linha no gráfico temporal.

```dax
GMV Total =
VAR Resultado = SUMX(order_items, order_items[price])
RETURN
IF(ISBLANK(Resultado), 0, Resultado)
```
