# 🔌 API Documentation - Manuvi

## Visão Geral

A API do Manuvi é uma RESTful API construída com FastAPI que fornece endpoints para gerenciamento de produção industrial. Todas as operações são multi-tenant e requerem autenticação.

## 🔐 Autenticação

### JWT Token
```http
Authorization: Bearer <jwt_token>
```

### Headers Obrigatórios
```http
Content-Type: application/json
X-Tenant-ID: {tenant_id}
Authorization: Bearer {jwt_token}
```

## 📋 Endpoints

### 🏢 Autenticação

#### Login
```http
POST /auth/login
Content-Type: application/json

{
  "email": "usuario@empresa.com",
  "password": "senha123"
}
```

**Resposta (200 OK):**
```json
{
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9...",
  "token_type": "bearer",
  "expires_in": 3600,
  "user": {
    "id": 1,
    "email": "usuario@empresa.com",
    "name": "João Silva",
    "role": "manager"
  }
}
```

#### Refresh Token
```http
POST /auth/refresh
Authorization: Bearer {refresh_token}
```

### 👥 Usuários

#### Listar Usuários
```http
GET /users
Authorization: Bearer {token}
X-Tenant-ID: {tenant_id}
```

**Resposta (200 OK):**
```json
{
  "users": [
    {
      "id": 1,
      "email": "usuario@empresa.com",
      "name": "João Silva",
      "role": "manager",
      "active": true,
      "created_at": "2024-01-01T00:00:00Z",
      "updated_at": "2024-01-01T00:00:00Z"
    }
  ],
  "total": 1,
  "page": 1,
  "per_page": 10
}
```

#### Criar Usuário
```http
POST /users
Content-Type: application/json
Authorization: Bearer {token}
X-Tenant-ID: {tenant_id}

{
  "email": "novo@empresa.com",
  "name": "Maria Santos",
  "role": "operator",
  "password": "senha123"
}
```

### 🏭 Produtos

#### Listar Produtos
```http
GET /products
Authorization: Bearer {token}
X-Tenant-ID: {tenant_id}
```

**Query Parameters:**
- `page`: Página (padrão: 1)
- `per_page`: Itens por página (padrão: 10)
- `search`: Busca por nome
- `active`: Filtrar por status (true/false)

**Resposta (200 OK):**
```json
{
  "products": [
    {
      "id": 1,
      "name": "Produto A",
      "code": "PA001",
      "production_time": 60,
      "setup_time": 30,
      "batch_size": 100,
      "unit": "pcs",
      "active": true,
      "created_at": "2024-01-01T00:00:00Z",
      "updated_at": "2024-01-01T00:00:00Z"
    }
  ],
  "total": 1,
  "page": 1,
  "per_page": 10
}
```

#### Criar Produto
```http
POST /products
Content-Type: application/json
Authorization: Bearer {token}
X-Tenant-ID: {tenant_id}

{
  "name": "Produto Novo",
  "code": "PN001",
  "production_time": 45,
  "setup_time": 20,
  "batch_size": 50,
  "unit": "pcs"
}
```

#### Atualizar Produto
```http
PUT /products/{id}
Content-Type: application/json
Authorization: Bearer {token}
X-Tenant-ID: {tenant_id}

{
  "name": "Produto Atualizado",
  "production_time": 50
}
```

#### Deletar Produto
```http
DELETE /products/{id}
Authorization: Bearer {token}
X-Tenant-ID: {tenant_id}
```

### ⚙️ Máquinas

#### Listar Máquinas
```http
GET /machines
Authorization: Bearer {token}
X-Tenant-ID: {tenant_id}
```

**Resposta (200 OK):**
```json
{
  "machines": [
    {
      "id": 1,
      "name": "Máquina 01",
      "code": "M001",
      "capacity_per_day": 480,
      "shifts": [
        {
          "id": 1,
          "name": "Turno 1",
          "start_time": "06:00",
          "end_time": "14:00"
        }
      ],
      "active": true,
      "created_at": "2024-01-01T00:00:00Z",
      "updated_at": "2024-01-01T00:00:00Z"
    }
  ],
  "total": 1,
  "page": 1,
  "per_page": 10
}
```

