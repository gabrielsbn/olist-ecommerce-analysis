# Métricas de Produtos e Categorias — Medidas DAX

## Visão Geral
Medidas DAX utilizadas na página de Análise de Produtos e Categorias.
Esta página cruza três dimensões por categoria de produto: faturamento,
satisfação do cliente e custo relativo de frete.

---

## GMV Top Categoria
Retorna o valor de GMV da categoria com maior faturamento.
Utilizado no KPI card de referência rápida da página.

```dax
GMV_TOP_CATEGORIA = 
                    MAXX(
                          ALL(olist_products_dataset[product_category_name]),
                          [GMV_TOTAL]
                    )
```

---

## Nota Média por Categoria
Média do review score filtrada pelo contexto de categoria de produto.
Utilizada no visual de satisfação por categoria.

```dax
NOTA_MEDIA_CATEGORIA = 
                        AVERAGEX(
                                 olist_order_reviews_dataset,
                                 olist_order_reviews_dataset[review_score]
                        )
```

---

## Frete % Preço por Categoria
Percentual do valor do frete em relação ao preço do produto por categoria.
Exclui itens com preço zero para evitar distorção de média.

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

## Frete % GMV (KPI Card)
Percentual da receita de frete em relação ao GMV total.
Utilizado no KPI card da página como indicador macro de custo logístico.

```dax
% FRETE_GMV = DIVIDE([RECEITA_FRETE],
                     [GMV_TOTAL],
                      0
               )
```

---

## Lógica Analítica da Página

Esta página cruza três dimensões por categoria de produto:

1. **Faturamento** — quais categorias mais contribuem para o GMV
2. **Satisfação** — quais categorias têm clientes mais e menos satisfeitos
3. **Custo de frete relativo** — onde o frete é desproporcional ao preço

O cruzamento dessas três dimensões na tabela consolidada permite
identificar categorias com alto GMV mas baixa satisfação — sinalizando
risco — e categorias com frete desproporcional ao preço do produto —
sinalizando problema estrutural de precificação logística.

---

## Principais Achados

| Categoria | Frete % Preço | Nota Média | Situação |
|---|---|---|---|
| Casa Conforto 2 | 53,97% | 3,6 | Crítico |
| Fraldas e Higiene | 38,92% | 3,7 | Atenção |
| Flores | 44,04% | — | Atenção |
| Móveis Escritório | — | 3,6 | Atenção |
| CDs, DVDs e Musicais | — | 4,7 | Destaque positivo |
| Saúde e Beleza | — | 4,1 | Líder em GMV |
