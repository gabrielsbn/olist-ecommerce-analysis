# Dicionário de Dados — Olist E-Commerce Analysis

## Visão Geral
Este documento descreve todas as tabelas e colunas utilizadas na análise, incluindo tipo de dado, descrição e observações relevantes.

---

## olist_orders_dataset
Tabela central do modelo — registra todos os pedidos realizados.

| Coluna | Tipo | Descrição |
|---|---|---|
| order_id | Texto | Identificador único do pedido |
| customer_id | Texto | Identificador do cliente por pedido |
| order_status | Texto | Status do pedido (delivered, cancelled, etc.) |
| order_purchase_timestamp | Data/Hora | Data e hora da compra |
| order_approved_at | Data/Hora | Data e hora da aprovação do pagamento |
| order_delivered_carrier_date | Data/Hora | Data de entrega à transportadora |
| order_delivered_customer_date | Data/Hora | Data de entrega ao cliente |
| order_estimated_delivery_date | Data/Hora | Data estimada de entrega |

**Observações:**
- 1.737 registros sem `order_delivered_customer_date` — pedidos não entregues - Chave de ligação central com todas as demais tabelas via `order_id`

---

## olist_order_items_dataset
Registra os itens de cada pedido — um pedido pode ter múltiplos itens.

| Coluna | Tipo | Descrição |
|---|---|---|
| order_id | Texto | Identificador do pedido |
| order_item_id | Inteiro | Sequencial do item dentro do pedido |
| product_id | Texto | Identificador do produto |
| seller_id | Texto | Identificador do vendedor |
| shipping_limit_date | Data/Hora | Prazo limite para postagem |
| price | Decimal | Preço do item em R$ |
| freight_value | Decimal | Valor do frete em R$ |

**Observações:**
- Correção aplicada para tipo decimal antes de qualquer análise
- 112.650 linhas para 99.092 pedidos distintos — média de 1,14 itens por pedido

---

## olist_order_payments_dataset
Registra os pagamentos de cada pedido — um pedido pode ter múltiplos meios de pagamento.

| Coluna | Tipo | Descrição |
|---|---|---|
| order_id | Texto | Identificador do pedido |
| payment_sequential | Inteiro | Sequencial do pagamento |
| payment_type | Texto | Meio de pagamento (credit_card, boleto, voucher, debit_card) |
| payment_installments | Inteiro | Número de parcelas |
| payment_value | Decimal | Valor do pagamento em R$ |

**Observações:**
- 103.886 linhas — pedidos com múltiplos meios de pagamento geram múltiplas linhas

---

## olist_order_reviews_dataset
Registra as avaliações dos clientes após a entrega.

| Coluna | Tipo | Descrição |
|---|---|---|
| review_id | Texto | Identificador único da avaliação |
| order_id | Texto | Identificador do pedido avaliado |
| review_score | Inteiro | Nota de 1 a 5 |
| review_comment_title | Texto | Título do comentário (opcional) |
| review_comment_message | Texto | Texto do comentário (opcional) |
| review_creation_date | Data/Hora | Data de envio da solicitação de avaliação |
| review_answer_timestamp | Data/Hora | Data de resposta do cliente |

**Observações:**
- 103.387 linhas para 98.330 pedidos distintos com avaliação
- 762 pedidos sem avaliação — cobertura de 99,2%

---

## olist_customers_dataset
Registra os clientes da plataforma.

| Coluna | Tipo | Descrição |
|---|---|---|
| customer_id | Texto | Identificador do cliente por pedido — gerado a cada compra |
| customer_unique_id | Texto | Identificador real e único do cliente |
| customer_zip_code_prefix | Texto | CEP do cliente (5 dígitos) |
| customer_city | Texto | Cidade do cliente |
| customer_state | Texto | Estado do cliente (sigla) |

**Observações:**
- `customer_id` é gerado a cada pedido — o mesmo cliente tem IDs diferentes
- `customer_unique_id` é o identificador real — usado em todas as análises
- - 96.096 clientes únicos para 99.441 pedidos — confirma baixa taxa de recompra

---

## olist_sellers_dataset
Registra os vendedores ativos na plataforma.

| Coluna | Tipo | Descrição |
|---|---|---|
| seller_id | Texto | Identificador único do vendedor |
| seller_zip_code_prefix | Texto | CEP do vendedor (5 dígitos) |
| seller_city | Texto | Cidade do vendedor |
| seller_state | Texto | Estado do vendedor (sigla) |

**Observações:**
- 3.068 vendedores ativos no período analisado
- 65% concentrados no estado de São Paulo

---

## olist_products_dataset
Registra os produtos disponíveis na plataforma.

| Coluna | Tipo | Descrição |
|---|---|---|
| product_id | Texto | Identificador único do produto |
| product_category_name | Texto | Nome da categoria em português |
| product_name_lenght | Inteiro | Comprimento do nome do produto |
| product_description_lenght | Inteiro | Comprimento da descrição |
| product_photos_qty | Inteiro | Quantidade de fotos |
| product_weight_g | Inteiro | Peso em gramas |
| product_length_cm | Inteiro | Comprimento em cm |
| product_height_cm | Inteiro | Altura em cm |
| product_width_cm | Inteiro | Largura em cm |

---

## olist_geolocation_dataset
Registra coordenadas geográficas por CEP.

| Coluna | Tipo | Descrição |
|---|---|---|
| geolocation_zip_code_prefix | Texto | CEP (5 dígitos) |
| geolocation_lat | Decimal | Latitude |
| geolocation_lng | Decimal | Longitude |
| geolocation_city | Texto | Cidade |
| geolocation_state | Texto | Estado (sigla) |

**Observações:**
- Múltiplas linhas por CEP — necessário deduplicar antes de relacionar
- Utilizada para análise geográfica de clientes e vendedores

---

## product_category_name_translation
Tabela de tradução dos nomes de categorias do português para o inglês.

| Coluna | Tipo | Descrição |
|---|---|---|
| product_category_name | Texto | Nome da categoria em português |
| product_category_name_english | Texto | Nome da categoria em inglês |

---

## Modelo de Relacionamentos

| Tabela origem | Chave | Tabela destino | Tipo |
|---|---|---|---|
| customers | customer_id | orders | 1 para muitos |
| orders | order_id | order_items | 1 para muitos |
| orders | order_id | order_payments | 1 para muitos |
| orders | order_id | order_reviews | 1 para muitos |
| order_items | product_id | products | muitos para 1 |
| order_items | seller_id | sellers | muitos para 1 |
| products | product_category_name | category_translation | muitos para 1 |
| customers | customer_zip_code_prefix | geolocation | muitos para 1 |
