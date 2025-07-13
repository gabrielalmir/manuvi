# :hammer_and_wrench: Development Guide – Manuvi

## :information_source: Current Status

- **Front-end** – Ready for local development. Uses mock data and mock authentication only.
- **Back-end** – Contains domain models; API endpoints and persistence are under construction.

## :pushpin: Prerequisites

- Node.js 18+, npm 9+
- Python 3.11+
- Git
- VS Code (recommended)

Refer to the original Portuguese sections below for full commands and file structure.

## :rocket: Initial Setup

### 1. Clone the Repository
```bash
git clone https://github.com/gabrielalmir/manuvi.git
cd manuvi
```

### 2. Configure the Frontend
```bash
cd frontend
npm install
```

### 3. Configure the Backend
```bash
cd ../backend
python -m venv venv

# Windows
venv\Scripts\activate

# macOS/Linux
source venv/bin/activate

pip install -r requirements.txt
```

### 4. Environment Variables

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

## :arrow_forward: Execution

### Frontend (Development)
```bash
cd frontend
npm run dev
```
Access: http://localhost:3000

### Backend (Development)
```bash
cd backend
uvicorn main:app --reload --port 8000
```
API available at: http://localhost:8000

### Run Tests

#### Frontend
```bash
cd frontend

# Run all tests
npm test

# Run in watch mode
npm run test:watch

# Run with coverage
npm run test:coverage
```

#### Backend
```bash
cd backend
pytest

# With coverage
pytest --cov=src --cov-report=html
```

## 📁 Development Structure

### Frontend
```
frontend/
├── src/
│   ├── app/              # Routes (Next.js App Router)
│   │   ├── layout.tsx    # Root Layout
│   │   ├── page.tsx      # Home Page
│   │   ├── globals.css   # Global Styles
│   │   └── [module]/     # Pages by module
│   ├── components/       # Reusable Components
│   │   ├── ui/          # Base UI Components
│   │   ├── forms/       # Forms
│   │   └── layout/      # Layout Components
│   ├── lib/             # Utilities and configurations
│   ├── store/           # State Management (Zustand)
│   └── types/           # TypeScript Type Definitions
├── public/              # Static Assets
├── __tests__/           # Tests
└── docs/                # Specific Documentation
```

### Backend
```
backend/
├── src/
│   ├── core/            # Central Configurations
│   ├── models/          # Data Models
│   ├── schemas/         # Pydantic Schemas
│   ├── services/        # Business Logic
│   ├── repositories/    # Data Layer
│   ├── controllers/     # HTTP Controllers
│   └── utils/           # Utilities
├── tests/               # Tests
├── migrations/          # Database Migrations
└── requirements.txt     # Python Dependencies
```

## 🔧 Code Standards

### Frontend (TypeScript/React)

#### Components
```typescript
// Functional Component with TypeScript
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

#### Custom Hooks
```typescript
// hooks/useProducts.ts
import { useState, useEffect } from 'react';

export function useProducts() {
  const [products, setProducts] = useState([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    // Search logic
  }, []);

  return { products, loading };
}
```

#### Global State (Zustand)
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

#### Models
```python
# models/product.py
from pydantic import BaseModel
from datetime import datetime

class Product(BaseModel):
    id: int
    name: str
    production_time: int  # in minutes
    setup_time: int      # in minutes
    batch_size: int
    created_at: datetime
    updated_at: datetime
```

#### Services
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

#### Controllers
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

## 🧪 Tests

### Frontend (Jest + React Testing Library)

#### Component Test
```typescript
// __tests__/components/ProductForm.test.tsx
import { render, screen, fireEvent } from '@testing-library/react';
import { ProductForm } from '@/components/ProductForm';

describe('ProductForm', () => {
  it('should render form fields', () => {
    render(<ProductForm onSubmit={jest.fn()} />);

    expect(screen.getByLabelText(/name/i)).toBeInTheDocument();
    expect(screen.getByLabelText(/production time/i)).toBeInTheDocument();
  });

  it('should call onSubmit with form data', () => {
    const onSubmit = jest.fn();
    render(<ProductForm onSubmit={onSubmit} />);

    fireEvent.change(screen.getByLabelText(/name/i), {
      target: { value: 'Test Product' }
    });

    fireEvent.click(screen.getByRole('button', { name: /save/i }));

    expect(onSubmit).toHaveBeenCalledWith({
      name: 'Test Product'
    });
  });
});
```

### Backend (pytest)

#### Service Test
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

## 🔄 Development Flow

### 1. Create Branch
```bash
git checkout -b feature/new-feature
```

### 2. Make Changes
- Implement code
- Add tests
- Update documentation

### 3. Run Tests
```bash
# Frontend
cd frontend && npm test

# Backend
cd backend && pytest
```

### 4. Commit and Push
```bash
git add .
git commit -m "feat: add new feature"
git push origin feature/new-feature
```

### 5. Create Pull Request
- Use PR template
- Include detailed description
- Tag reviewers

## 📊 Debugging

### Frontend
```typescript
// Use React DevTools
console.log('Debug info:', { state, props });

// Use breakpoints in VSCode
debugger;
```

### Backend
```python
# Use pdb for debugging
import pdb; pdb.set_trace()

# Structured logs
import logging
logging.info("Processing request", extra={"user_id": user.id})
```

## 🔧 Useful Tools

### Linting and Formatting
```bash
# Frontend
npm run lint
npm run format

# Backend
black src/
isort src/
flake8 src/
```

### Code Analysis
```bash
# TypeScript
npx tsc --noEmit

# Python
mypy src/
```

## 📈 Performance

### Frontend
- Use `React.memo` for components
- Implement `useMemo` and `useCallback`
- Optimize images with Next.js Image

### Backend
- Use async/await
- Implement Redis cache
- Optimize SQL queries

## 🐛 Troubleshooting

### Common Issues

#### Frontend
```bash
# Clear Next.js cache
rm -rf .next

# Reinstall node_modules
rm -rf node_modules
npm install
```

#### Backend
```bash
# Recreate virtual environment
rm -rf venv
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

### Logs
- Frontend: Browser DevTools Console
- Backend: Terminal or log file
- Database: Specific PostgreSQL logs

## 🎯 Next Steps

1. **Configure CI/CD**
2. **Implement Docker**
3. **Configure Monitoring**
4. **Document APIs**
5. **Create storybook**

## 🆘 Support

- **Issues**: Use GitHub Issues
- **Discussions**: GitHub Discussions
- **Documentation**: `/docs` folder
- **Contact**: gabr.almir@gmail.com

## :warning: Note
This guide reflects the realistic state of the project, with parts implemented and others planned. Refer to the referenced files for details.
