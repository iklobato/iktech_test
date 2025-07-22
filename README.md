# Tarefa Técnica - LightAPI Junior

## Objetivo
Implementar um novo modelo e testar a funcionalidade do LightAPI framework. Esta tarefa avalia conhecimentos em Python, SQLAlchemy, APIs REST e Git.

## Instruções

### 1. Setup Inicial (5 min)
- Fork do repositório: https://github.com/iklobato/LightAPI
- Clone seu fork localmente
- Instale as dependências: `pip install -r requirements.txt` (ou `pip install lightapi`)

### 2. Implementação (20 min)

#### Criar um novo modelo `Product`
No diretório do projeto, crie um arquivo `product_api.py` com um modelo SQLAlchemy chamado `Product` que contenha os seguintes campos:

- `id` - chave primária (integer)
- `name` - nome do produto (string, máximo 100 caracteres, obrigatório)
- `description` - descrição (string, máximo 500 caracteres, opcional)
- `price` - preço (float, obrigatório)
- `created_at` - data de criação (datetime, com valor padrão)

Configure o LightAPI para registrar este modelo e executar na porta 8080.

#### Adicionar validação customizada
Implemente uma validação que garanta que o preço seja sempre maior que 0. Se o preço for inválido, deve retornar erro apropriado.

### 3. Teste e Documentação (5 min)
- Execute a aplicação: `python product_api.py`
- Teste os endpoints gerados automaticamente:
  - `GET /products/` - listar produtos
  - `POST /products/` - criar produto
- Crie um arquivo `README_TASK.md` documentando:
  - Como executar sua implementação
  - Quais endpoints estão disponíveis
  - Um exemplo de JSON para criar um produto

### 4. Entrega
- Commit das alterações no seu fork
- Envie o link do seu repositório
- Inclua o `README_TASK.md` com a documentação

## Critérios de Avaliação

**Funcionalidade (40%)**
- O modelo Product está corretamente definido
- A API executa sem erros
- Os endpoints CRUD funcionam

**Código (30%)**
- Código limpo e bem estruturado
- Uso correto do SQLAlchemy
- Implementação da validação

**Git/Documentação (20%)**
- Fork e commits apropriados
- README claro e objetivo
- Instruções funcionais

**Bonus (10%)**
- Testes adicionais dos endpoints
- Tratamento de erros personalizado
- Melhorias na documentação

## Dicas
- Consulte a documentação do LightAPI no README do projeto
- Teste sua implementação localmente antes do commit

**Tempo estimado: 30 minutos**
