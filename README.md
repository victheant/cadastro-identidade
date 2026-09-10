# cadastro-identidade
Documentação do workflow feito no n8n sobre o serviço de cadastros de uma pizzaria. 

# API de Cadastro / Identidade — v1

Serviço responsável pelo cadastro de usuários, validação de token e recuperação do perfil associado ao token.

## 1. Endpoints, métodos e versão

A versão atual da API é **v1.7**.

| Método | Endpoint | Descrição |
|---|---|---|
| GET | `/v1/cadastro/health` | Verifica se o serviço está disponível |
| POST | `/v1/cadastro/usuario` | Cadastra um usuário ou retorna o perfil já associado ao token |
| GET | `/v1/cadastro/perfil` | Valida o token e retorna o perfil associado |

URL base de produção:

```text
https://pzaas.online/webhook
```

---

## 2. Headers obrigatórios

### POST /v1/cadastro/usuario

```text
Content-Type: application/json
x-api-key: turma2026
Authorization: Bearer <token>
```

### GET /v1/cadastro/perfil

```text
Content-Type: application/json
x-api-key: turma2026
Authorization: Bearer <token>
```

O valor de `Authorization` identifica o perfil que será armazenado ou consultado.

Exemplo:

```text
Authorization: Bearer token-prod-002
```

---

## 3. Corpo da requisição e da resposta

### POST /v1/cadastro/usuario

URL:

```text
https://pzaas.online/webhook/v1/cadastro/usuario
```

Os campos `id`, `nome` e `email` são obrigatórios.

O campo `status` é opcional. Caso não seja informado, o serviço utiliza `ativo`.

#### Exemplo de requisição

```json
{
  "id": "usuario-004",
  "nome": "Carlos",
  "email": "carlos@teste.com"
}
```

#### Resposta para novo cadastro — 201 Created

```json
{
  "id": "usuario-004",
  "nome": "Carlos",
  "email": "carlos@teste.com",
  "status": "ativo"
}
```

Se o token já estiver associado a um perfil, o serviço não cria outro cadastro e retorna o perfil existente com `200 OK`.

#### Resposta para perfil já cadastrado — 200 OK

```json
{
  "id": "usuario-004",
  "nome": "Carlos",
  "email": "carlos@teste.com",
  "status": "ativo"
}
```

---

### GET /v1/cadastro/perfil

URL:

```text
https://pzaas.online/webhook/v1/cadastro/perfil
```

Esse endpoint não possui corpo de requisição. O token deve ser enviado no header `Authorization`.

#### Resposta — 200 OK

```json
{
  "id": "usuario-004",
  "nome": "Carlos",
  "email": "carlos@teste.com",
  "status": "ativo"
}
```

---

### GET /v1/cadastro/health

URL:

```text
https://pzaas.online/webhook/v1/cadastro/health
```

Esse endpoint verifica a disponibilidade do serviço.

#### Resposta — 200 OK

```json
{
  "status": "ok",
  "servico": "cadastro-identidade"
}
```

---

## 4. Códigos de erro

### 400 Bad Request

Ocorre no cadastro quando um ou mais campos obrigatórios (`id`, `nome` ou `email`) não são enviados.

Exemplo de requisição inválida:

```json
{
  "id": "usuario-005",
  "nome": "Marina"
}
```

Resposta:

```json
{
  "erro": "Dados obrigatórios ausentes"
}
```

### 401 Unauthorized

Ocorre quando o token está ausente ou não corresponde a um perfil cadastrado.

```json
{
  "erro": "Token inválido ou ausente"
}
```

### 403 Forbidden

Ocorre quando a API Key está ausente ou é inválida.

```json
{
  "erro": "API Key inválida"
}
```

---

## 5. Exemplo de chamada REAL

A chamada abaixo foi realizada no endpoint publicado em produção utilizando o Postman.

### Requisição

```http
GET https://pzaas.online/webhook/v1/cadastro/perfil
```

Headers utilizados:

```text
Content-Type: application/json
x-api-key: turma2026
Authorization: Bearer token-prod-002
```

### Resposta real

Status HTTP:

```text
200 OK
```

Body:

```json
{
  "id": "usuario-004",
  "nome": "Carlos",
  "email": "carlos@teste.com",
  "status": "ativo"
}
```

Também foi realizada uma chamada real utilizando um token não cadastrado:

```text
Authorization: Bearer token-inexistente-999
```

Resposta obtida:

```text
401 Unauthorized
```

```json
{
  "erro": "Token inválido ou ausente"
}
```

---

## URL do serviço de Logger utilizado
https://pzaas.online/webhook/logger241425:v1/logs

### Headers do serviço de Logger

```text
Content-Type: application/json
x-api-key: turma2026
x-pedido-id: cadastro-identidade
```
