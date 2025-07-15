# 📊 Análise de Vendas E-commerce com SQL no Databricks

Um projeto abrangente de **Análise de Dados** focado em **SQL**, utilizando o ambiente **Databricks** para extrair insights valiosos de um dataset de vendas de e-commerce. Este repositório demonstra habilidades em manipulação, consulta e agregação de dados, incluindo o uso de `JOIN`s, `CTE`s (Common Table Expressions) e `VIEW`s para responder a perguntas de negócio estratégicas e construir um dashboard analítico.

---

## 🎯 Objetivo do Projeto

O principal objetivo deste projeto é desenvolver e demonstrar proficiência em SQL no contexto de uma análise de vendas de e-commerce. Através de um estudo de caso prático, buscou-se:

* **Extrair Insights Acionáveis:** Analisar o comportamento de compra, performance de produtos e tendências de vendas.
* **Otimizar Operações:** Fornecer dados para melhor gestão de estoque, logística e estratégias de marketing.
* **Aprimorar Habilidades em SQL:** Aplicar comandos SQL básicos a avançados (`SELECT`, `GROUP BY`, `ORDER BY`, `JOIN`, `CTE`, `VIEW`) em um cenário de negócio real.
* **Construir um Pipeline de Análise:** Desde o entendimento do problema até a visualização dos dados em um dashboard.

---

## 🛍️ Contexto de Negócio: Otimizando Vendas em um E-commerce

Este projeto simula o papel de um analista de dados em uma grande empresa de e-commerce que opera em todo o Brasil. A organização enfrenta desafios comuns, como a falta de uma visão clara sobre os padrões de compra, dificuldade em identificar clientes de alto valor e a necessidade de otimizar suas operações de vendas e logística.

Nosso trabalho focou em desvendar os dados transacionais para responder a perguntas-chave que possam informar decisões estratégicas, visando:

* **Aumento de Receita:** Identificando produtos e categorias de alta performance.
* **Melhora da Experiência do Cliente:** Compreendendo as preferências de compra.
* **Eficiência Operacional:** Fornecendo dados para uma gestão de estoque e entrega mais eficaz.
* **Apoio à Decisão Estratégica:** Oferecendo uma base sólida para o planejamento futuro do negócio.

---

## 💾 Fontes de Dados

Para este projeto, utilizamos o **Brazilian E-Commerce Public Dataset by Olist**, disponível no Kaggle. Este dataset é ideal para a prática de SQL, pois contém múltiplas tabelas que permitem a criação de relacionamentos e a execução de consultas complexas.

As principais tabelas utilizadas foram:

* **`orders`**: Contém informações sobre os pedidos (ID do pedido, ID do cliente, status, datas de compra e entrega).
* **`order_items`**: Detalha os itens dentro de cada pedido (ID do pedido, ID do produto, preço, valor do frete).
* **`products`**: Armazena informações sobre os produtos (ID do produto, categoria, atributos como peso e comprimento).
* **`customers`**: Contém dados dos clientes (ID do cliente, cidade, estado).

---

## 🛠️ Ferramentas Utilizadas

* **SQL (Structured Query Language):** Linguagem principal para manipulação e consulta dos dados.
* **Databricks:** Plataforma unificada de dados e IA, utilizada para carregar os datasets, executar as consultas SQL e, opcionalmente, criar o dashboard.

---

## 📈 Análises e Consultas SQL

Nesta seção, apresentamos as principais perguntas de negócio e as consultas SQL desenvolvidas para respondê-las. Todas as consultas foram executadas no ambiente Databricks.

### 1. Volume de Pedidos e Receita Total por Mês e Ano

**Pergunta de Negócio:** Qual o desempenho de vendas (número de pedidos e receita) ao longo do tempo para identificar sazonalidades e tendências?

```sql
SELECT
    YEAR(o.order_purchase_timestamp) AS purchase_year,
    MONTH(o.order_purchase_timestamp) AS purchase_month,
    COUNT(DISTINCT o.order_id) AS total_orders,
    SUM(oi.price + oi.freight_value) AS total_revenue
FROM
    orders AS o
JOIN
    order_items AS oi ON o.order_id = oi.order_id
GROUP BY
    purchase_year,
    purchase_month
ORDER BY
    purchase_year,
    purchase_month;
```
## 2. Top 10 Categorias Mais Vendidas em Receita

