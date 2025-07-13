# :page_facing_up: Documentação - Manuvi

Bem-vindo à documentação do **Manuvi** - Plataforma Inteligente de Planejamento de Produção.

## :card_index_dividers: Índice da Documentação

### :bookmark_tabs: Visão Geral
- **[README Principal](../README.md)**
- **[Arquitetura](./ARCHITECTURE.md)**
- **[Guia de Desenvolvimento](./DEVELOPMENT.md)**

### :hammer_and_wrench: Desenvolvimento
- **[API Documentation](./API.md)**
- **[Banco de Dados](./DATABASE.md)**
- **[Autenticação](./AUTH.md)**

## :information_source: Estado Atual do Projeto

### Frontend
- Estrutura Next.js 14 (App Router), TypeScript, Tailwind, Zustand ([DEVELOPMENT.md](./DEVELOPMENT.md), [ARCHITECTURE.md](./ARCHITECTURE.md)).
- Páginas principais implementadas: autenticação (mock), dashboard, produtos, máquinas, ordens, planejamento, relatórios, equipe ([ARCHITECTURE.md](./ARCHITECTURE.md)).
- Autenticação e dados são simulados/local, sem integração real com backend ([AUTH.md](./AUTH.md)).
- Testes unitários previstos, cobertura real pode ser limitada ([DEVELOPMENT.md](./DEVELOPMENT.md)).

### Backend
- Estrutura Python, modelagem de entidades com dataclasses ([ARCHITECTURE.md](./ARCHITECTURE.md)).
- FastAPI, PostgreSQL, Redis, Alembic estão planejados, mas não necessariamente implementados ([ARCHITECTURE.md](./ARCHITECTURE.md), [DEVELOPMENT.md](./DEVELOPMENT.md)).
- Endpoints RESTful e autenticação JWT descritos, mas podem não estar 100% funcionais ([API.md](./API.md), [AUTH.md](./AUTH.md)).
- Banco de dados e lógica de negócio podem estar parcialmente implementados ([DATABASE.md](./DATABASE.md)).
- Testes e deploy automatizado são planejados ([DEVELOPMENT.md](./DEVELOPMENT.md)).

## :warning: Observação
Este projeto é open source para fins de portfólio e aprendizado. Futuras versões podem se tornar privadas/comerciais. Veja [README principal](../README.md) para detalhes de licenciamento.

## :busts_in_silhouette: Público-Alvo
- Desenvolvedores: React, Next.js, TypeScript, Python, FastAPI ([ARCHITECTURE.md](./ARCHITECTURE.md))
- Gestores de Produto: arquitetura e roadmap ([ARCHITECTURE.md](./ARCHITECTURE.md))
- Administradores: setup e configuração ([DEVELOPMENT.md](./DEVELOPMENT.md))

## :memo: Como Contribuir
Consulte [DEVELOPMENT.md](./DEVELOPMENT.md) para instruções e padrões.

## :link: Suporte
Para dúvidas ou licenciamento comercial, entre em contato via [LinkedIn](https://linkedin.com/in/gabrielalmir).

```
