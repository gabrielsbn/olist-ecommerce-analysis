# Métricas Comerciais — Medidas DAX

## Visão Geral
Medidas DAX utilizadas na página de Desempenho Comercial — Visão Geral.
Todas as medidas estão armazenadas na tabela `_Medidas` no modelo Power BI.

---

## GMV Total
Soma do valor de todos os itens vendidos, excluindo frete.

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

## Receita de Frete
Soma do valor de frete de todos os itens.

```dax
RECEITA_FRETE = 
                SUMX(olist_order_items_dataset,
                     olist_order_items_dataset[freight_value]
                )
```

---

## Receita Total
GMV somado à receita de frete — total movimentado pelo ecossistema.

```dax
RECEITA_TOTAL = 
                [GMV_TOTAL] + [RECEITA_FRETE]
```

---

## Volume de Pedidos
Contagem distinta de pedidos — evita duplicação por múltiplos itens.

```dax
VOLUME_PEDIDOS = 
                DISTINCTCOUNT(olist_orders_dataset[order_id])
```

---

## Total de Clientes Únicos
Contagem de clientes reais — usa `customer_unique_id` e não `customer_id`.

```dax
TOTAL_CLIENTES = 
                DISTINCTCOUNT(olist_customers_dataset[customer_unique_id])
```

---

## Ticket Médio
Receita média por pedido.

```dax
TICKET_MEDIO = 
                DIVIDE([GMV_TOTAL],
                       [VOLUME_PEDIDOS],
                       0
                )
```

---

## Frete % GMV
Percentual do frete em relação ao GMV — indicador de eficiência logística.

```dax
% FRETE_GMV = DIVIDE([RECEITA_FRETE],
                     [GMV_TOTAL],
                     0
                )
```

---

## Frete Médio por Pedido
Valor médio de frete por item vendido.

```dax
FRETE_MEDIO_PEDIDO = 
                     AVERAGEX(
                              olist_order_items_dataset,
                              olist_order_items_dataset[freight_value]
                     )
```

---

## Parcelamento Médio
Número médio de parcelas por transação.

```dax
PARCELAMENTO_MEDIO = 
                    AVERAGEX(
                                olist_order_payments_dataset,
                                olist_order_payments_dataset[payment_installments])
```

---

## GMV Top Categoria
Valor de GMV da categoria com maior faturamento.

```dax
GMV_TOP_CATEGORIA = 
                    MAXX(
                          ALL(olist_products_dataset[product_category_name]),
                          [GMV_TOTAL]
                    )
```

---

## Frete % Preço por Categoria
Percentual do frete em relação ao preço do produto por categoria.
Exclui itens com preço zero para evitar distorção.

```dax
% FRETE_PRECO_CATEGORIA = 
                          DIVIDE(
                                 SUMX(
                                       FILTER(olist_order_items_dataset, olist_order_items_dataset[price] > 0),
                                       olist_order_items_dataset[freight_value]
                                       ),
                                 SUMX(
                                       FILTER(olist_order_items_dataset, olist_order_items_dataset[price] > 0),
                                       olist_order_items_dataset[price]
                                 ),
                                 0
                          )
```

---

## GMV com Tratamento de Branco
Versão do GMV que retorna zero em vez de branco para meses sem pedidos.
Necessária para manter continuidade da linha no gráfico temporal.

```dax
GMV_TOTAL = 
 CALCULATE(
        SUMX(
            olist_order_items_dataset, olist_order_items_dataset[price]),
            CROSSFILTER(olist_orders_dataset[order_id], olist_order_items_dataset[order_id], Both)
        )
```
