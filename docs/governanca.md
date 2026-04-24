# Governança de Dados — Olist E-Commerce Analysis

## Visão Geral
Este documento registra todas as decisões analíticas, critérios de exclusão e tratamentos de dados aplicados ao longo do projeto. O objetivo é garantir transparência, reproducibilidade e rastreabilidade de cada escolha metodológica.

---

## 1. Recorte Temporal

**Decisão:** Exclusão do período de setembro a novembro de 2016.

**Motivo:** O dataset cobre formalmente o período de setembro de 2016 a outubro de 2018. No entanto, os meses de setembro a novembro de 2016 representam a fase inicial da operação, com volume de pedidos estatisticamente irrelevante e dados fragmentados que distorceriam 
análises de tendência e crescimento.

**Impacto:** Afeta GMV total, receita, volume de pedidos e total de clientes únicos. Os valores apresentados no dashboard refletem exclusivamente o período de janeiro de 2017 a agosto de 2018.

**Período oficial de análise:** Janeiro de 2017 a Agosto de 2018 (20 meses de operação consolidada).

---

## 2. Outlier — Amapá (Análise Logística)

**Decisão:** Exclusão do estado do Amapá do visual de atraso médio por estado.

**Motivo:** O estado do Amapá apresentou atraso médio de 144 dias, resultante de um único pedido com 145 dias de atraso registrado. Por tratar-se de um outlier isolado sem representatividade estatística, sua inclusão distorceria completamente a escala do visual e 
comprometeria a leitura dos demais estados.

**Impacto:** Afeta exclusivamente o visual de atraso médio por estado na página de Eficiência Operacional.

---

## 4. Outlier — Amapá (Análise de Satisfação)

**Decisão:** Exclusão do estado do Amapá do visual de nota média por estado.

**Motivo:** O Amapá apresenta volume muito baixo de pedidos, gerando uma média de satisfação instável e não representativa da experiência regional. A inclusão de médias calculadas sobre amostras muito pequenas compromete a validade estatística da comparação entre estados.

**Impacto:** Afeta o visual de satisfação média por estado na página de Satisfação e Retenção de Clientes.

---

## 5. Categoria Seguros e Serviços

**Decisão:** Exclusão da categoria "seguros e serviços" de todos os visuais de análise por categoria.

**Motivo:** A categoria registrou apenas 2 pedidos no período analisado, volume absolutamente insuficiente para representatividade estatística. A nota média de 2,5 registrada para essa categoria não reflete padrão de comportamento — é resultado de amostra mínima sem validade analítica.

**Impacto:** Afeta os visuais de satisfação por categoria e frete percentual por categoria na página de Análise de Produtos e Categorias.

---

## 6. NPS Adaptado — Metodologia

**Decisão:** Utilização de NPS adaptado baseado no `review_score` de 1 a 5 em vez do NPS clássico de 0 a 10.

**Motivo:** O dataset não contém a pergunta padrão de NPS ("De 0 a 10, quanto você recomendaria?"). A adaptação foi necessária e é declarada explicitamente em todos os visuais e no relatório.

**Critério de classificação:**
| Classificação | Notas | Equivalência NPS Clássico |
|---|---|---|
| Promotores | 5 | 9 – 10 |
| Neutros | 4 | 7 – 8 |
| Detratores | 1, 2 e 3 | 0 – 6 |

**Fórmula:** NPS = % Promotores − % Detratores × 100

**Resultado:** NPS Adaptado = +35 (faixa "bom" — benchmark mínimo: 30)

---

## 7. Queda Pontual do NPS — Janeiro de 2018

**Observação:** O índice de satisfação registrou queda pontual em janeiro de 2018, atingindo NPS 20 — abaixo do benchmark de 30.

**Investigação:** A análise dos dados aponta para efeito cascata do período festivo. O pico de volume em novembro e dezembro de 2017 gerou sobrecarga nas transportadoras, elevando o tempo médio de entrega em 2 dias e o atraso médio em 1 dia em janeiro de 2018. Esse padrão 
é consistente com a correlação identificada no dataset entre atraso e satisfação.

**Conclusão:** O NPS retomou trajetória ascendente nos meses seguintes, confirmando o caráter sazonal e não estrutural do evento.

---

## Qualidade dos Dados

| Dimensão | Resultado |
|---|---|
| Cobertura de reviews | 99,2% dos pedidos avaliados |
| Pedidos sem itens registrados | 0,4% — excluídos da análise comercial |
| Colunas com nulos relevantes | order_delivered_customer_date — 1.737 registros |
| Período com dados completos | Janeiro 2017 a Agosto 2018 |
| Outliers identificados e tratados | 2 (Amapá logístico e Amapá satisfação) |
| Categorias excluídas | 1 (Seguros e Serviços — 2 pedidos) |
