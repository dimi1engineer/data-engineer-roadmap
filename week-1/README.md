# Semana 1 — Base forte: SQL, modelagem e Python

Esta pasta contém o conteúdo de estudo, o projeto guia e o espaço para anotações e desenvolvimentos da semana 1 do roadmap.

---

## Objetivo da semana

Consolidar SQL avançado, modelagem de dados analítica e estruturação de código Python para produção, iniciando o projeto-base que será utilizado ao longo dos 90 dias.

---

## Conteúdo de estudo

### Dia 1 — Planejamento e ambiente

- Definir o projeto-base da semana (ver seção abaixo).
- Configurar ambiente local: Python, virtualenv ou conda, editor de código.
- Configurar banco de dados local (PostgreSQL, DuckDB ou SQLite).
- Criar estrutura inicial do repositório/pasta do projeto.

**Referências:**
- [DuckDB — banco leve ideal para projetos analíticos locais](https://duckdb.org/docs/)
- [Python venv — ambientes virtuais](https://docs.python.org/3/library/venv.html)

---

### Dia 2 — SQL: JOINs, CTEs e Window Functions

**JOINs:**
- `INNER JOIN`: retorna registros com correspondência em ambas as tabelas.
- `LEFT JOIN`: retorna todos os registros da tabela esquerda e os correspondentes da direita.
- `FULL OUTER JOIN`: retorna todos os registros de ambas as tabelas.
- `SELF JOIN`: junção de uma tabela com ela mesma.

**CTEs (Common Table Expressions):**
```sql
WITH vendas_por_cliente AS (
    SELECT cliente_id, SUM(valor) AS total
    FROM pedidos
    GROUP BY cliente_id
)
SELECT c.nome, v.total
FROM clientes c
JOIN vendas_por_cliente v ON c.id = v.cliente_id;
```

**Window Functions:**
```sql
SELECT
    nome,
    salario,
    ROW_NUMBER() OVER (PARTITION BY departamento ORDER BY salario DESC) AS rank,
    AVG(salario) OVER (PARTITION BY departamento) AS media_depto
FROM funcionarios;
```

Funções mais usadas: `ROW_NUMBER()`, `RANK()`, `DENSE_RANK()`, `LAG()`, `LEAD()`, `SUM() OVER`, `AVG() OVER`.

**Referências:**
- [Mode SQL Tutorial — Window Functions](https://mode.com/sql-tutorial/sql-window-functions/)
- [PostgreSQL Docs — Window Functions](https://www.postgresql.org/docs/current/tutorial-window.html)
- [SQLZoo — exercícios práticos](https://sqlzoo.net/)

---

### Dia 3 — Exercícios de SQL avançado

Pratique os conceitos do dia anterior com os seguintes tipos de exercício:

1. Ranking de clientes por valor total de compras usando `RANK()`.
2. Calcular a média móvel de vendas dos últimos 3 meses com `AVG() OVER`.
3. Identificar clientes que compraram em meses consecutivos usando `LAG()`.
4. Criar uma CTE para calcular receita por categoria e filtrar as top 5.
5. Encontrar duplicatas em uma tabela usando `ROW_NUMBER()`.

**Sites de exercícios:**
- [LeetCode Database](https://leetcode.com/problemset/database/)
- [HackerRank SQL](https://www.hackerrank.com/domains/sql)
- [StrataScratch](https://www.stratascratch.com/)

---

### Dia 4 — Modelagem de dados analítica

**Modelagem dimensional:**
- **Tabela fato**: armazena métricas e eventos (ex: vendas, cliques, transações).
- **Tabela dimensão**: armazena contexto (ex: cliente, produto, data, localização).
- **Star schema**: fato no centro, dimensões ao redor — mais simples e performático.
- **Snowflake schema**: dimensões normalizadas — mais detalhado, mais joins.

**Camadas de dados (medalha):**

| Camada | Nome | Descrição |
|--------|------|-----------|
| Raw | Bronze | Dados brutos, sem transformação, espelho da fonte. |
| Trusted | Silver | Dados limpos, tipados, deduplicados e validados. |
| Refined | Gold | Dados modelados e prontos para consumo analítico. |

**Exemplo de estrutura de tabelas:**
```
fato_pedidos
  - pedido_id (PK)
  - cliente_id (FK → dim_clientes)
  - produto_id (FK → dim_produtos)
  - data_id (FK → dim_data)
  - valor
  - quantidade

dim_clientes
  - cliente_id (PK)
  - nome
  - cidade
  - segmento
```

**Referências:**
- [dbt Docs — camadas de dados](https://docs.getdbt.com/best-practices/how-we-structure/1-guide-overview)
- [Kimball Group — modelagem dimensional](https://www.kimballgroup.com/data-warehouse-business-intelligence-resources/)

---

### Dia 5 — Arquitetura do projeto-base

Documente a arquitetura do projeto escolhido com:

- Fonte de dados: origem dos dados (API, CSV, banco, etc.).
- Ingestão: como os dados entram no sistema.
- Armazenamento: onde ficam (pasta local, S3, banco).
- Transformação: o que será feito com os dados.
- Saída: onde os dados processados serão entregues.
- Orquestração: como o fluxo será executado (Airflow, script, etc.).

Use o arquivo `arquitetura.md` nesta pasta para registrar isso.

---

### Dia 6 — Implementação inicial

Inicie o projeto com:

1. Criar estrutura de pastas do projeto.
2. Implementar script de ingestão de dados.
3. Salvar dados brutos na camada bronze.
4. Criar conexão com banco de dados ou sistema de armazenamento.
5. Versionar no GitHub com commit descritivo.

**Estrutura sugerida de pastas:**
```
projeto/
├── data/
│   ├── bronze/
│   ├── silver/
│   └── gold/
├── src/
│   ├── ingestion/
│   ├── transformation/
│   └── utils/
├── tests/
├── README.md
└── requirements.txt
```

---

### Dia 7 — Revisão e anotações

- Revisar o que foi estudado e implementado.
- Registrar dúvidas, dificuldades e descobertas.
- Atualizar este README com aprendizados da semana.
- Commitar tudo que foi produzido.

---

## Projeto guia da semana

### Pipeline de análise de vendas

**Descrição:**  
Construir um pipeline simples que ingere dados de vendas (CSV ou API pública), armazena em camadas bronze/silver/gold e disponibiliza uma tabela analítica pronta para consultas.

**Fonte de dados sugerida:**  
- [Brazilian E-Commerce (Olist) — Kaggle](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce): dataset real de e-commerce brasileiro com pedidos, clientes, produtos e avaliações.
- Alternativa: gerar dados fictícios com Python (`faker`).

**Etapas:**

1. **Ingestão** — ler CSV e salvar na camada bronze sem alterações.
2. **Transformação (silver)** — limpar nulos, tipar colunas, remover duplicatas.
3. **Modelagem (gold)** — criar tabela fato de pedidos e dimensões de clientes e produtos.
4. **Consulta analítica** — responder perguntas com SQL usando window functions e CTEs:
   - Quais os 10 clientes com maior valor total de compras?
   - Qual a média móvel de receita por mês?
   - Quais produtos têm maior taxa de cancelamento?

**Tecnologias:**  
Python, pandas, DuckDB ou PostgreSQL, SQL.

**Entregável:**  
Script funcional com as 3 camadas, pelo menos 3 queries analíticas documentadas e README com descrição da arquitetura.

---

## Espaço para anotações

> Use os arquivos abaixo para registrar seus desenvolvimentos ao longo da semana.

- `anotacoes-sql.md` — anotações sobre SQL avançado.
- `anotacoes-modelagem.md` — anotações sobre modelagem de dados.
- `arquitetura.md` — diagrama e descrição da arquitetura do projeto.
- `diario.md` — registro diário de progresso, dúvidas e aprendizados.
