# API de Gerenciamento de Pedidos

**Projeto Prático Avaliativo — Tópicos Avançados em Sistemas de Informação II**

**Aluno:** Diogo Henrique Giordano  
**RA:** 41607554

## 1. Descrição do projeto

API REST desenvolvida em JavaScript, utilizando Node.js e Express, para gerenciamento de pedidos de uma pequena loja.

O sistema permite cadastrar pedidos, listar os pedidos registrados, consultar um pedido pelo identificador e atualizar seu status, respeitando as regras de negócio.

O projeto utiliza arquitetura em camadas, validação de dados, tratamento centralizado de erros e testes automatizados com Jest e Supertest.

Os pedidos são armazenados em memória, sem utilização de banco de dados. Por esse motivo, os registros são apagados quando o servidor é reiniciado.

## 2. Tecnologias utilizadas

- Node.js
- JavaScript
- Express
- Jest
- Supertest
- Postman
- GitHub

## 3. Instalação e execução

**Requisito:** Node.js 18 ou superior.

Para instalar as dependências:

```bash
npm install
```

Para iniciar o servidor:

```bash
npm start
```

A API estará disponível, por padrão, em:

`http://localhost:3000`

Para executar os testes automatizados:

```bash
npm test
```

A porta pode ser alterada utilizando a variável de ambiente `PORT`.

## 4. Funcionalidades e rotas

| Método | Endpoint | Funcionalidade | Sucesso |
|---|---|---|---|
| POST | `/pedidos` | Cadastrar pedido | 201 |
| GET | `/pedidos` | Listar pedidos | 200 |
| GET | `/pedidos/:id` | Consultar pedido por ID | 200 |
| PATCH | `/pedidos/:id/status` | Atualizar status | 200 |

A API também retorna respostas de erro adequadas, incluindo:

- **400 Bad Request:** dados inválidos ou transição de status não permitida.
- **404 Not Found:** pedido ou rota inexistente.
- **500 Internal Server Error:** erro inesperado no servidor.

## 5. Cadastro de pedidos

**Método:** POST  
**Endpoint:** `/pedidos`

Exemplo de requisição:

```json
{
  "cliente": "Carlos Silva",
  "tipo": "entrega",
  "valor": 350,
  "prioridade": "normal"
}
```

Exemplo de resposta HTTP 201:

```json
{
  "id": 1,
  "cliente": "Carlos Silva",
  "tipo": "entrega",
  "valor": 350,
  "prioridade": "normal",
  "status": "recebido"
}
```

O identificador é gerado automaticamente e todo novo pedido recebe o status inicial `recebido`.

## 6. Listagem de pedidos

**Método:** GET  
**Endpoint:** `/pedidos`

Retorna um array com todos os pedidos cadastrados.

Caso não existam pedidos, a resposta será:

```json
[]
```

## 7. Consulta de pedidos

**Método:** GET  
**Endpoint:** `/pedidos/:id`

Exemplo:

`GET /pedidos/1`

Retorna o pedido correspondente ao identificador informado.

Caso o pedido não exista, será retornado HTTP 404.

Exemplo:

```json
{
  "erro": "Pedido com id 999 não encontrado."
}
```

Identificadores inválidos, como letras ou números não positivos, geram HTTP 400.

## 8. Atualização de status

**Método:** PATCH  
**Endpoint:** `/pedidos/:id/status`

Exemplo:

```json
{
  "status": "em_preparacao"
}
```

O sistema verifica a existência do pedido, a validade do status e se a transição solicitada é permitida.

### Status disponíveis

- `recebido`
- `em_preparacao`
- `enviado`
- `entregue`
- `cancelado`

### Transições permitidas

| Status atual | Próximos status |
|---|---|
| recebido | em_preparacao, cancelado |
| em_preparacao | enviado, cancelado |
| enviado | entregue |
| entregue | Estado final |
| cancelado | Estado final |

As transições impedem alterações incoerentes no fluxo de atendimento.

Pedidos entregues ou cancelados não podem ter o status alterado novamente.

## 9. Validação dos dados

O sistema realiza validações na camada de serviços, impedindo registros com informações inválidas.

| Campo | Regra |
|---|---|
| cliente | Texto obrigatório, não vazio após remoção dos espaços nas extremidades |
| tipo | `entrega` ou `retirada` |
| valor | Número finito maior que zero |
| prioridade | `baixa`, `normal`, `alta` ou `urgente` |
| status | Um dos cinco status permitidos |
| id | Número inteiro positivo |

O sistema também rejeita JSON malformado.

## 10. Arquitetura do projeto

A aplicação foi organizada em camadas, separando o tratamento das requisições HTTP das regras de negócio.

```text
src/
├── server.js
├── app.js
├── routes/
│   └── pedidoRoutes.js
├── controllers/
│   └── pedidoController.js
├── services/
│   └── pedidoService.js
├── middlewares/
│   └── errorHandler.js
└── errors/
    └── AppError.js

tests/
└── pedidos.test.js

evidencias/
```

### Responsabilidades

**Routes:** definem os métodos HTTP e os endpoints, encaminhando as requisições aos controllers.

**Controllers:** recebem os dados das requisições, acionam os serviços e retornam respostas HTTP.

**Services:** executam as validações, controlam o armazenamento em memória e aplicam as regras de negócio.

**Middlewares:** realizam o tratamento centralizado de erros e retornam respostas padronizadas.

**Errors:** definem classes específicas para erros de validação e recursos não encontrados.

O fluxo principal é:

`Routes → Controllers → Services → Resposta HTTP`

Essa separação facilita a manutenção, a organização e a realização de testes.

## 11. Testes automatizados

Os testes foram implementados utilizando Jest e Supertest.

Para executá-los:

```bash
npm test
```

Resultado obtido na execução local:

```text
Test Suites: 1 passed, 1 total
Tests:       30 passed, 30 total
Snapshots:   0 total
Time:        7.26 s
Ran all test suites.
```

Foram aprovados os 33 testes automatizados existentes.

Conforme a documentação do projeto, os cenários cobrem cadastro, validações de entrada, listagem, consulta, identificadores inválidos, atualização de status, transições proibidas e tratamento de erros HTTP.

## 12. Evidências de funcionamento

Os testes manuais são realizados utilizando o Postman para enviar requisições HTTP à aplicação.

A pasta `evidencias/` foi prevista para armazenar as capturas de tela:

- `01-criacao.png` — cadastro de pedido com HTTP 201.
- `02-validacao.png` — rejeição de dados inválidos com HTTP 400.
- `03-listagem.png` — listagem de pedidos com HTTP 200.
- `04-consulta-inexistente.png` — consulta de pedido inexistente com HTTP 404.
- `05-atualizacao.png` — atualização do status com HTTP 200.

As imagens devem ser incluídas na pasta antes da entrega final.

## 13. Desenvolvimento e utilização de inteligência artificial

O projeto foi desenvolvido em contexto de colaboração acadêmica e utilizado como base para esta entrega individual.

A implementação compartilhada teve
