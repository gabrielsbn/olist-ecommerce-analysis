# Segmentação RFM — Tabelas e Medidas DAX

## Visão Geral
Documentação completa da análise RFM (Recência, Frequência e Monetização) aplicada à base de clientes da Olist. A segmentação classifica os 96.096 clientes únicos em grupos comportamentais para orientar estratégias de retenção e reativação.

---

## Conceito RFM

| Dimensão | Pergunta | Score Alto |
|---|---|---|
| Recência (R) | Há quantos dias foi a última compra? | Comprou recentemente |
| Frequência (F) | Quantas vezes comprou? | Comprou muitas vezes |
| Monetização (M) | Quanto gastou no total? | Gastou muito |

Cada dimensão recebe um score de 1 a 5. A combinação dos três scores
define o segmento de cada cliente.

---

## Tabela RFM_Base
Tabela calculada que consolida os dados brutos de cada cliente. Data de referência: 31/08/2018 (último dia do período analisado).

```dax
RFM_Base = 
           ADDCOLUMNS(
                      SUMMARIZE(
                                olist_orders_dataset,
                                olist_customers_dataset[customer_unique_id],
                                "ULTIMA_COMPRA",
                                       MAX(olist_orders_dataset[order_purchase_timestamp]),
                                "FREQUENCIA",
                                       CALCULATE(DISTINCTCOUNT(olist_orders_dataset[order_id])),
                                "MONETIZACAO",
                                       CALCULATE(SUMX(olist_order_items_dataset, olist_order_items_dataset[price]))
                      ),
                      "RECENCIA",
                                DATEDIFF(
                                          [ULTIMA_COMPRA],
                                          DATE(2018, 8, 31),
                                          DAY
                                )
           )
```

---

## Tabela RFM_Scores
Tabela calculada que converte os valores brutos em scores de 1 a 5 e classifica cada cliente no segmento correspondente.

```dax
RFM_Scores = 
             ADDCOLUMNS(
                        RFM_Base,
                        "SCORE_R",
                                  SWITCH(
                                         TRUE(),
                                         RFM_Base[RECENCIA] <= 90, 5,
                                         RFM_Base[RECENCIA] <= 180, 4,
                                         RFM_Base[RECENCIA] <= 270, 3,
                                         RFM_Base[RECENCIA] <= 365, 2,
                                         1
                                  ),
                        "SCORE_F",
                                  SWITCH(
                                         TRUE(),
                                         RFM_Base[FREQUENCIA] >= 5, 5,
                                         RFM_Base[FREQUENCIA] = 4,  4,
                                         RFM_Base[FREQUENCIA] = 3,  3,
                                         RFM_Base[FREQUENCIA] = 2,  2,
                                         1
                                  ),
                        "SCORE_M",
                                  SWITCH(
                                         TRUE(),
                                         RFM_Base[MONETIZACAO] >= 1000, 5,
                                         RFM_Base[MONETIZACAO] >= 500,  4,
                                         RFM_Base[MONETIZACAO] >= 200,  3,
                                         RFM_Base[MONETIZACAO] >= 100,  2,
                                         1
                                   )
             )
```

---

## Coluna Segmento_Refinado
Coluna calculada adicionada à tabela `RFM_Scores` que classifica cada cliente em um segmento comportamental.

```dax
SEGMENTO_REFINADO = 
           VAR R = [SCORE_R]
           VAR F = [SCORE_F]
           VAR M = [SCORE_M]
           VAR RFM_Score = R * 100 + F * 10 + M
RETURN
        SWITCH(
                TRUE(),
                R >= 4 && F >= 4 && M >= 4, "Campeões",
                R >= 3 && F >= 3 && M >= 3, "Clientes Fiéis",
                R <= 2 && F >= 2,           "Em Risco",
                R >= 4 && F = 1,            "Novos Clientes",
                R >= 3 && F = 1,            "Potencial",
                R <= 2 && F = 1 && M <= 2,  "Hibernando",
                "Perdidos"
            )
```

---

## Distribuição dos Segmentos

| Segmento | Clientes | % | Prioridade Estratégica |
|---|---|---|---|
| Novos Clientes | 37.238 | 38,7% | Alta — ativar primeira recompra |
| Hibernando | 31.573 | 32,9% | Média — reengajar com oferta |
| Potencial | 18.371 | 19,1% | Alta — converter clientes quentes |
| Perdidos | 7.727 | 8,0% | Baixa — custo alto de reativação |
| Em Risco | 1.059 | 1,1% | Urgente — eram bons clientes |
| Clientes Fiéis | 110 | 0,1% | Crítica — proteger base fiel |
| Campeões | 18 | 0,02% | Crítica — tratamento VIP |

---

## Critérios de Score por Dimensão

### Recência
| Score | Critério |
|---|---|
| 5 | Última compra há até 90 dias |
| 4 | Última compra entre 91 e 180 dias |
| 3 | Última compra entre 181 e 270 dias |
| 2 | Última compra entre 271 e 365 dias |
| 1 | Última compra há mais de 365 dias |

### Frequência
| Score | Critério |
|---|---|
| 5 | 5 ou mais compras |
| 4 | 4 compras |
| 3 | 3 compras |
| 2 | 2 compras |
| 1 | 1 compra |

### Monetização
| Score | Critério |
|---|---|
| 5 | Gasto total acima de R$ 1.000 |
| 4 | Gasto total entre R$ 500 e R$ 999 |
| 3 | Gasto total entre R$ 200 e R$ 499 |
| 2 | Gasto total entre R$ 100 e R$ 199 |
| 1 | Gasto total abaixo de R$ 100 |

---

## Insight Principal
57,8% dos clientes são Novos ou Potenciais — compraram recentemente mas não voltaram. Converter 5% desse grupo em clientes fiéis geraria aproximadamente 2.780 novos compradores recorrentes, triplicando a base fiel atual sem nenhum investimento em aquisição.
