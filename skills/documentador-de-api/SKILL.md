---
name: documentador-de-api
description: >
  Gera documentação de APIs REST no padrão OpenAPI em português. Use quando
  pedirem para documentar endpoints, criar ou atualizar um arquivo openapi.yaml,
  descrever contratos de API, ou quando o usuário disser "documente esta API".
---

# /documentador-de-api

Você transforma código de API em documentação OpenAPI clara e completa.
Documentação boa responde, para cada endpoint: o que faz, o que recebe,
o que devolve e o que pode dar errado.

## Quando usar

- O usuário pede "documente esta API" ou "gere o OpenAPI"
- Existe código de API sem documentação (ou com documentação desatualizada)
- Precisa descrever o contrato de uma API para outro time consumir

## Como fazer

### 1. Mapeie a API no código

Percorra o código (rotas, controllers, handlers) e levante para cada endpoint:

- Método HTTP e caminho (`GET /pedidos/{id}`)
- Parâmetros de caminho, de query e cabeçalhos
- Corpo da requisição: formato e campos (obrigatórios e opcionais)
- Respostas: códigos de status e formato de cada uma
- Autenticação exigida (token, chave de API, nenhuma)

Não invente endpoints: documente só o que existe no código.

### 2. Escreva o arquivo `openapi.yaml`

Use o padrão OpenAPI 3.1. Estrutura mínima:

```yaml
openapi: 3.1.0
info:
  title: Nome da API
  version: 1.0.0
  description: O que esta API faz, em português
paths:
  /pedidos/{id}:
    get:
      summary: Busca um pedido pelo identificador
      parameters:
        - name: id
          in: path
          required: true
          schema:
            type: string
      responses:
        '200':
          description: Pedido encontrado
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Pedido'
        '404':
          description: Pedido não encontrado
components:
  schemas:
    Pedido:
      type: object
      required: [id, total]
      properties:
        id:
          type: string
        total:
          type: number
```

Regras de qualidade:

- Todo `summary` e `description` em português claro e direto.
- Todo parâmetro de caminho marcado como `required: true`.
- Toda resposta de erro que o código realmente retorna deve aparecer
  (404, 400, 401...), não só o caminho feliz.
- Modelos reutilizáveis vão em `components/schemas`, referenciados com
  `$ref` — nada de repetir o mesmo objeto em vários endpoints.
- Inclua **exemplos** de requisição e resposta nos endpoints principais:
  exemplo vale mais que parágrafo.

### 3. Valide a estrutura

Confira a indentação do YAML com atenção — um espaço a menos quebra o
arquivo inteiro. Se o projeto já tem um `openapi.yaml`, atualize-o em vez
de criar outro, mantendo o mesmo estilo.

### 4. Sugira como visualizar

Um `openapi.yaml` bem escrito pode ser aberto no Swagger UI ou no Redoc
para gerar uma página de documentação interativa — mencione isso ao
entregar.

## Exemplos

Pedido: "documente a API de pedidos em `src/api/pedidos.py`"

1. Leia o arquivo e liste os endpoints: `POST /pedidos`, `GET /pedidos/{id}`,
   `DELETE /pedidos/{id}`.
2. Para cada um, anote parâmetros, corpo, status de retorno e autenticação.
3. Gere `openapi.yaml` com os três paths, schemas `Pedido` e
   `NovoPedido` em `components`, exemplos de payload e todas as respostas
   de erro que o código retorna.
4. Entregue o arquivo e um resumo: quantos endpoints, quais schemas,
   e o que ficou de fora (se algum endpoint não pôde ser documentado,
   diga qual e por quê).
