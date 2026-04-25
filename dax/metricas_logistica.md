# Métricas de Logística — Medidas DAX

## Visão Geral
Medidas DAX utilizadas na página de Eficiência Operacional e Desempenho de Vendedores. Todas as medidas estão armazenadas na tabela `_Medidas` no modelo Power BI.

---

## Entrega no Prazo %
Percentual de pedidos entregues até a data estimada.

```dax
ENTREGA_PRAZO % = 
                  VAR ENTREGAS_NO_PRAZO = 
                                        CALCULATE(
                                                    COUNTROWS(olist_orders_dataset),
                                                    olist_orders_dataset[order_delivered_customer_date] <= olist_orders_dataset[order_estimated_delivery_date]
                                        )
                                        RETURN
                                        DIVIDE(ENTREGAS_NO_PRAZO, [VOLUME_PEDIDOS], 0)
```

---

## Atraso Médio em Dias
Média de dias de atraso considerando apenas pedidos atrasados. Calculada a partir da tabela `order_reviews` para respeitar o contexto de filtro do `review_score`.

```dax
ATRASO_MEDIO_DIAS = 
VAR TabelaCruzada =
    CALCULATETABLE(
        ADDCOLUMNS(
            olist_order_reviews_dataset,
            "Atraso",
            RELATED(olist_orders_dataset[order_delivered_customer_date]) -
            RELATED(olist_orders_dataset[order_estimated_delivery_date])
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
TEMPO_ENTREGA_TOTAL_DIAS = 
                            CALCULATE(
                                        AVERAGEX(
                                                olist_orders_dataset,
                                                DATEDIFF(olist_orders_dataset[order_purchase_timestamp],
                                                         olist_orders_dataset[order_delivered_customer_date],
                                                         DAY)
                                        ),
                                        NOT ISBLANK(olist_orders_dataset[order_delivered_customer_date]),
                                        olist_orders_dataset[order_status] = "delivered"
                            )
```

---

## Tempo Médio de Aprovação
Média de dias entre a compra e a aprovação do pagamento. Responsabilidade da plataforma.

```dax
TEMPO_APROVACAO_DIAS = 
                        AVERAGEX(
                                    FILTER(
                                            olist_orders_dataset,
                                            NOT ISBLANK(olist_orders_dataset[order_approved_at])
                                    ),
                                    DATEDIFF(
                                             olist_orders_dataset[order_purchase_timestamp],
                                             olist_orders_dataset[order_approved_at],
                                             DAY)
                        )
```

---

## Tempo Médio de Postagem
Média de dias entre a aprovação e a entrega à transportadora. Responsabilidade do vendedor.

```dax
TEMPO_POSTAGEM_DIAS = 
                        AVERAGEX(
                                 FILTER(
                                        olist_orders_dataset,
                                        NOT ISBLANK(olist_orders_dataset[order_delivered_carrier_date])
                                                    && NOT ISBLANK(olist_orders_dataset[order_approved_at])
                                 ),
                                 DATEDIFF(
                                            olist_orders_dataset[order_approved_at],
                                            olist_orders_dataset[order_delivered_carrier_date],
                                            DAY
                                 )
                        )
```

---

## Tempo Médio de Trânsito
Média de dias entre a coleta pela transportadora e a entrega ao cliente. Responsabilidade da transportadora.

```dax
TEMPO_TRANSITO_DIAS = 
                        AVERAGEX(
                                 FILTER(
                                        olist_orders_dataset,
                                        NOT ISBLANK(olist_orders_dataset[order_delivered_customer_date])
                                                    && NOT ISBLANK(olist_orders_dataset[order_delivered_carrier_date])
                                 ),
                                 DATEDIFF(
                                          olist_orders_dataset[order_delivered_carrier_date],
                                          olist_orders_dataset[order_delivered_customer_date],
                                          DAY
                                 )
                        )
```

---

## Tempo por Etapa SLA
Medida dinâmica que retorna o valor correto para cada etapa da decomposição do SLA conforme o contexto de filtro.

```dax
TEMPO_ETAPA_SLA = 
                SWITCH(
                        SELECTEDVALUE(decomposicao_SLA[Etapa]),
                                      "Aprovação", [TEMPO_APROVACAO_DIAS],
                                      "Postagem", [TEMPO_POSTAGEM_DIAS],
                                      "Trânsito", [TEMPO_TRANSITO_DIAS]
                        )
```
