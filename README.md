# skills-pt

Pacote de **Agent Skills em português** — instruções reutilizáveis que deixam
qualquer agente de IA (Claude Code, Codex, Cursor) mais útil no dia a dia do
desenvolvimento de software. Cada skill vive numa pasta própria com um arquivo
`SKILL.md` no formato padrão de Agent Skills: frontmatter YAML (`name` +
`description`) dizendo **quando** usar, e corpo em Markdown dizendo **como**
fazer.

## Skills incluídas

| Skill | O que faz |
|---|---|
| `revisor-de-codigo` | Revisão de código em três eixos: estilo, segurança e desempenho |
| `mensagem-de-commit` | Mensagens de commit no padrão Conventional Commits, a partir do `git diff` |
| `depurador` | Diagnóstico de erros: lê tracebacks e localiza a causa raiz |
| `gerador-de-testes` | Gera testes `pytest`/`unittest` a partir do código-fonte |
| `documentador-de-api` | Gera documentação de APIs REST no padrão OpenAPI |
| `auditoria-de-seguranca` | Auditoria básica de segurança guiada pelo OWASP Top 10 |
| `otimizador-sql` | Sugestões de otimização de consultas SQL com `EXPLAIN QUERY PLAN` |

## Instalação

Copie as pastas das skills para o diretório de skills do seu agente:

```bash
# Claude Code
cp -r skills/* ~/.claude/skills/

# Codex CLI
cp -r skills/* ~/.codex/skills/

# Cursor — use o diretório de skills configurado no seu Cursor
# (consulte a documentação atual do Cursor para o caminho exato na sua versão)
```

O agente carrega automaticamente a skill quando o seu pedido combina com a
descrição dela — por exemplo, "revise este código" ativa `revisor-de-codigo`,
e "crie uma mensagem de commit" ativa `mensagem-de-commit`. Você também pode
invocar uma skill diretamente pelo nome.

## Estrutura do repositório

```text
skills-pt/
├── README.md
├── LICENSE
├── .gitignore
└── skills/
    ├── revisor-de-codigo/SKILL.md
    ├── mensagem-de-commit/SKILL.md
    ├── depurador/SKILL.md
    ├── gerador-de-testes/SKILL.md
    ├── documentador-de-api/SKILL.md
    ├── auditoria-de-seguranca/SKILL.md
    └── otimizador-sql/SKILL.md
```

## Autor

Feito por **Ezra-Zhao**. Todo o conteúdo foi escrito do zero em português
(clean-room) — nenhum texto foi copiado de outros repositórios de skills.

---

## English summary

**skills-pt** is a Portuguese-native pack of Agent Skills: reusable instruction
files (`SKILL.md`, YAML frontmatter + Markdown body) that make AI coding
agents (Claude Code, Codex, Cursor) more useful for everyday development.
It ships 7 skills: code review, Conventional Commits messages, debugging,
test generation, OpenAPI documentation, OWASP Top 10 security audit, and SQL
optimization. Install by copying the `skills/*` folders into your agent's
skills directory. All content is original Portuguese, written clean-room.
By **Ezra-Zhao** · MIT License.
