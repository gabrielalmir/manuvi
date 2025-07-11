# 🗄️ Documentação do Banco de Dados - Manuvi

## Visão Geral

O Manuvi utiliza PostgreSQL como banco de dados principal, com Redis para cache e InfluxDB para métricas de performance. A arquitetura é multi-tenant com isolamento completo entre empresas.

## 🏗️ Arquitetura de Dados

### Tecnologias
- **PostgreSQL 15+**: Banco principal
- **Redis 7+**: Cache e sessões
- **InfluxDB 2.0+**: Métricas e time-series

### Estratégia Multi-tenant
- **Isolamento por Schema**: Cada empresa tem seu próprio schema
- **Dados Compartilhados**: Configurações globais em schema `public`
- **Segurança**: Row-level security para isolamento adicional

## 📊 Modelo de Dados

### Estrutura Geral
```sql
-- Schemas por tenant
CREATE SCHEMA IF NOT EXISTS tenant_acme;
CREATE SCHEMA IF NOT EXISTS tenant_fabrica_xyz;

-- Schema público para dados compartilhados
CREATE SCHEMA IF NOT EXISTS public;
```

## 🏢 Entidades Principais

### 1. Empresas (Tenants)
```sql
-- public.companies
CREATE TABLE public.companies (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name VARCHAR(255) NOT NULL,
    slug VARCHAR(100) UNIQUE NOT NULL,
    domain VARCHAR(255),
    subscription_tier VARCHAR(50) DEFAULT 'basic',
    active BOOLEAN DEFAULT true,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Índices
CREATE INDEX idx_companies_slug ON public.companies(slug);
CREATE INDEX idx_companies_domain ON public.companies(domain);
```

### 2. Usuários
```sql
-- {tenant_schema}.users
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    name VARCHAR(255) NOT NULL,
    role VARCHAR(50) DEFAULT 'operator',
    active BOOLEAN DEFAULT true,
    last_login TIMESTAMP WITH TIME ZONE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Índices
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_users_role ON users(role);
CREATE INDEX idx_users_active ON users(active);
```

### 3. Produtos
```sql
-- {tenant_schema}.products
CREATE TABLE products (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name VARCHAR(255) NOT NULL,
    code VARCHAR(100) UNIQUE NOT NULL,
    description TEXT,
    production_time INTEGER NOT NULL, -- em minutos
    setup_time INTEGER DEFAULT 0,     -- em minutos
    batch_size INTEGER DEFAULT 1,
    unit VARCHAR(20) DEFAULT 'pcs',
    active BOOLEAN DEFAULT true,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Índices
CREATE INDEX idx_products_code ON products(code);
CREATE INDEX idx_products_name ON products(name);
CREATE INDEX idx_products_active ON products(active);
```

### 4. Máquinas
```sql
-- {tenant_schema}.machines
CREATE TABLE machines (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name VARCHAR(255) NOT NULL,
    code VARCHAR(100) UNIQUE NOT NULL,
    description TEXT,
    capacity_per_day INTEGER NOT NULL, -- em minutos
    active BOOLEAN DEFAULT true,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Índices
CREATE INDEX idx_machines_code ON machines(code);
CREATE INDEX idx_machines_active ON machines(active);
```

### 5. Turnos
```sql
-- {tenant_schema}.shifts
CREATE TABLE shifts (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    machine_id UUID NOT NULL REFERENCES machines(id) ON DELETE CASCADE,
    name VARCHAR(100) NOT NULL,
    start_time TIME NOT NULL,
    end_time TIME NOT NULL,
    active BOOLEAN DEFAULT true,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Índices
CREATE INDEX idx_shifts_machine_id ON shifts(machine_id);
CREATE INDEX idx_shifts_active ON shifts(active);
```

