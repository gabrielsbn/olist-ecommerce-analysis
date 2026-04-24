# Métricas de Clientes — Medidas DAX

## Visão Geral
Medidas DAX utilizadas na página de Satisfação e Retenção de Clientes. Todas as medidas estão armazenadas na tabela `_Medidas` no modelo Power BI.

---

## NPS Adaptado
Índice de satisfação adaptado do NPS clássico para a escala de review score de 1 a 5. Declarado explicitamente como adaptação metodológica em todos os visuais.

**Critério de classificação:**
- Promotores → nota 5
- Neutros → nota 4
- Detratores → notas 1, 2 e 3

```dax
NPS Adaptado =
VAR Promotores =
    DIVIDE(
        CALCULATE(
            COUNTROWS(order_reviews),
            order_reviews[review_score] = 5
        ),
        COUNTROWS(order_reviews),
        0
    )
VAR Detratores =
    DIVIDE(
        CALCULATE(
            COUNTROWS(order_reviews),
            order_reviews[review_score] <= 3
        ),
        COUNTROWS(order_reviews),
        0
    )
RETURN
    (Promotores - Detratores) * 100
```

---

## Taxa de Recompra %
Percentual de clientes únicos que realizaram mais de uma compra no período analisado.

```dax
Taxa Recompra % =
VAR ClientesQueVoltaram =
    COUNTROWS(
        FILTER(
            SUMMARIZE(
                customers,
                customers[customer_unique_id],
                "TotalPedidos", CALCULATE(COUNTROWS(orders))
            ),
            [TotalPedidos] > 1
        )
    )
VAR TotalClientesUnicos =
    DISTINCTCOUNT(customers[customer_unique_id])
RETURN
    DIVIDE(ClientesQueVoltaram, TotalClientesUnicos, 0)
```

---

## % Pedidos sem Review
Percentual de pedidos entregues sem avaliação registrada.

```dax
% Pedidos sem Review =
VAR PedidosComReview =
    DISTINCTCOUNT(order_reviews[order_id])
VAR TotalPedidos =
    DISTINCTCOUNT(orders[order_id])
VAR PedidosSemReview =
    TotalPedidos - PedidosComReview
RETURN
    DIVIDE(PedidosSemReview, TotalPedidos, 0)
```

---

## Nota Média Geral
Média geral do review score considerando todos os pedidos avaliados.

```dax
Nota Media Geral =
AVERAGE(order_reviews[review_score])
```

---

## Nota Média por Categoria
Média do review score filtrada pelo contexto de categoria de produto. Utilizada nos visuais de satisfação por categoria.

```dax
Nota Media Categoria =
AVERAGEX(
    order_reviews,
    order_reviews[review_score]
)
```

---

## Nota Média por Estado
Média do review score filtrada pelo contexto de estado do cliente. Utilizada nos visuais de satisfação geográfica.

```dax
Nota Media Estado =
AVERAGEX(
    order_reviews,
    order_reviews[review_score]
)
```

---

## Tempo Médio para Deixar Review
Média de dias entre a entrega do pedido e a resposta do cliente à pesquisa de satisfação.

```dax
Tempo Review Dias =
AVERAGEX(
    FILTER(
        order_reviews,
        NOT ISBLANK(order_reviews[review_answer_timestamp])
            && NOT ISBLANK(RELATED(orders[order_delivered_customer_date]))
    ),
    DATEDIFF(
        RELATED(orders[order_delivered_customer_date]),
        order_reviews[review_answer_timestamp],
        DAY
    )
)
```

---

## GMV % do Total
Percentual de participação no GMV total — utilizado na análise de concentração geográfica e por categoria.

```dax
GMV % do Total =
DIVIDE(
    [GMV Total],
    CALCULATE([GMV Total], ALL(customers)),
    0
)
```

---

## Total de Vendedores Ativos
Contagem distinta de vendedores com pelo menos um pedido no período analisado.

```dax
Total Vendedores =
DISTINCTCOUNT(olist_sellers_dataset[seller_id])
```

---

## Concentração Top 10 Vendedores %
Percentual do GMV total concentrado nos 10 maiores vendedores. Indicador de risco de dependência do ecossistema.

```dax
GMV Top 10 Vendedores % =
VAR Top10GMV =
    CALCULATE(
        [GMV Total],
        TOPN(
            10,
            ALL(olist_sellers_dataset[seller_id]),
            [GMV Total],
            DESC
        )
    )
RETURN
    DIVIDE(
        Top10GMV,
        CALCULATE([GMV Total], ALL(olist_sellers_dataset)),
        0
    )
```
