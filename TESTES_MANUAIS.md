
# Testes Manuais — API de Gerenciamento de Pedidos

**Aluno:** Diogo Henrique Giordano  
**RA:** 41607554

## Objetivo

Documentar os testes realizados no Postman para verificar o funcionamento das rotas da API.

## Ambiente

- Node.js e Express
- Postman Desktop
- Endereço: http://localhost:3000

## Testes realizados

| Teste | Método | Endpoint | HTTP esperado |
|---|---|---|---|
| Cadastro | POST | /pedidos | 201 |
| Validação de valor inválido | POST | /pedidos | 400 |
| Listagem | GET | /pedidos | 200 |
| Consulta inexistente | GET | /pedidos/999999 | 404 |
| Atualização de status | PATCH | /pedidos/:id/status | 200 |

## Dados para cadastro

```json
{
  "cliente": "Carlos Silva",
  "tipo": "entrega",
  "valor": 350,
  "prioridade": "normal"
}
```

## Teste de validação

Alterar o campo `valor` para `0`. A API deve retornar HTTP 400.

## Teste de atualização

Cadastrar um pedido e utilizar seu ID na requisição PATCH:

```json
{
  "status": "em_preparacao"
}
```

A resposta esperada é HTTP 200, com o status atualizado.

## Evidências

As capturas estão armazenadas na pasta `evidencias/`:

- 01-criacao.png
- 02-validacao.png
- 03-listagem.png
- 04-consulta-inexistente.png
- 05-atualizacao.png

## Testes automatizados

Comando: `npm test`

Resultado da execução local: 30 testes aprovados, nenhuma falha.
