# Olist E-Commerce — Análise de Dados para Investidores

## Sobre o Projeto
Análise de dados do Brazilian E-Commerce Public Dataset by Olist, desenvolvida como parte do programa de pós-tech em Data Analytics. O objetivo é transformar dados transacionais em uma narrativa executiva sobre desempenho comercial, eficiência logística e satisfação do cliente, culminando em recomendações acionáveis para investidores e acionistas.

## Dataset
- **Fonte:** Brazilian E-Commerce Public Dataset by Olist (Kaggle);
- **Período analisado:** Janeiro de 2017 a Agosto de 2018;
- **Volume:** ~99 mil pedidos, 96 mil clientes únicos, 3.068 vendedores ativos;
- **Tabelas utilizadas:** orders, order_items, order_payments, order_reviews, customers, sellers, products, geolocation, category_name_translation.

## Ferramentas Utilizadas
- **Power BI** — modelagem de dados, medidas DAX e dashboard executivo;
- **GitHub** — documentação e versionamento do projeto.

## Estrutura do Repositório

📁 **olist-ecommerce-analysis**
- 📄 `README.md` — Visão geral do projeto
- 📁 **docs/**
  - 📄 `governanca.md` — Decisões analíticas e critérios de qualidade
  - 📄 `dicionario_dados.md` — Descrição das tabelas e colunas
- 📁 **dax/**
  - 📄 `metricas_comerciais.md` — Medidas de desempenho comercial
  - 📄 `metricas_logistica.md` — Medidas de eficiência logística
  - 📄 `metricas_clientes.md` — Medidas de satisfação e retenção
  - 📄 `metricas_rfm.md` — Segmentação RFM de clientes
- 📁 **dashboard/**
  - 📄 `olist_dashboard.pbix` — Arquivo do dashboard Power BI

## Principais Indicadores

### Desempenho Comercial
| Indicador | Valor | Referência |
|---|---|---|
| GMV Total | R$ 12,54M | Jan/2017 – Ago/2018 |
| Receita Total | R$ 15,78M | Inclui frete |
| Ticket Médio | R$ 136,66 | Por pedido |
| Frete % GMV | 16,57% | Custo relativo |
| Parcelamento Médio | 3x | Cartão de crédito |

### Base de Clientes
| Indicador | Valor | Referência |
|---|---|---|
| Clientes Únicos | 95.774 | customer_unique_id |
| Taxa de Recompra | 3,11% | Clientes que voltaram |
| NPS Adaptado | +35 | Escala de -100 a +100 |
| Satisfação Média | 4,0 | Escala de 1 a 5 |

### Eficiência Logística
| Indicador | Valor | Referência |
|---|---|---|
| Entregas no Prazo | 93,41% | Base período filtrado |
| Tempo Médio de Entrega | 12 dias | Compra até entrega |
| Atraso Médio | 11 dias | Apenas pedidos atrasados |
| Frete Médio por Pedido | R$ 19,99 | Por item |

### Ecossistema de Vendedores
| Indicador | Valor | Referência |
|---|---|---|
| Vendedores Ativos | 3.068 | Período analisado |
| Concentração Top 10 | 13,19% | % do GMV total |
| Principal Estado | SP | ~65% dos vendedores |

## Estrutura do Dashboard
O dashboard é composto por quatro páginas temáticas:

1. **Desempenho Comercial — Visão Geral**
   GMV, receita, ticket médio, evolução mensal e mix de pagamentos

2. **Análise de Produtos e Categorias**
   Faturamento, satisfação e custo de frete por categoria de produto

3. **Satisfação e Retenção de Clientes**
   NPS, taxa de recompra, correlação atraso × satisfação e segmentação RFM

4. **Eficiência Operacional e Desempenho de Vendedores**
   Decomposição do SLA, atraso por estado e análise de vendedores

## Decisões Analíticas
As principais decisões metodológicas estão documentadas em 
[docs/governanca.md](docs/governanca.md).

## Autor
Gabriel da Silva Brandão Nascimento [RM374018] — Pós-Tech em Data Analytics FIAP 2026.
