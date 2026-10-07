---
name: auditoria-de-seguranca
description: >
  Auditoria básica de segurança de código guiada pelo OWASP Top 10, em
  português. Use quando pedirem para auditar, verificar vulnerabilidades,
  fazer security review, checar segurança antes de publicar, ou quando o
  usuário disser "isso é seguro?" sobre um trecho de código.
---

# /auditoria-de-seguranca

Você faz uma auditoria de segurança honesta e priorizada: encontra falhas
reais, classifica a gravidade e explica como corrigir. Não crie pânico com
achados teóricos — cada vulnerabilidade apontada precisa de arquivo, linha
e caminho de exploração plausível.

## Quando usar

- O usuário pede "audite a segurança", "tem vulnerabilidade aqui?"
- Antes de publicar uma aplicação ou abrir um repositório
- Depois de um incidente, para caçar falhas da mesma família

## Como fazer

### 1. Defina o escopo

Pergunte ou deduza: qual código auditar (pasta, módulo), qual linguagem,
a aplicação é exposta à internet? Sem escopo, a auditoria vira lista genérica.

### 2. Rode varredura automatizada (projetos Python)

```bash
pip install bandit
bandit -r ./src/                              # varre o código recursivamente
bandit -r ./src/ -f json -o bandit-report.json  # relatório em JSON para análise
bandit -r ./src/ --severity-level=high          # mostra só achados de alta severidade
```

Cheque também as dependências:

```bash
pip audit   # aponta pacotes com vulnerabilidades conhecidas
```

Ferramenta não substitui revisão manual: ela aponta padrões suspeitos, mas
só você julga se o dado ali é realmente controlável por um atacante.

### 3. Percorra o OWASP Top 10 (edição 2021, a mais citada)

Use como checklist, adaptando ao código em análise:

| Código | Categoria | O que procurar |
|---|---|---|
| A01 | Controle de acesso quebrado | Falta de checagem de permissão; IDs de recurso manipuláveis na URL (IDOR) |
| A02 | Falhas criptográficas | Senhas em texto puro ou hash fraco (MD5/SHA1); dados sensíveis sem TLS |
| A03 | Injeção | SQL concatenado; `eval`/`exec`; comandos de shell com entrada do usuário |
| A04 | Design inseguro | Regra de negócio que confia no cliente (preço vindo do front-end) |
| A05 | Má configuração | Debug ligado em produção; cabeçalhos de segurança ausentes; erros verbosos |
| A06 | Componentes vulneráveis | Bibliotecas desatualizadas com CVE conhecida (`pip audit` ajuda aqui) |
| A07 | Falhas de autenticação | Login sem limite de tentativas; sessão sem expiração; senha fraca aceita |
| A08 | Falhas de integridade | Desserialização insegura (`pickle`); atualização sem verificação |
| A09 | Falhas de log e monitoramento | Ações sensíveis sem registro; logs que vazam dados pessoais |
| A10 | SSRF | URL fornecida pelo usuário sendo buscada pelo servidor sem validação |

> Nota: o OWASP publicou uma edição 2025 que reorganiza essas categorias
> (ex.: cadeia de suprimentos de software ganha destaque próprio). Se o time
> do usuário já adota a edição 2025, use os nomes dela — o conteúdo da
> auditoria não muda.

### 4. Entregue o relatório

Tabela priorizada por severidade:

| Severidade | Categoria OWASP | Arquivo:Linha | Achado | Como explorar | Correção |
|---|---|---|---|---|---|
| crítica | | | | | |
| alta | | | | | |
| média | | | | | |
| baixa | | | | | |

Para cada achado crítico ou alto, inclua um trecho de código vulnerável e a
versão corrigida lado a lado. Termine com um resumo executivo de três a
cinco linhas: quantos achados por severidade e os dois ou três pontos que
merecem atenção imediata.

## Exemplos

Pedido: "audite a segurança do módulo de login em `src/auth/`"

1. Rode `bandit -r ./src/auth/` e `pip audit`.
2. Revise manualmente: como a senha é armazenada? Há limite de tentativas?
   O token de sessão expira? Erros de login revelam se o usuário existe?
3. Suponha que encontre senha comparada em texto puro: registre como
   **crítica**, categoria A02, mostre o trecho e sugira hash com bcrypt
   ou argon2.
4. Entregue a tabela completa e o resumo executivo.
