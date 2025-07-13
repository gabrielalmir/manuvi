# :hammer_and_wrench: Guia de Desenvolvimento - Manuvi

## :information_source: Estado Atual

- O frontend está funcional para navegação, autenticação simulada e visualização de páginas principais ([ARCHITECTURE.md](./ARCHITECTURE.md), [README.md](../README.md)).
- O backend possui modelagem de entidades, mas integrações reais com banco, autenticação JWT e endpoints RESTful podem estar incompletos ([API.md](./API.md), [DATABASE.md](./DATABASE.md)).

## :pushpin: Pré-requisitos

### Sistema Operacional
- Windows 10/11, macOS 10.15+, ou Linux (Ubuntu 20.04+)

### Ferramentas Necessárias
- **Node.js**: 18.x ou superior
- **npm**: 9.x ou superior (vem com Node.js)
- **Python**: 3.11 ou superior
- **Git**: Para controle de versão
- **VSCode**: Editor recomendado

### Extensões VSCode Recomendadas
```json
{
  "recommendations": [
    "ms-vscode.vscode-typescript-next",
    "bradlc.vscode-tailwindcss",
    "esbenp.prettier-vscode",
    "dbaeumer.vscode-eslint",
    "ms-python.python",
    "ms-python.flake8",
    "ms-python.black-formatter",
    "orta.vscode-jest",
    "ms-vscode.test-adapter-converter"
  ]
}
```

## :rocket: Configuração Inicial

### 1. Clonar o Repositório
```bash
git clone https://github.com/seu-usuario/manuvi.git
cd manuvi
```

### 2. Configurar o Frontend
```bash
cd frontend
npm install
```

### 3. Configurar o Backend
```bash
cd ../backend
python -m venv venv

# Windows
venv\Scripts\activate

# macOS/Linux
source venv/bin/activate

pip install -r requirements.txt
```

### 4. Variáveis de Ambiente

#### Frontend (.env.local)
```env
NEXT_PUBLIC_API_URL=http://localhost:8000
NEXT_PUBLIC_APP_NAME=Manuvi
NEXT_PUBLIC_TENANT_ID=demo
```

#### Backend (.env)
```env
DATABASE_URL=postgresql://user:password@localhost:5432/manuvi
REDIS_URL=redis://localhost:6379
JWT_SECRET=your-jwt-secret-here
ENVIRONMENT=development
```

## :arrow_forward: Execução

### Frontend (Development)
```bash
cd frontend
npm run dev
```
Acesse: http://localhost:3000

### Backend (Development)
```bash
cd backend
uvicorn main:app --reload --port 8000
```
API disponível em: http://localhost:8000

### Executar Testes

#### Frontend
```bash
cd frontend

# Executar todos os testes
npm test

# Executar em modo watch
npm run test:watch

# Executar com coverage
npm run test:coverage
```

#### Backend
```bash
cd backend
pytest

# Com coverage
pytest --cov=src --cov-report=html
```

## 📁 Estrutura de Desenvolvimento

### Frontend
```
frontend/
├── src/
│   ├── app/              # Rotas (Next.js App Router)
│   │   ├── layout.tsx    # Layout raiz
│   │   ├── page.tsx      # Página inicial
│   │   ├── globals.css   # Estilos globais
│   │   └── [módulo]/     # Páginas por módulo
│   ├── components/       # Componentes reutilizáveis
│   │   ├── ui/          # Componentes de UI base
│   │   ├── forms/       # Formulários
│   │   └── layout/      # Componentes de layout
│   ├── lib/             # Utilitários e configurações
│   ├── store/           # Gerenciamento de estado (Zustand)
│   └── types/           # Definições de tipos TypeScript
├── public/              # Assets estáticos
├── __tests__/           # Testes
└── docs/                # Documentação específica
```

### Backend
```
backend/
├── src/
│   ├── core/            # Configurações centrais
│   ├── models/          # Modelos de dados
│   ├── schemas/         # Schemas Pydantic
│   ├── services/        # Lógica de negócio
│   ├── repositories/    # Camada de dados
│   ├── controllers/     # Controladores HTTP
│   └── utils/           # Utilitários
├── tests/               # Testes
├── migrations/          # Migrações do banco
└── requirements.txt     # Dependências Python
```

## 🔧 Padrões de Código

### Frontend (TypeScript/React)

#### Componentes
```typescript
// Componente funcional com TypeScript
interface Props {
  title: string;
  onSubmit: (data: FormData) => void;
}

export function Component({ title, onSubmit }: Props) {
  return (
    <div className="p-4">
      <h1 className="text-2xl font-bold">{title}</h1>
    </div>
  );
}
```

#### Hooks Personalizados
```typescript
// hooks/useProducts.ts
import { useState, useEffect } from 'react';

export function useProducts() {
  const [products, setProducts] = useState([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    // Lógica de busca
  }, []);

  return { products, loading };
}
```

#### Estado Global (Zustand)
```typescript
// store/useStore.ts
import { create } from 'zustand';

interface State {
  tenant: string;
  user: User | null;
  setTenant: (tenant: string) => void;
  setUser: (user: User) => void;
}

export const useStore = create<State>((set) => ({
  tenant: '',
  user: null,
  setTenant: (tenant) => set({ tenant }),
  setUser: (user) => set({ user }),
}));
```