#### Criar Máquina
```http
POST /machines
Content-Type: application/json
Authorization: Bearer {token}
X-Tenant-ID: {tenant_id}

{
  "name": "Máquina Nova",
  "code": "MN001",
  "capacity_per_day": 600,
  "shifts": [
    {
      "name": "Turno 1",
      "start_time": "06:00",
      "end_time": "14:00"
    }
  ]
}
```

### 📦 Pedidos

#### Listar Pedidos
```http
GET /orders
Authorization: Bearer {token}
X-Tenant-ID: {tenant_id}
```

**Query Parameters:**
- `status`: Filtrar por status (pending, in_progress, completed)
- `priority`: Filtrar por prioridade (low, medium, high, urgent)
- `date_from`: Data inicial (YYYY-MM-DD)
- `date_to`: Data final (YYYY-MM-DD)

**Resposta (200 OK):**
```json
{
  "orders": [
    {
      "id": 1,
      "code": "ORD001",
      "customer_name": "Cliente A",
      "due_date": "2024-02-01",
      "priority": "high",
      "status": "pending",
      "items": [
        {
          "id": 1,
          "product_id": 1,
          "product_name": "Produto A",
          "quantity": 500,
          "unit_price": 10.50
        }
      ],
      "total_amount": 5250.00,
      "created_at": "2024-01-01T00:00:00Z",
      "updated_at": "2024-01-01T00:00:00Z"
    }
  ],
  "total": 1,
  "page": 1,
  "per_page": 10
}
```

#### Criar Pedido
```http
POST /orders
Content-Type: application/json
Authorization: Bearer {token}
X-Tenant-ID: {tenant_id}

{
  "code": "ORD002",
  "customer_name": "Cliente B",
  "due_date": "2024-02-15",
  "priority": "medium",
  "items": [
    {
      "product_id": 1,
      "quantity": 200,
      "unit_price": 12.00
    }
  ]
}
```

### 📊 Planejamento

#### Gerar Plano de Produção
```http
POST /planning/generate
Content-Type: application/json
Authorization: Bearer {token}
X-Tenant-ID: {tenant_id}

{
  "start_date": "2024-02-01",
  "end_date": "2024-02-29",
  "order_ids": [1, 2, 3],
  "machine_ids": [1, 2],
  "strategy": "priority_first"
}
```

**Resposta (200 OK):**
```json
{
  "plan_id": "plan_20240201_001",
  "start_date": "2024-02-01",
  "end_date": "2024-02-29",
  "strategy": "priority_first",
  "allocations": [
    {
      "machine_id": 1,
      "machine_name": "Máquina 01",
      "tasks": [
        {
          "order_id": 1,
          "product_id": 1,
          "product_name": "Produto A",
          "quantity": 500,
          "start_time": "2024-02-01T06:00:00Z",
          "end_time": "2024-02-01T14:00:00Z",
          "duration": 480
        }
      ]
    }
  ],
  "summary": {
    "total_orders": 3,
    "completed_orders": 2,
    "pending_orders": 1,
    "total_production_time": 1440,
    "efficiency": 85.5
  },
  "created_at": "2024-01-01T00:00:00Z"
}
```

#### Listar Planos
```http
GET /planning/plans
Authorization: Bearer {token}
X-Tenant-ID: {tenant_id}
```

#### Visualizar Plano
```http
GET /planning/plans/{plan_id}
Authorization: Bearer {token}
X-Tenant-ID: {tenant_id}
```

### 📈 Relatórios

#### Relatório de Produção
```http
GET /reports/production
Authorization: Bearer {token}
X-Tenant-ID: {tenant_id}
```

**Query Parameters:**
- `start_date`: Data inicial (YYYY-MM-DD)
- `end_date`: Data final (YYYY-MM-DD)
- `machine_id`: Filtrar por máquina
- `product_id`: Filtrar por produto

**Resposta (200 OK):**
```json
{
  "period": {
    "start_date": "2024-01-01",
    "end_date": "2024-01-31"
  },
  "summary": {
    "total_orders": 50,
    "completed_orders": 45,
    "total_production_time": 21600,
    "total_setup_time": 3600,
    "efficiency": 87.5
  },
  "by_machine": [
    {
      "machine_id": 1,
      "machine_name": "Máquina 01",
      "orders_completed": 25,
      "production_time": 12000,
      "efficiency": 90.0
    }
  ],
  "by_product": [
    {
      "product_id": 1,
      "product_name": "Produto A",
      "quantity_produced": 5000,
      "production_time": 8000
    }
  ]
}
```

