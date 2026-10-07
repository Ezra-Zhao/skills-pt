---
name: mensagem-de-commit
description: >
  Escreve mensagens de commit no padrão Conventional Commits em português,
  analisando o git diff. Use quando pedirem para criar, sugerir ou melhorar
  uma mensagem de commit, ou quando o usuário disser "faz o commit" e a
  mensagem ainda não existe.
---

# /mensagem-de-commit

Você escreve mensagens de commit claras e padronizadas. Nunca invente o
conteúdo da mudança: derive tudo do diff real.

## Quando usar

- O usuário pede "crie uma mensagem de commit", "sugira um commit"
- O usuário diz "faz o commit" sem especificar a mensagem
- Uma mensagem de commit existente está confusa e precisa melhorar

## Como fazer

### 1. Descubra o que mudou

```bash
git status --short   # quais arquivos foram tocados
git diff --staged     # o conteúdo exato que vai para o commit
```

Se não houver nada no stage, avise o usuário em vez de inventar uma mensagem.
Se houver mudanças fora do stage, mencione que elas ficarão de fora do commit.

### 2. Identifique o tipo da mudança

Use o tipo do Conventional Commits que melhor descreve o diff:

| Tipo | Quando usar |
|---|---|
| `feat` | funcionalidade nova |
| `fix` | correção de bug |
| `docs` | só documentação |
| `style` | formatação, sem mudança de comportamento |
| `refactor` | reestruturação interna, sem mudar comportamento |
| `perf` | melhoria de desempenho |
| `test` | adição ou ajuste de testes |
| `build` | sistema de build ou dependências |
| `ci` | pipeline de integração contínua |
| `chore` | tarefas de manutenção que não se encaixam acima |
| `revert` | desfaz um commit anterior |

### 3. Monte a mensagem

Formato:

```text
tipo(escopo): descrição curta no imperativo
```

Regras:

- **Descrição em português**, no imperativo: "adiciona", "corrige", "remove".
- **Até 72 caracteres** na primeira linha.
- **Sem ponto final** no fim da primeira linha.
- **Tudo em minúsculas** (inclusive o tipo).
- O **escopo** é opcional, mas ajuda: nome do módulo ou área afetada.
- Um commit, um tipo: se o diff mistura duas coisas não relacionadas, sugira
  dividir em dois commits em vez de forçar uma mensagem só.
- Se a mudança precisa de contexto, adicione um corpo separado por uma linha
  em branco, explicando o **porquê** (não o *como* — o *como* está no diff).

### 4. Confirme antes de commitar

Mostre a mensagem proposta e pergunte se pode prosseguir. Só rode
`git commit` depois do aval do usuário.

## Exemplos

Diff que adiciona renovação automática de token no módulo de autenticação:

```text
feat(auth): adiciona renovação automática de token
```

Diff que corrige cálculo de frete que ignorava o CEP de destino:

```text
fix(frete): corrige cálculo que ignorava o CEP de destino

O valor era calculado só com o peso porque o CEP não era repassado
para a função de cotação. Agora o endereço completo entra no cálculo.
```

Diff que só ajusta indentação em vários arquivos:

```text
style: padroniza indentação para 4 espaços
```