### 6. Clientes
```sql
-- {tenant_schema}.customers
CREATE TABLE customers (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name VARCHAR(255) NOT NULL,
    email VARCHAR(255),
    phone VARCHAR(50),
    address TEXT,
    active BOOLEAN DEFAULT true,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Índices
CREATE INDEX idx_customers_name ON customers(name);
CREATE INDEX idx_customers_active ON customers(active);
```

### 7. Pedidos
```sql
-- {tenant_schema}.orders
CREATE TABLE orders (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    code VARCHAR(100) UNIQUE NOT NULL,
    customer_id UUID REFERENCES customers(id),
    customer_name VARCHAR(255) NOT NULL,
    due_date DATE NOT NULL,
    priority VARCHAR(20) DEFAULT 'medium',
    status VARCHAR(20) DEFAULT 'pending',
    total_amount DECIMAL(10,2) DEFAULT 0,
    notes TEXT,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Índices
CREATE INDEX idx_orders_code ON orders(code);
CREATE INDEX idx_orders_customer_id ON orders(customer_id);
CREATE INDEX idx_orders_due_date ON orders(due_date);
CREATE INDEX idx_orders_priority ON orders(priority);
CREATE INDEX idx_orders_status ON orders(status);
```

### 8. Itens do Pedido
```sql
-- {tenant_schema}.order_items
CREATE TABLE order_items (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    order_id UUID NOT NULL REFERENCES orders(id) ON DELETE CASCADE,
    product_id UUID NOT NULL REFERENCES products(id),
    quantity INTEGER NOT NULL,
    unit_price DECIMAL(10,2) DEFAULT 0,
    total_price DECIMAL(10,2) GENERATED ALWAYS AS (quantity * unit_price) STORED,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Índices
CREATE INDEX idx_order_items_order_id ON order_items(order_id);
CREATE INDEX idx_order_items_product_id ON order_items(product_id);
```

### 9. Planos de Produção
```sql
-- {tenant_schema}.production_plans
CREATE TABLE production_plans (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name VARCHAR(255) NOT NULL,
    start_date DATE NOT NULL,
    end_date DATE NOT NULL,
    strategy VARCHAR(50) DEFAULT 'priority_first',
    status VARCHAR(20) DEFAULT 'draft',
    generated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    created_by UUID REFERENCES users(id),
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Índices
CREATE INDEX idx_production_plans_start_date ON production_plans(start_date);
CREATE INDEX idx_production_plans_status ON production_plans(status);
```

### 10. Alocações do Plano
```sql
-- {tenant_schema}.plan_allocations
CREATE TABLE plan_allocations (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    plan_id UUID NOT NULL REFERENCES production_plans(id) ON DELETE CASCADE,
    order_id UUID NOT NULL REFERENCES orders(id),
    machine_id UUID NOT NULL REFERENCES machines(id),
    product_id UUID NOT NULL REFERENCES products(id),
    quantity INTEGER NOT NULL,
    start_time TIMESTAMP WITH TIME ZONE NOT NULL,
    end_time TIMESTAMP WITH TIME ZONE NOT NULL,
    duration INTEGER NOT NULL, -- em minutos
    setup_time INTEGER DEFAULT 0,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Índices
CREATE INDEX idx_plan_allocations_plan_id ON plan_allocations(plan_id);
CREATE INDEX idx_plan_allocations_order_id ON plan_allocations(order_id);
CREATE INDEX idx_plan_allocations_machine_id ON plan_allocations(machine_id);
CREATE INDEX idx_plan_allocations_start_time ON plan_allocations(start_time);
```

### 11. Atividades de Produção
```sql
-- {tenant_schema}.production_activities
CREATE TABLE production_activities (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    allocation_id UUID REFERENCES plan_allocations(id),
    order_id UUID NOT NULL REFERENCES orders(id),
    machine_id UUID NOT NULL REFERENCES machines(id),
    product_id UUID NOT NULL REFERENCES products(id),
    operator_id UUID REFERENCES users(id),
    quantity_planned INTEGER NOT NULL,
    quantity_produced INTEGER DEFAULT 0,
    start_time TIMESTAMP WITH TIME ZONE,
    end_time TIMESTAMP WITH TIME ZONE,
    status VARCHAR(20) DEFAULT 'pending',
    notes TEXT,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Índices
CREATE INDEX idx_production_activities_allocation_id ON production_activities(allocation_id);
CREATE INDEX idx_production_activities_status ON production_activities(status);
CREATE INDEX idx_production_activities_start_time ON production_activities(start_time);
```

