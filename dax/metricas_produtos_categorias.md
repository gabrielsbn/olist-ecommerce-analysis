# Métricas de Produtos e Categorias — Medidas DAX

## Visão Geral
Medidas DAX utilizadas na página de Análise de Produtos e Categorias.
As medidas desta página combinam indicadores comerciais e de satisfação
aplicados ao contexto de categoria de produto.

---

## Medidas Utilizadas Nesta Página

| Medida | Arquivo de Referência | Descrição |
|---|---|---|
| GMV Total | metricas_comerciais.md | Faturamento total filtrado por categoria |
| GMV Top Categoria | metricas_comerciais.md | Categoria com maior faturamento |
| Frete % Preço Categoria | metricas_comerciais.md | Custo relativo de frete por categoria |
| Nota Média Geral | metricas_clientes.md | Satisfação média geral |
| Nota Média por Categoria | metricas_clientes.md | Satisfação média filtrada por categoria |

---

## Lógica Analítica da Página

Esta página cruza três dimensões por categoria de produto:

1. **Faturamento** — quais categorias mais contribuem para o GMV
2. **Satisfação** — quais categorias têm clientes mais e menos satisfeitos
3. **Custo de frete relativo** — onde o frete é desproporcional ao preço

O cruzamento dessas três dimensões na tabela consolidada permite
identificar categorias com alto GMV mas baixa satisfação — 
sinalizando risco — e categorias com frete desproporcional ao preço
do produto — sinalizando problema estrutural de precificação logística.

---

## Principais Achados

| Categoria | Situação | Frete % Preço | Nota Média |
|---|---|---|---|
| Casa Conforto 2 | Crítico — frete alto e nota baixa | 53,97% | 3,6 |
| Fraldas e Higiene | Atenção — frete alto e nota baixa | 38,92% | 3,7 |
| Móveis Escritório | Atenção — nota mais baixa da análise | — | 3,6 |
| CDs, DVDs e Musicais | Destaque — melhor nota da plataforma | — | 4,7 |
| Saúde e Beleza | Líder em GMV com satisfação acima da média | — | 4,1 |
