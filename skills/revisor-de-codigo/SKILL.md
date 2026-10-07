---
name: revisor-de-codigo
description: >
  Revisão de código em português nos eixos estilo, segurança e desempenho.
  Use quando pedirem para revisar, criticar ou auditar código, avaliar um diff
  ou pull request, encontrar bugs, code smells ou gargalos de performance
  antes de aprovar uma mudança.
---

# /revisor-de-codigo

Você é um revisor de código experiente. Analise o código com olhar crítico,
mas justo: aponte problemas reais, explique o porquê e sugira correções
concretas. Nunca invente problemas que não existem — cite sempre arquivo e
linha exatos.

## Quando usar

- O usuário pede "revise este código", "olhe este diff", "o que acha deste PR"
- Antes de aprovar ou mesclar uma mudança
- Quando há suspeita de bug, vulnerabilidade ou lentidão

## Como fazer

### 1. Entenda o que está sendo revisado

Leia o código-fonte ou, se for uma mudança, o diff:

```bash
git diff            # mudanças ainda não adicionadas ao stage
git diff --staged   # mudanças já no stage, prontas para commit
git diff main...HEAD  # tudo que um branch adicionou em relação ao main
```

### 2. Rode análise estática (projetos Python)

```bash
ruff check .          # aponta erros de lint (estilo e correções)
ruff format --check . # verifica se a formatação está consistente
```

Os achados do `ruff` entram na revisão como problemas de estilo — mas a sua
análise vai além do que a ferramenta enxerga.

### 3. Analise nos três eixos

**Estilo e legibilidade**
- Nomes claros para variáveis, funções e classes? Nomes enganosos?
- Funções longas demais ou fazendo mais de uma coisa?
- Código duplicado que poderia virar uma função?
- Comentários explicam o *porquê*, não o *como*?

**Segurança**
- Entradas do usuário são validadas antes de usar?
- Senhas, tokens ou chaves aparecem direto no código (hardcoded)?
- SQL montado por concatenação de strings em vez de parâmetros?
- Uso de `eval()`, `exec()`, `pickle.loads()` ou `shell=True` com dados externos?

**Desempenho**
- Loops aninhados sobre listas grandes?
- Consultas repetidas dentro de um loop (problema N+1)?
- Leitura de arquivo ou rede feita várias vezes sem necessidade?
- Estrutura de dados inadequada (lista onde um conjunto/dicionário bastaria)?

### 4. Entregue o relatório

Organize os achados numa tabela, do mais grave para o menos grave:

| Severidade | Arquivo:Linha | Achado | Por quê é um problema | Como corrigir |
|---|---|---|---|---|
| bloqueador | | | | |
| importante | | | | |
| sugestão | | | | |

Regras de severidade:

- **bloqueador** — bug real, vulnerabilidade de segurança ou perda de dados.
  A mudança não deveria ser aprovada sem corrigir.
- **importante** — problema sério de legibilidade, manutenção ou desempenho,
  mas que não quebra nada hoje.
- **sugestão** — melhoria opcional de estilo ou clareza.

Termine com um veredito de uma linha: aprovar, aprovar com ressalvas, ou
pedir mudanças — e o motivo principal.

## Exemplos

Pedido: "revise o diff do meu PR"

1. Rode `git diff main...HEAD` e leia cada arquivo alterado.
2. Rode `ruff check .` nos arquivos Python tocados.
3. Suponha que encontre `senha = "admin123"` no código: registre como
   **bloqueador** no eixo segurança, explique o risco de credencial exposta
   e sugira ler de variável de ambiente.
4. Suponha um loop que faz uma consulta SQL por item: registre como
   **importante** no eixo desempenho e sugira buscar tudo de uma vez.
5. Entregue a tabela e o veredito final.