### 12. Log de Atividades
```sql
-- {tenant_schema}.activity_logs
CREATE TABLE activity_logs (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id),
    entity_type VARCHAR(50) NOT NULL,
    entity_id UUID NOT NULL,
    action VARCHAR(50) NOT NULL,
    old_values JSONB,
    new_values JSONB,
    ip_address INET,
    user_agent TEXT,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Índices
CREATE INDEX idx_activity_logs_user_id ON activity_logs(user_id);
CREATE INDEX idx_activity_logs_entity_type ON activity_logs(entity_type);
CREATE INDEX idx_activity_logs_entity_id ON activity_logs(entity_id);
CREATE INDEX idx_activity_logs_created_at ON activity_logs(created_at);
```

## 🔧 Triggers e Funções

### Atualização Automática de Timestamps
```sql
-- Função para atualizar updated_at
CREATE OR REPLACE FUNCTION update_updated_at_column()
RETURNS TRIGGER AS $$
BEGIN
    NEW.updated_at = NOW();
    RETURN NEW;
END;
$$ language 'plpgsql';

-- Aplicar em todas as tabelas
CREATE TRIGGER update_users_updated_at BEFORE UPDATE ON users
    FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();

CREATE TRIGGER update_products_updated_at BEFORE UPDATE ON products
    FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();

-- ... (repetir para todas as tabelas)
```

### Validação de Dados
```sql
-- Validar horários de turno
CREATE OR REPLACE FUNCTION validate_shift_times()
RETURNS TRIGGER AS $$
BEGIN
    IF NEW.start_time >= NEW.end_time THEN
        RAISE EXCEPTION 'Horário de início deve ser anterior ao fim';
    END IF;
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER validate_shift_times_trigger
    BEFORE INSERT OR UPDATE ON shifts
    FOR EACH ROW EXECUTE FUNCTION validate_shift_times();
```

## 🔐 Segurança

### Row Level Security (RLS)
```sql
-- Habilitar RLS em todas as tabelas
ALTER TABLE users ENABLE ROW LEVEL SECURITY;
ALTER TABLE products ENABLE ROW LEVEL SECURITY;
ALTER TABLE machines ENABLE ROW LEVEL SECURITY;
-- ... (repetir para todas as tabelas)

-- Política de acesso baseada em tenant
CREATE POLICY tenant_isolation ON users
    USING (current_schema() = current_setting('app.current_tenant'));
```

### Roles e Permissões
```sql
-- Role para aplicação
CREATE ROLE manuvi_app WITH LOGIN PASSWORD 'secure_password';

-- Permissões por schema
GRANT USAGE ON SCHEMA tenant_acme TO manuvi_app;
GRANT SELECT, INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA tenant_acme TO manuvi_app;
GRANT USAGE ON ALL SEQUENCES IN SCHEMA tenant_acme TO manuvi_app;
```

## 📊 Índices e Performance

### Índices Compostos
```sql
-- Consultas frequentes
CREATE INDEX idx_orders_status_due_date ON orders(status, due_date);
CREATE INDEX idx_plan_allocations_machine_time ON plan_allocations(machine_id, start_time);
CREATE INDEX idx_production_activities_status_time ON production_activities(status, start_time);
```

### Particionamento
```sql
-- Particionamento por data para logs
CREATE TABLE activity_logs_2024 PARTITION OF activity_logs
FOR VALUES FROM ('2024-01-01') TO ('2025-01-01');
```

