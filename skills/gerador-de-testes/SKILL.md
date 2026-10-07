---
name: gerador-de-testes
description: >
  Gera testes automatizados em português para código Python usando pytest ou
  unittest. Use quando pedirem para criar testes, aumentar a cobertura de um
  módulo, ou quando o usuário disser "escreva testes para isso" ou "teste
  esta função".
---

# /gerador-de-testes

Você escreve testes que realmente protegem o código: cobrem o caminho feliz,
os casos de borda e os erros esperados. Teste bom falha quando o código
quebra e passa quando o código está certo — nada de teste que só enfeita.

## Quando usar

- O usuário pede "crie testes para este módulo/função"
- Precisa aumentar a cobertura de testes de um projeto
- Um bug foi corrigido e merece um teste de regressão

## Como fazer

### 1. Identifique o framework do projeto

```bash
pytest --version          # se existir, o projeto usa pytest
ls tests/                # convenção: testes moram na pasta tests/
```

- Com **pytest**: arquivos `test_*.py` ou `*_test.py`, funções `test_*`,
  asserções com `assert` puro.
- Com **unittest** (biblioteca padrão): classes que herdam de
  `unittest.TestCase`, métodos `test_*`, execução com
  `python3 -m unittest discover -s tests`.

Prefira o framework que o projeto já usa. Se não houver nenhum, use pytest.

### 2. Analise o código antes de escrever

Leia a função ou módulo e liste:

- **Caminho feliz**: entradas normais e saídas esperadas.
- **Casos de borda**: listas vazias, zero, `None`, strings vazias, valores
  no limite.
- **Erros esperados**: quais exceções a função deveria lançar, e quando
  (teste com `pytest.raises`).

Não teste detalhes internos de implementação — teste comportamento: dada
uma entrada, a saída (ou o erro) é a esperada?

### 3. Escreva os testes

Convenção de nomes: `test_<o_que>_<condição>_<resultado_esperado>`, em
inglês simples e snake_case (padrão da comunidade Python). Use comentários
ou docstrings em português para explicar o *porquê* de cada caso.

Exemplo para uma função `calcular_desconto(preco, percentual)`:

```python
def test_desconto_percentual_normal():
    # 10% de desconto sobre 200 deve resultar em 180
    assert calcular_desconto(200, 10) == 180

def test_desconto_zero_percentual():
    # sem desconto, o preço não muda
    assert calcular_desconto(200, 0) == 200

def test_desconto_percentual_invalido_lanca_erro():
    # percentual acima de 100 não faz sentido e deve ser rejeitado
    with pytest.raises(ValueError):
        calcular_desconto(200, 150)
```

Para código com efeitos colaterais (arquivos, rede, banco), isole com
mocks — o teste não pode depender de internet ou de estado externo.

### 4. Rode e confira a cobertura

```bash
pytest tests/ -v                       # roda tudo, modo verboso
pytest --cov=app --cov-report=term-missing tests/   # mostra a cobertura por arquivo
```

A meta é cobrir os caminhos importantes, não perseguir 100% a qualquer
custo. Linhas sem cobertura que aparecem no relatório (`term-missing`)
indicam onde ainda faltam casos.

## Exemplos

Pedido: "escreva testes para a função `dividir_conta(valor, pessoas)`"

1. Leia a função e identifique: divisão normal, `pessoas = 0` (divisão por
   zero), valores negativos, arredondamento de centavos.
2. Gere `tests/test_conta.py` com um teste para cada caso, incluindo
   `pytest.raises` para a divisão por zero — ou o comportamento que a função
   documentar para esse caso.
3. Rode `pytest tests/test_conta.py -v` e mostre o resultado.
4. Se o projeto usa unittest em vez de pytest, gere uma classe
   `TestDividirConta(unittest.TestCase)` e valide com
   `python3 -m unittest discover -s tests`.
