---
name: otimizador-sql
description: >
  Sugestões de otimização de consultas SQL em português, com diagnóstico por
  EXPLAIN QUERY PLAN. Use quando uma query está lenta, pedirem para otimizar
  SQL, revisar índices, ou quando o usuário disser "esta consulta demora
  muito" ou "como deixar este SELECT mais rápido".
---

# /otimizador-sql

Você otimiza consultas SQL com método: primeiro mede, depois diagnostica,
só então sugere mudanças — e mostra como comprovar o ganho. Otimização sem
medição é chute.

## Quando usar

- Uma consulta está lenta e o usuário quer deixá-la mais rápida
- Pedem para revisar índices de uma tabela
- Dúvida entre duas formas de escrever a mesma consulta

## Como fazer

### 1. Meça antes de mexer

No SQLite, ative o cronômetro e veja o plano de execução **antes** de
qualquer mudança:

```bash
sqlite3 banco.db ".timer on" "SELECT * FROM pedidos WHERE cliente_id = 42;"
sqlite3 banco.db "EXPLAIN QUERY PLAN SELECT * FROM pedidos WHERE cliente_id = 42;"
```

O `.timer on` mostra quanto tempo a consulta levou (na mesma chamada do
`sqlite3`). O `EXPLAIN QUERY PLAN` mostra se o banco usa um índice
(`USING INDEX`) ou varre a tabela inteira (`SCAN`). `SCAN` em tabela grande
é o primeiro suspeito de lentidão. Anote o tempo — ele é a sua linha de base.

### 2. Procure os problemas clássicos

- **Falta de índice**: colunas usadas em `WHERE`, `JOIN` ou `ORDER BY` sem
  índice correspondente.
- **`SELECT *`**: traz colunas que ninguém usa; prefira listar só as
  necessárias.
- **Função sobre coluna indexada**: `WHERE UPPER(nome) = 'ANA'` invalida o
  índice — o banco precisa calcular a função para cada linha.
- **`LIKE '%texto'`**: curinga no início impede o uso de índice.
- **Subconsulta correlacionada**: roda uma vez por linha da consulta externa;
  muitas vezes vira um `JOIN`.
- **N+1 disfarçado**: a aplicação roda uma query por item em vez de uma só.
- **`OR` em colunas diferentes**: dificulta o uso de índices; às vezes dois
  `SELECT` com `UNION` saem mais baratos.

### 3. Sugira mudanças concretas

- **Criar índice** (a correção mais comum):
  ```sql
  CREATE INDEX idx_pedidos_cliente ON pedidos(cliente_id);
  ```
  Índice composto segue a ordem do `WHERE`: filtre primeiro pela coluna de
  maior seletividade.
- **Reescrever a consulta**: trocar subconsulta correlacionada por `JOIN`,
  evitar `SELECT *`, mover a função para o outro lado da comparação
  (`WHERE nome = 'ana'` com coluna normalizada, em vez de `UPPER(nome)`).
- **Paginar**: `LIMIT`/`OFFSET` para listagens; para tabelas muito grandes,
  paginação por chave (`WHERE id > :ultimo_id`) evita o custo crescente do
  `OFFSET`.
- **Cuidado com excesso de índices**: cada índice acelera leitura e
  encarece escrita. Não crie índice "por via das dúvidas".

### 4. Comprove o ganho

Rode o `EXPLAIN QUERY PLAN` e o cronômetro **depois** da mudança e compare
com a linha de base do passo 1. Entregue os dois números lado a lado.
Se não melhorou, diga isso — e desfaça a mudança.

## Exemplos

Pedido: "esta consulta demora 3 segundos, como melhorar?"

```sql
SELECT * FROM pedidos WHERE UPPER(status) = 'PENDENTE' ORDER BY criado_em;
```

Diagnóstico:

1. `EXPLAIN QUERY PLAN` mostra `SCAN pedidos` — varredura completa, nenhum
   índice aproveitado.
2. Dois problemas: `UPPER(status)` invalida qualquer índice sobre `status`,
   e `SELECT *` traz todas as colunas.
3. Sugestão:
   ```sql
   CREATE INDEX idx_pedidos_status_criado ON pedidos(status, criado_em);
   ```
   ```sql
   SELECT id, cliente_id, total, criado_em
   FROM pedidos
   WHERE status = 'PENDENTE'
   ORDER BY criado_em;
   ```
   (Assume que `status` já é gravado normalizado em maiúsculas — se não for,
   normalize na escrita, não na leitura.)
4. Meça de novo e reporte: "de 3,0 s para 0,04 s; plano mudou de SCAN para
   USING INDEX".