## 🔄 Migrations

### Estrutura de Migrations
```sql
-- migrations/001_initial_schema.sql
BEGIN;

-- Criar tabelas básicas
CREATE TABLE users (...);
CREATE TABLE products (...);
-- ...

-- Inserir dados iniciais
INSERT INTO public.companies (name, slug) VALUES ('Demo Company', 'demo');

COMMIT;
```

### Versionamento
```sql
-- public.migrations
CREATE TABLE public.migrations (
    id SERIAL PRIMARY KEY,
    version VARCHAR(50) UNIQUE NOT NULL,
    description TEXT,
    applied_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);
```

## 💾 Backup e Recuperação

### Backup Automático
```bash
# Script de backup diário
#!/bin/bash
TIMESTAMP=$(date +"%Y%m%d_%H%M%S")
pg_dump -h localhost -U manuvi_app -d manuvi > backup_${TIMESTAMP}.sql
```

### Replicação
```sql
-- Configurar replicação streaming
CREATE PUBLICATION manuvi_pub FOR ALL TABLES;
```

## 📈 Monitoramento

### Métricas Importantes
```sql
-- Tamanho das tabelas
SELECT
    schemaname,
    tablename,
    pg_size_pretty(pg_total_relation_size(schemaname||'.'||tablename)) as size
FROM pg_tables
WHERE schemaname NOT IN ('information_schema', 'pg_catalog')
ORDER BY pg_total_relation_size(schemaname||'.'||tablename) DESC;

-- Consultas mais lentas
SELECT query, mean_time, calls
FROM pg_stat_statements
ORDER BY mean_time DESC LIMIT 10;
```

## 🧪 Dados de Teste

### Seed Data
```sql
-- Inserir dados de teste
INSERT INTO users (email, name, role, password_hash) VALUES
('admin@demo.com', 'Admin User', 'admin', '$2b$12$...'),
('operator@demo.com', 'Operator User', 'operator', '$2b$12$...');

INSERT INTO products (name, code, production_time, setup_time) VALUES
('Produto A', 'PA001', 60, 15),
('Produto B', 'PB001', 45, 10);

INSERT INTO machines (name, code, capacity_per_day) VALUES
('Máquina 01', 'M001', 480),
('Máquina 02', 'M002', 600);
```

## 🔧 Manutenção

### Limpeza de Dados
```sql
-- Limpar logs antigos (> 90 dias)
DELETE FROM activity_logs
WHERE created_at < NOW() - INTERVAL '90 days';

-- Vacuum e analyze periódicos
VACUUM ANALYZE;
```

### Otimização
```sql
-- Reindexar tabelas grandes
REINDEX TABLE production_activities;

-- Atualizar estatísticas
ANALYZE;
```

## 📚 Documentação Adicional

### Convenções
- **Nomes**: snake_case para tabelas e colunas
- **Chaves**: UUIDs para todas as entidades
- **Timestamps**: Sempre com timezone
- **Soft Delete**: Usar campo `active` ao invés de DELETE

### Padrões
- **Auditoria**: Todos os CRUDs são logados
- **Versionamento**: Schemas separados por tenant
- **Backup**: Backup completo diário + logs contínuos

### Ferramentas
- **pg_stat_statements**: Monitoramento de queries
- **pgAdmin**: Interface gráfica
- **pg_dump/pg_restore**: Backup/restore
- **pgbench**: Testes de performance

## 🆘 Troubleshooting

### Problemas Comuns
```sql
-- Verificar conexões ativas
SELECT count(*) FROM pg_stat_activity;

-- Verificar locks
SELECT * FROM pg_locks WHERE NOT granted;

-- Verificar tamanho do banco
SELECT pg_size_pretty(pg_database_size('manuvi'));
```

### Contato
- **DBA**: dba@manuvi.com
- **Suporte**: support@manuvi.com
- **Docs**: https://docs.manuvi.com/database
