# 📚 Documentação - Manuvi

Bem-vindo à documentação completa do **Manuvi** - Plataforma Inteligente de Planejamento de Produção.

## 🗂️ Índice da Documentação

### 📋 Visão Geral
- **[README Principal](../README.md)** - Introdução ao projeto e overview geral
- **[Arquitetura](./ARCHITECTURE.md)** - Visão arquitetural e princípios técnicos
- **[Guia de Desenvolvimento](./DEVELOPMENT.md)** - Como configurar e desenvolver

### 🛠️ Desenvolvimento
- **[API Documentation](./API.md)** - Documentação completa da API REST
- **[Banco de Dados](./DATABASE.md)** - Esquema, modelos e estrutura de dados
- **[Autenticação](./AUTH.md)** - Sistema de login, cadastro e recuperação de senha

### 🎯 Recursos Específicos
- **[Planejamento de Produção](./PLANNING.md)** - Algoritmos e lógica de planejamento *(em desenvolvimento)*
- **[Multi-tenant](./MULTITENANCY.md)** - Arquitetura multi-inquilino *(em desenvolvimento)*
- **[Relatórios](./REPORTS.md)** - Sistema de relatórios e dashboards *(em desenvolvimento)*

### 🧪 Testes e Qualidade
- **[Testes](./TESTING.md)** - Estratégias de teste e cobertura *(em desenvolvimento)*
- **[Deploy](./DEPLOYMENT.md)** - Processo de deploy e CI/CD *(em desenvolvimento)*
- **[Monitoramento](./MONITORING.md)** - Métricas e observabilidade *(em desenvolvimento)*

## 🚀 Início Rápido

### Para Desenvolvedores
1. Clone o repositório
2. Siga o **[Guia de Desenvolvimento](./DEVELOPMENT.md)**
3. Configure o ambiente conforme a **[Arquitetura](./ARCHITECTURE.md)**
4. Consulte a **[API Documentation](./API.md)** para integração

### Para Usuários
1. Acesse a aplicação em `http://localhost:3000`
2. Utilize as credenciais demo: `admin@manuvi.com.br` / `admin123`
3. Explore as funcionalidades através do **[Sistema de Autenticação](./AUTH.md)**

## 🏗️ Estrutura do Projeto

```
manuvi/
├── frontend/              # Aplicação Next.js
│   ├── src/
│   │   ├── app/          # Páginas (App Router)
│   │   └── components/   # Componentes reutilizáveis
│   └── docs/             # Documentação específica do frontend
├── backend/              # API Python
│   ├── src/              # Código fonte
│   └── docs/             # Documentação específica do backend
├── docs/                 # Documentação geral
│   ├── ARCHITECTURE.md
│   ├── API.md
│   ├── DATABASE.md
│   ├── AUTH.md
│   └── DEVELOPMENT.md
└── README.md
```

## 🎯 Público-Alvo

### 👨‍💻 Desenvolvedores
- **Frontend**: React, Next.js, TypeScript
- **Backend**: Python, FastAPI, PostgreSQL
- **DevOps**: Docker, Kubernetes, CI/CD

### 🏢 Gestores de Produto
- **Visão**: Arquitetura e roadmap
- **Funcionalidades**: Recursos e capacidades
- **Integração**: APIs e conectividade

### 🔧 Administradores
- **Instalação**: Setup e configuração
- **Manutenção**: Monitoramento e troubleshooting
- **Segurança**: Políticas e controles

## 💡 Como Contribuir

### 📝 Documentação
1. Identifique gaps na documentação
2. Crie issues para melhorias
3. Envie pull requests com atualizações
4. Siga o padrão de markdown estabelecido

### 🔧 Código
1. Consulte o **[Guia de Desenvolvimento](./DEVELOPMENT.md)**
2. Siga os padrões de código definidos
3. Adicione testes para novas funcionalidades
4. Atualize a documentação relevante

### 🐛 Bugs e Melhorias
1. Reporte bugs com reprodução clara
2. Sugira melhorias com justificativa
3. Priorize com base no impacto
4. Teste thoroughly antes de submeter

## 📞 Suporte - Planejado

### 🔗 Links Úteis
- **GitHub**: https://github.com/gabrielalmir/manuvi
- **Documentação Online**: https://docs.manuvi.com.br
- **Demo**: https://demo.manuvi.com.br
- **Status**: https://status.manuvi.com.br

### 💬 Comunidade
- **GitHub Discussions**: https://github.com/gabrielalmir/manuvi/discussions
- **LinkedIn**: https://linkedin.com/in/gabrielalmir

### 🎯 Objetivos
- Manter 90%+ de cobertura
- Atualizar semanalmente
- Feedback < 24h
- Qualidade consistente

## 🔄 Versionamento

### Convenções
- **Semantic Versioning**: MAJOR.MINOR.PATCH
- **Documentação**: Versionada junto com código
- **Breaking Changes**: Claramente documentados
- **Migration Guides**: Para versões major
