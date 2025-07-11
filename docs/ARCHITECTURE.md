# 🏗️ Arquitetura do Manuvi

## Visão Geral

O **Manuvi** é uma plataforma de planejamento de produção inteligente construída com uma arquitetura moderna e escalável. O projeto utiliza uma abordagem full-stack com separação clara entre frontend e backend.

## 🎯 Princípios Arquiteturais

### 1. **Separação de Responsabilidades**
- Frontend: Interface do usuário e experiência
- Backend: Lógica de negócio e processamento de dados
- Banco de dados: Persistência e consultas

### 2. **Multi-tenant por Design**
- Isolamento completo entre diferentes empresas
- Contexto de tenant em todas as operações
- Segurança e privacidade de dados

### 3. **Escalabilidade Horizontal**
- Arquitetura preparada para microserviços
- APIs RESTful stateless
- Cache distribuído

## 🏢 Estrutura de Alto Nível

```
manuvi/
├── frontend/          # Aplicação Next.js
├── backend/           # API Python
├── docs/              # Documentação
└── README.md          # Documentação principal
```

## 🎨 Frontend (Next.js)

### Stack Tecnológico
- **Framework**: Next.js 14 (App Router)
- **Linguagem**: TypeScript
- **Estilização**: Tailwind CSS
- **Gerenciamento de Estado**: Zustand
- **Ícones**: Lucide React
- **Animações**: Framer Motion
- **Testes**: Jest + React Testing Library

### Arquitetura do Frontend

```
src/
├── app/                    # App Router (Next.js 14)
│   ├── layout.tsx          # Layout principal
│   ├── page.tsx            # Página inicial
│   ├── globals.css         # Estilos globais
│   ├── dashboard/          # Dashboard
│   ├── products/           # Gestão de produtos
│   ├── machines/           # Gestão de máquinas
│   ├── orders/             # Gestão de pedidos
│   ├── planning/           # Planejamento de produção
│   ├── reports/            # Relatórios
│   └── team/               # Gestão de equipe
├── components/             # Componentes reutilizáveis
│   └── layout/             # Componentes de layout
│       ├── AppLayout.tsx   # Layout da aplicação
│       ├── Sidebar.tsx     # Barra lateral
│       └── Topbar.tsx      # Barra superior
└── __tests__/              # Testes unitários
```

### Padrões de Desenvolvimento

#### 1. **Componentes**
- Componentes funcionais com TypeScript
- Props tipadas com interfaces
- Hooks para estado e efeitos colaterais

#### 2. **Roteamento**
- App Router do Next.js 14
- Rotas dinâmicas para entidades
- Layouts aninhados

#### 3. **Estado Global**
- Zustand para gerenciamento de estado
- Contexto de tenant
- Estado reativo

## 🐍 Backend (Python)

### Stack Tecnológico
- **Linguagem**: Python 3.11+
- **Framework**: FastAPI (planejado)
- **Banco de Dados**: PostgreSQL (planejado)
- **Cache**: Redis (planejado)
- **Validação**: Pydantic
- **Migrations**: Alembic (planejado)

### Arquitetura do Backend

```
backend/
├── src/
│   ├── core/               # Configurações e utilitários
│   ├── models/             # Modelos de dados
│   ├── schemas/            # Schemas Pydantic
│   ├── services/           # Lógica de negócio
│   ├── repositories/       # Camada de dados
│   ├── controllers/        # Controladores HTTP
│   └── utils/              # Utilitários
├── tests/                  # Testes
├── migrations/             # Migrações do banco
└── main.py                 # Ponto de entrada
```

### Clean Architecture

O backend segue os princípios da Clean Architecture:

1. **Entities**: Modelos de domínio
2. **Use Cases**: Lógica de negócio
3. **Adapters**: Interfaces externas
4. **Infrastructure**: Implementações concretas

## 🏗️ Fluxo de Dados

### 1. **Requisição HTTP**
```
Cliente → Next.js → Backend API → Banco de Dados
```

### 2. **Resposta**
```
Banco de Dados → Backend API → Next.js → Cliente
```

### 3. **Processamento de Planejamento**
```
Dados de Entrada → Algoritmo de Planejamento → Plano Otimizado → Visualização
```

## 🔐 Segurança

### Frontend
- Validação de entrada
- Sanitização de dados
- Headers de segurança
- Proteção CSRF

### Backend
- Autenticação JWT
- Autorização baseada em roles
- Validação de schemas
- Rate limiting

## 🚀 Deployment

### Frontend
- Build estático do Next.js
- CDN para assets estáticos
- Serverless functions

### Backend
- Containerização com Docker
- Orquestração com Kubernetes
- Auto-scaling horizontal

## 📊 Monitoramento

### Métricas
- Performance da aplicação
- Uso de recursos
- Erros e exceções
- Tempo de resposta

### Logs
- Logs estruturados
- Correlação de traces
- Alertas automáticos

## 🔄 Integração Contínua

### Pipeline
1. **Testes**: Unitários e integração
2. **Build**: Compilação e empacotamento
3. **Deploy**: Automático para staging/produção
4. **Monitoring**: Verificação de saúde

## 🎯 Próximos Passos

1. **Backend API**: Implementar endpoints RESTful
2. **Banco de Dados**: Configurar PostgreSQL
3. **Algoritmos**: Implementar lógica de planejamento
4. **Integrações**: APIs externas (ERPs)
5. **Mobile**: Aplicativo mobile (React Native)

## 📈 Escalabilidade

### Horizontal
- Load balancers
- Múltiplas instâncias
- Microserviços

### Vertical
- Otimização de queries
- Cache inteligente
- Compressão de dados

## 🛠️ Ferramentas de Desenvolvimento

### Frontend
- ESLint + Prettier
- Husky (Git hooks)
- Storybook (componentes)

### Backend
- Black (formatação)
- isort (imports)
- pytest (testes)
- mypy (type checking)

## 📚 Documentação

### API
- OpenAPI/Swagger
- Postman collections
- Exemplos de uso

### Código
- Docstrings
- Comentários inline
- README por módulo