### Backend (Python)

#### Modelos
```python
# models/product.py
from pydantic import BaseModel
from datetime import datetime

class Product(BaseModel):
    id: int
    name: str
    production_time: int  # em minutos
    setup_time: int      # em minutos
    batch_size: int
    created_at: datetime
    updated_at: datetime
```

#### Serviços
```python
# services/product_service.py
from typing import List
from models.product import Product
from repositories.product_repository import ProductRepository

class ProductService:
    def __init__(self, repository: ProductRepository):
        self.repository = repository

    async def create_product(self, product: Product) -> Product:
        return await self.repository.create(product)

    async def get_products(self) -> List[Product]:
        return await self.repository.get_all()
```

#### Controladores
```python
# controllers/product_controller.py
from fastapi import APIRouter, Depends
from services.product_service import ProductService

router = APIRouter(prefix="/products", tags=["products"])

@router.post("/", response_model=Product)
async def create_product(
    product: Product,
    service: ProductService = Depends()
):
    return await service.create_product(product)
```

## 🧪 Testes

### Frontend (Jest + React Testing Library)

#### Teste de Componente
```typescript
// __tests__/components/ProductForm.test.tsx
import { render, screen, fireEvent } from '@testing-library/react';
import { ProductForm } from '@/components/ProductForm';

describe('ProductForm', () => {
  it('should render form fields', () => {
    render(<ProductForm onSubmit={jest.fn()} />);

    expect(screen.getByLabelText(/nome/i)).toBeInTheDocument();
    expect(screen.getByLabelText(/tempo de produção/i)).toBeInTheDocument();
  });

  it('should call onSubmit with form data', () => {
    const onSubmit = jest.fn();
    render(<ProductForm onSubmit={onSubmit} />);

    fireEvent.change(screen.getByLabelText(/nome/i), {
      target: { value: 'Produto Teste' }
    });

    fireEvent.click(screen.getByRole('button', { name: /salvar/i }));

    expect(onSubmit).toHaveBeenCalledWith({
      name: 'Produto Teste'
    });
  });
});
```

### Backend (pytest)

#### Teste de Serviço
```python
# tests/services/test_product_service.py
import pytest
from unittest.mock import AsyncMock
from services.product_service import ProductService
from models.product import Product

@pytest.fixture
def mock_repository():
    return AsyncMock()

@pytest.fixture
def service(mock_repository):
    return ProductService(mock_repository)

@pytest.mark.asyncio
async def test_create_product(service, mock_repository):
    # Arrange
    product = Product(name="Test Product", production_time=60)
    mock_repository.create.return_value = product

    # Act
    result = await service.create_product(product)

    # Assert
    assert result == product
    mock_repository.create.assert_called_once_with(product)
```

## 🔄 Fluxo de Desenvolvimento

### 1. Criar Branch
```bash
git checkout -b feature/nova-funcionalidade
```

### 2. Fazer Alterações
- Implementar código
- Adicionar testes
- Atualizar documentação

### 3. Executar Testes
```bash
# Frontend
cd frontend && npm test

# Backend
cd backend && pytest
```

### 4. Commit e Push
```bash
git add .
git commit -m "feat: adicionar nova funcionalidade"
git push origin feature/nova-funcionalidade
```

### 5. Criar Pull Request
- Usar template de PR
- Incluir descrição detalhada
- Marcar reviewers

## 📊 Debugging

### Frontend
```typescript
// Usar React DevTools
console.log('Debug info:', { state, props });

// Usar breakpoints no VSCode
debugger;
```

### Backend
```python
# Usar pdb para debugging
import pdb; pdb.set_trace()

# Logs estruturados
import logging
logging.info("Processing request", extra={"user_id": user.id})
```

## 🔧 Ferramentas Úteis

### Linting e Formatação
```bash
# Frontend
npm run lint
npm run format

# Backend
black src/
isort src/
flake8 src/
```

### Análise de Código
```bash
# TypeScript
npx tsc --noEmit

# Python
mypy src/
```

## 📈 Performance

### Frontend
- Usar `React.memo` para componentes
- Implementar `useMemo` e `useCallback`
- Otimizar imagens com Next.js Image

### Backend
- Usar async/await
- Implementar cache Redis
- Otimizar queries SQL

## 🐛 Troubleshooting

### Problemas Comuns

#### Frontend
```bash
# Limpar cache do Next.js
rm -rf .next

# Reinstalar node_modules
rm -rf node_modules
npm install
```

#### Backend
```bash
# Recriar ambiente virtual
rm -rf venv
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

### Logs
- Frontend: Browser DevTools Console
- Backend: Terminal ou arquivo de log
- Banco de dados: Logs específicos do PostgreSQL

## 🎯 Próximos Passos

1. **Configurar CI/CD**
2. **Implementar Docker**
3. **Configurar monitoramento**
4. **Documentar APIs**
5. **Criar storybook**

## 🆘 Suporte

- **Issues**: Usar GitHub Issues
- **Discussões**: GitHub Discussions
- **Documentação**: Pasta `/docs`
- **Contato**: gabr.almir@gmail.com

## :warning: Observação
Este guia reflete o estado realista do projeto, com partes implementadas e outras planejadas. Consulte os arquivos referenciados para detalhes.