## 🔄 Códigos de Status

### Sucesso
- `200 OK`: Operação realizada com sucesso
- `201 Created`: Recurso criado com sucesso
- `204 No Content`: Operação realizada sem conteúdo de resposta

### Erro do Cliente
- `400 Bad Request`: Dados inválidos
- `401 Unauthorized`: Token não fornecido ou inválido
- `403 Forbidden`: Acesso negado
- `404 Not Found`: Recurso não encontrado
- `409 Conflict`: Conflito (ex: código já existe)
- `422 Unprocessable Entity`: Dados não podem ser processados

### Erro do Servidor
- `500 Internal Server Error`: Erro interno do servidor
- `503 Service Unavailable`: Serviço indisponível

## 📋 Formato de Erro

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Dados inválidos fornecidos",
    "details": [
      {
        "field": "email",
        "message": "Email é obrigatório"
      }
    ]
  }
}
```

## 🔍 Paginação

### Request
```http
GET /products?page=2&per_page=20
```

### Response
```json
{
  "data": [...],
  "pagination": {
    "page": 2,
    "per_page": 20,
    "total": 100,
    "total_pages": 5,
    "has_next": true,
    "has_prev": true
  }
}
```

## 📊 Rate Limiting

### Limites
- **Autenticação**: 10 tentativas por minuto
- **Geral**: 100 requests por minuto
- **Relatórios**: 20 requests por minuto

### Headers de Resposta
```http
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 85
X-RateLimit-Reset: 1640995200
```

## 🔧 Webhooks

### Configuração
```http
POST /webhooks
Content-Type: application/json

{
  "url": "https://sua-app.com/webhook",
  "events": ["order.created", "plan.generated"],
  "secret": "webhook_secret"
}
```

### Eventos Disponíveis
- `order.created`: Novo pedido criado
- `order.updated`: Pedido atualizado
- `plan.generated`: Plano de produção gerado
- `machine.status_changed`: Status da máquina alterado

## 📱 Versionamento

### Header de Versão
```http
API-Version: v1
```

### Versões Disponíveis
- `v1`: Versão atual (padrão)

## 🛠️ Ferramentas de Desenvolvimento

### Swagger UI
Acesse: `http://localhost:8000/docs`

### Redoc
Acesse: `http://localhost:8000/redoc`

### Postman Collection
Disponível em: `/docs/postman/manuvi-api.json`

## 📚 Exemplos de Uso

### Fluxo Completo
```bash
# 1. Fazer login
curl -X POST http://localhost:8000/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email": "user@company.com", "password": "password"}'

# 2. Criar produto
curl -X POST http://localhost:8000/products \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer TOKEN" \
  -H "X-Tenant-ID: company1" \
  -d '{"name": "Produto Teste", "production_time": 60}'

# 3. Criar pedido
curl -X POST http://localhost:8000/orders \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer TOKEN" \
  -H "X-Tenant-ID: company1" \
  -d '{"code": "ORD001", "items": [{"product_id": 1, "quantity": 100}]}'

# 4. Gerar plano
curl -X POST http://localhost:8000/planning/generate \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer TOKEN" \
  -H "X-Tenant-ID: company1" \
  -d '{"order_ids": [1], "machine_ids": [1]}'
```

## 🔒 Segurança

### CORS
```
Access-Control-Allow-Origin: https://app.manuvi.com.br
Access-Control-Allow-Methods: GET, POST, PUT, DELETE
Access-Control-Allow-Headers: Content-Type, Authorization, X-Tenant-ID
```

### Headers de Segurança
```
X-Frame-Options: DENY
X-Content-Type-Options: nosniff
X-XSS-Protection: 1; mode=block
Strict-Transport-Security: max-age=31536000; includeSubDomains
```

## 🆘 Suporte

Para dúvidas sobre a API:
- 📧 Email: api-support@manuvi.com.br
- 📖 Documentação: https://docs.manuvi.com.br
- 🐛 Issues: https://github.com/manuvi/manuvi/issues
