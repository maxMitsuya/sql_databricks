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