**Pergunta de Negócio:** Quais categorias de produtos geram a maior receita para a empresa, ajudando a focar esforços de marketing e estoque?

```sql
SELECT
    p.product_category_name,
    SUM(oi.price + oi.freight_value) AS category_revenue
FROM
    products AS p
JOIN
    order_items AS oi ON p.product_id = oi.product_id
GROUP BY
    p.product_category_name
ORDER BY
    category_revenue DESC
LIMIT 10;
```
## 3. Clientes com Maior Valor de Compra (LTV - Lifetime Value)

**Pergunta de Negócio:** Quem são os clientes mais valiosos com base no valor total de suas compras, para programas de fidelidade ou comunicação direcionada?

```sql
SELECT
    c.customer_id,
    c.customer_city,
    c.customer_state,
    SUM(oi.price + oi.freight_value) AS total_spent
FROM
    customers AS c
JOIN
    orders AS o ON c.customer_id = o.customer_id
JOIN
    order_items AS oi ON o.order_id = oi.order_id
GROUP BY
    c.customer_id,
    c.customer_city,
    c.customer_state
ORDER BY
    total_spent DESC
LIMIT 5;

```
## 4. Média de Itens por Pedido (Usando CTE)

**Pergunta de Negócio:** Em média, quantos itens um cliente compra por pedido, indicando o comportamento de compra e otimização de estratégias de up-selling/cross-selling?

```sql
WITH OrderItemCounts AS (
    SELECT
        order_id,
        COUNT(order_item_id) AS num_items
    FROM
        order_items
    GROUP BY
        order_id
)
SELECT
    AVG(num_items) AS average_items_per_order
FROM
    OrderItemCounts;
```
## 5. Status dos Pedidos (Usando CTE)

**Pergunta de Negócio:** Qual a distribuição dos status dos pedidos e qual a média de tempo para entrega, avaliando a eficiência da operação de entrega?

```sql
-- Contagem de Pedidos por Status
SELECT
    order_status,
    COUNT(order_id) AS num_orders
FROM
    orders
GROUP BY
    order_status
ORDER BY
    num_orders DESC;
```

## 6. Criação de uma VIEW para Análise Contínua: Vendas Detalhadas

**Propósito:** Consolidar informações de múltiplas tabelas em uma única VIEW para simplificar consultas futuras e a construção do dashboard, oferecendo uma visão unificada de pedidos, itens, produtos e clientes.

```sql
CREATE VIEW sales_detail_view AS
SELECT
    o.order_id,
    o.order_status,
    o.order_purchase_timestamp,
    o.order_approved_at,
    o.order_delivered_customer_date,
    c.customer_id,
    c.customer_city,
    c.customer_state,
    p.product_id,
    p.product_category_name,
    oi.price,
    oi.freight_value,
    (oi.price + oi.freight_value) AS total_item_value
FROM
    orders AS o
JOIN
    customers AS c ON o.customer_id = c.customer_id
JOIN
    order_items AS oi ON o.order_id = oi.order_id
JOIN
    products AS p ON oi.product_id = p.product_id;
```

## 📊 Dashboard de Vendas

O dashboard foi construído no Databricks para visualizar os principais insights extraídos das consultas SQL. Ele oferece uma visão consolidada do desempenho de vendas, tendências, categorias de produtos e comportamento do cliente, sendo uma ferramenta essencial para a tomada de decisões estratégicas.

**Principais Seções do Dashboard:**

KPIs Essenciais: Receita Total, Número Total de Pedidos,Total de Pedidos Aprovados, Pedidos em Trânsito e Pedidos Entregues

Tendência de Vendas: Gráfico de linha mostrando receita e número de pedidos ao longo do tempo.

Performance de Produtos: Gráfico de barras das Top 10 Categorias por Receita.

![Dashboard de Vendas E-commerce](images/dashboard_final.png)
