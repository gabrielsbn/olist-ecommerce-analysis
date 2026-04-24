# Métricas de Logística — Medidas DAX

## Visão Geral
Medidas DAX utilizadas na página de Eficiência Operacional e Desempenho de Vendedores. Todas as medidas estão armazenadas na tabela `_Medidas` no modelo Power BI.

---

## Entrega no Prazo %
Percentual de pedidos entregues até a data estimada.

```dax
Entrega no Prazo % =
VAR EntregasNoPrazo =
    CALCULATE(
        COUNTROWS(orders),
        orders[order_delivered_customer_date] <=
        orders[order_estimated_delivery_date]
    )
RETURN
DIVIDE(EntregasNoPrazo, [Volume Pedidos], 0)
```

---

## Atraso Médio em Dias
Média de dias de atraso considerando apenas pedidos atrasados. Calculada a partir da tabela `order_reviews` para respeitar o contexto de filtro do `review_score`.

```dax
Atraso Medio Dias =
VAR TabelaCruzada =
    CALCULATETABLE(
        ADDCOLUMNS(
            order_reviews,
            "Atraso",
            RELATED(orders[order_delivered_customer_date]) -
            RELATED(orders[order_estimated_delivery_date])
        )
    )
VAR SomenteAtrasados =
    FILTER(
        TabelaCruzada,
        [Atraso] > 0
    )
RETURN
AVERAGEX(
    SomenteAtrasados,
    [Atraso]
)
```

---

## Tempo Médio de Entrega Total
Média de dias entre a compra e a entrega ao cliente. Considera apenas pedidos com status "delivered".

```dax
Tempo Entrega Total Dias =
CALCULATE(
    AVERAGEX(
        orders,
        DATEDIFF(
            orders[order_purchase_timestamp],
            orders[order_delivered_customer_date],
            DAY
        )
    ),
    NOT ISBLANK(orders[order_delivered_customer_date]),
    orders[order_status] = "delivered"
)
```

---

## Tempo Médio de Aprovação
Média de dias entre a compra e a aprovação do pagamento. Responsabilidade da plataforma.

```dax
Tempo Aprovacao Dias =
AVERAGEX(
    FILTER(
        orders,
        NOT ISBLANK(orders[order_approved_at])
    ),
    DATEDIFF(
        orders[order_purchase_timestamp],
        orders[order_approved_at],
        DAY
    )
)
```

---

## Tempo Médio de Postagem
Média de dias entre a aprovação e a entrega à transportadora. Responsabilidade do vendedor.

```dax
Tempo Postagem Dias =
AVERAGEX(
    FILTER(
        orders,
        NOT ISBLANK(orders[order_delivered_carrier_date])
            && NOT ISBLANK(orders[order_approved_at])
    ),
    DATEDIFF(
        orders[order_approved_at],
        orders[order_delivered_carrier_date],
        DAY
    )
)
```

---

## Tempo Médio de Trânsito
Média de dias entre a coleta pela transportadora e a entrega ao cliente. Responsabilidade da transportadora.

```dax
Tempo Transito Dias =
AVERAGEX(
    FILTER(
        orders,
        NOT ISBLANK(orders[order_delivered_customer_date])
            && NOT ISBLANK(orders[order_delivered_carrier_date])
    ),
    DATEDIFF(
        orders[order_delivered_carrier_date],
        orders[order_delivered_customer_date],
        DAY
    )
)
```

---

## Tabela de Decomposição do SLA
Tabela calculada utilizada no visual de decomposição do tempo de entrega por etapa e responsável.

```dax
Decomposicao_SLA =
DATATABLE(
    "Etapa", STRING,
    "Responsavel", STRING,
    "Ordem", INTEGER,
    {
        { "Aprovação", "Plataforma", 1 },
        { "Postagem", "Vendedor", 2 },
        { "Trânsito", "Transportadora", 3 }
    }
)
```

---

## Tempo por Etapa SLA
Medida dinâmica que retorna o valor correto para cada etapa da decomposição do SLA conforme o contexto de filtro.

```dax
Tempo Etapa SLA =
SWITCH(
    SELECTEDVALUE(Decomposicao_SLA[Etapa]),
    "Aprovação", [Tempo Aprovacao Dias],
    "Postagem",  [Tempo Postagem Dias],
    "Trânsito",  [Tempo Transito Dias]
)
```
