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
NPS_ADAPTADO = 
                VAR PROMOTORES = 
                                DIVIDE(
                                        CALCULATE(
                                                  COUNTROWS(olist_order_reviews_dataset),
                                                  olist_order_reviews_dataset[review_score] = 5
                                        ),
                                        COUNTROWS(olist_order_reviews_dataset),
                                        0
                                )
                VAR DETRATORES = 
                                DIVIDE(
                                        CALCULATE(
                                            COUNTROWS(olist_order_reviews_dataset),
                                            olist_order_reviews_dataset[review_score] <= 3
                                        ),
                                        COUNTROWS(olist_order_reviews_dataset),
                                        0
                                )
                RETURN 
                      (PROMOTORES - DETRATORES) * 100
```

---

## Taxa de Recompra %
Percentual de clientes únicos que realizaram mais de uma compra no período analisado.

```dax
% TAXA_RECOMPRA = 
                  VAR Clientes_Recompraram = 
                                             COUNTROWS(
                                                        FILTER(
                                                               SUMMARIZE(
                                                                          olist_customers_dataset,
                                                                          olist_customers_dataset[customer_unique_id],
                                                                          "Total_Pedidos", CALCULATE(COUNTROWS(olist_orders_dataset))
                                                               ),
                                                               [Total_Pedidos] > 1
                                                        )
                                             )
                    VAR TOTAL_CLIENTES_UNICOS = 
                                                DISTINCTCOUNT(olist_customers_dataset[customer_unique_id])
                    RETURN 
                                                DIVIDE(Clientes_Recompraram, TOTAL_CLIENTES_UNICOS, 0)
```

---

## % Pedidos sem Review
Percentual de pedidos entregues sem avaliação registrada.

```dax
% PEDIDOS_SEM_REVIEW = 
                       VAR PEDIDOS_COM_REVIEW = 
                                                DISTINCTCOUNT(olist_order_reviews_dataset[order_id])
                        VAR TOTAL_PEDIDOS = 
                                            DISTINCTCOUNT(olist_orders_dataset[order_id])
                        VAR PEDIDOS_SEM_REVIEW = 
                                            TOTAL_PEDIDOS - PEDIDOS_COM_REVIEW
                        RETURN 
                              DIVIDE(PEDIDOS_SEM_REVIEW, TOTAL_PEDIDOS, 0)
```

## Nota Média por Categoria
Média do review score filtrada pelo contexto de categoria de produto. Utilizada nos visuais de satisfação por categoria.

```dax
NOTA_MEDIA_CATEGORIA = 
                        AVERAGEX(
                                 olist_order_reviews_dataset,
                                 olist_order_reviews_dataset[review_score]
                        )
```

---

## Nota Média por Estado
Média do review score filtrada pelo contexto de estado do cliente. Utilizada nos visuais de satisfação geográfica.

```dax
NOTA_MEDIA_ESTADO = 
                    AVERAGEX(
                              olist_order_reviews_dataset,
                              olist_order_reviews_dataset[review_score]
                    )
```

---

## Tempo Médio para Deixar Review
Média de dias entre a entrega do pedido e a resposta do cliente à pesquisa de satisfação.

```dax
TEMPO_REVIEW_DIAS = 
                    AVERAGEX(
                              FILTER(
                                      olist_order_reviews_dataset,
                                      NOT ISBLANK(olist_order_reviews_dataset[review_answer_timestamp])
                                           && NOT ISBLANK(RELATED(olist_orders_dataset[order_delivered_customer_date]))
                              ),
                              DATEDIFF(
                                       RELATED(olist_orders_dataset[order_delivered_customer_date]),
                                       olist_order_reviews_dataset[review_answer_timestamp],
                                       DAY
                              )
                    )
```

---

## GMV % do Total
Percentual de participação no GMV total — utilizado na análise de concentração geográfica e por categoria.

```dax
% GMV_DO_TOTAL = 
                DIVIDE(
                        [GMV_TOTAL],
                        CALCULATE(
                                    [GMV_TOTAL], ALL(olist_customers_dataset)),
                                    0
                )
```

---

## Total de Vendedores Ativos
Contagem distinta de vendedores com pelo menos um pedido no período analisado.

```dax
TOTAL_VENDEDORES = 
                   DISTINCTCOUNT(olist_sellers_dataset[seller_id])
```

---

## Concentração Top 10 Vendedores %
Percentual do GMV total concentrado nos 10 maiores vendedores. Indicador de risco de dependência do ecossistema.

```dax
% TOP_10_GMV_VENDEDORES = 
                          VAR TOP10_GMV = 
                                          CALCULATE(
                                                     [GMV_TOTAL],
                                                     TOPN(
                                                           10,
                                                           ALL(olist_sellers_dataset[seller_id]),
                                                           [GMV_TOTAL],
                                                           DESC
                                                     )
                                          )
                            RETURN 
                                   DIVIDE(TOP10_GMV, CALCULATE([GMV_TOTAL], ALL(olist_sellers_dataset)), 0)
```
