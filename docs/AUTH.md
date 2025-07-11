# 🔐 Sistema de Autenticação - Manuvi

## Visão Geral

O sistema de autenticação do Manuvi foi projetado para ser seguro, intuitivo e responsivo. Implementa as melhores práticas de UX e segurança para proporcionar uma experiência fluida aos usuários.

## 🎨 Páginas Disponíveis

### 1. Login (`/auth/login`)
- **Finalidade**: Autenticação de usuários existentes
- **Campos**: Email, senha, lembrar-me
- **Recursos**: Mostrar/ocultar senha, credenciais demo
- **Validação**: Email obrigatório, senha obrigatória
- **Redirecionamento**: Dashboard após login bem-sucedido

### 2. Cadastro (`/auth/register`)
- **Finalidade**: Criação de novas contas
- **Campos**: Nome, email, empresa, senha, confirmar senha, aceitar termos
- **Recursos**: Validação de senha, confirmação de senha
- **Validação**: Campos obrigatórios, senhas coincidentes, mínimo 8 caracteres
- **Feedback**: Tela de sucesso com redirecionamento automático

### 3. Esqueceu a Senha (`/auth/forgot-password`)
- **Finalidade**: Recuperação de senha
- **Campos**: Email
- **Recursos**: Feedback de envio, informações de segurança
- **Validação**: Email obrigatório
- **Segurança**: Não revela se o email existe na base

## 🔧 Recursos Técnicos

### Componentes Reutilizáveis
```typescript
// Layout base para autenticação
<AuthLayout>
  <AuthForm
    title="Título da página"
    subtitle="Subtítulo opcional"
    footer={<ComponenteFooter />}
    info={<ComponenteInfo />}
  >
    {/* Conteúdo do formulário */}
  </AuthForm>
</AuthLayout>
```

### Hooks e Estado
```typescript
// Estado típico do formulário
const [formData, setFormData] = useState({
  email: '',
  password: '',
  // ... outros campos
})

const [isLoading, setIsLoading] = useState(false)
const [error, setError] = useState('')
```

### Validações
- **Email**: Formato válido obrigatório
- **Senha**: Mínimo 8 caracteres
- **Confirmação**: Senhas devem coincidir
- **Termos**: Aceite obrigatório no cadastro

## 🎯 Fluxo de Autenticação

### Login
1. Usuário acessa `/auth/login`
2. Preenche credenciais
3. Sistema valida (simulação)
4. Redirecionamento para `/dashboard`

### Cadastro
1. Usuário acessa `/auth/register`
2. Preenche formulário completo
3. Sistema valida dados
4. Tela de sucesso por 2 segundos
5. Redirecionamento para `/auth/login`

### Recuperação de Senha
1. Usuário acessa `/auth/forgot-password`
2. Informa email
3. Sistema simula envio
4. Tela de confirmação
5. Opção para tentar outro email

## 🔒 Segurança

### Práticas Implementadas
- **Validação client-side**: Feedback imediato
- **Não exposição de dados**: Senhas nunca visíveis em logs
- **Feedback seguro**: Não confirma existência de emails
- **Prevenção de ataques**: Rate limiting (futura implementação)

### Headers de Segurança
```http
X-Frame-Options: DENY
X-Content-Type-Options: nosniff
X-XSS-Protection: 1; mode=block
```

## 🎨 Design System

### Cores
- **Primária**: Teal (#0D9488)
- **Sucesso**: Verde (#10b981)
- **Erro**: Vermelho (#ef4444)
- **Fundo**: Gradiente teal suave

### Componentes
- **Inputs**: Ícones à esquerda, feedback visual
- **Botões**: Estados loading, disabled
- **Cards**: Sombras suaves, bordas arredondadas
- **Animações**: Fade-in e slide-in suaves

### Responsividade
- **Mobile-first**: Design otimizado para celular
- **Breakpoints**: Adaptação para tablet e desktop
- **Touch-friendly**: Alvos de toque adequados

## 📱 Estados da Interface

### Loading
```typescript
{isLoading ? (
  <div className="flex items-center justify-center gap-2">
    <div className="animate-spin rounded-full h-4 w-4 border-b-2 border-white"></div>
    Carregando...
  </div>
) : (
  'Texto do botão'
)}
```

### Erro
```typescript
{error && (
  <div className="flex items-center gap-2 p-3 bg-red-50 border border-red-200 rounded-lg text-red-700">
    <AlertCircle className="h-4 w-4" />
    <span className="text-sm">{error}</span>
  </div>
)}
```

### Sucesso
```typescript
{success && (
  <div className="flex items-center gap-2 p-3 bg-green-50 border border-green-200 rounded-lg text-green-700">
    <CheckCircle className="h-4 w-4" />
    <span className="text-sm">Operação realizada com sucesso!</span>
  </div>
)}
```

## 🚀 Integrações Futuras

### Backend
```typescript
// Exemplo de integração com API real
const handleLogin = async (credentials) => {
  try {
    const response = await fetch('/api/auth/login', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(credentials),
    })

    const data = await response.json()

    if (response.ok) {
      localStorage.setItem('token', data.token)
      router.push('/dashboard')
    } else {
      setError(data.message)
    }
  } catch (error) {
    setError('Erro de conexão')
  }
}
```

### Gerenciamento de Estado
```typescript
// Zustand store para autenticação
interface AuthState {
  user: User | null
  token: string | null
  isAuthenticated: boolean
  login: (token: string, user: User) => void
  logout: () => void
}

export const useAuthStore = create<AuthState>((set) => ({
  user: null,
  token: null,
  isAuthenticated: false,
  login: (token, user) => set({
    token,
    user,
    isAuthenticated: true
  }),
  logout: () => set({
    token: null,
    user: null,
    isAuthenticated: false
  }),
}))
```

## 📊 Métricas e Analytics

### Eventos a Rastrear
- **Login**: Sucesso/falha
- **Cadastro**: Início/conclusão
- **Recuperação**: Tentativas de envio
- **Tempo**: Duração nas páginas

### Implementação
```typescript
// Exemplo com Google Analytics
gtag('event', 'login_success', {
  event_category: 'authentication',
  event_label: 'email_login',
})
```

## 🧪 Testes

### Cenários de Teste
1. **Login com credenciais válidas**
2. **Login com credenciais inválidas**
3. **Cadastro com dados válidos**
4. **Cadastro com senhas diferentes**
5. **Recuperação de senha válida**
6. **Validação de campos obrigatórios**

### Exemplo de Teste
```typescript
describe('Login Page', () => {
  it('should login with valid credentials', async () => {
    render(<LoginPage />)

    fireEvent.change(screen.getByLabelText(/email/i), {
      target: { value: 'admin@manuvi.com' }
    })

    fireEvent.change(screen.getByLabelText(/senha/i), {
      target: { value: 'admin123' }
    })

    fireEvent.click(screen.getByRole('button', { name: /entrar/i }))

    await waitFor(() => {
      expect(mockPush).toHaveBeenCalledWith('/dashboard')
    })
  })
})
```

## 🔮 Melhorias Futuras

### Funcionalidades
- **2FA**: Autenticação em dois fatores
- **OAuth**: Login com Google/GitHub
- **Biometria**: Autenticação biométrica
- **SSO**: Single Sign-On empresarial

### UX/UI
- **Tema escuro**: Alternância de tema
- **Personalização**: Branding por tenant
- **Acessibilidade**: Suporte completo WCAG
- **Offline**: Funcionalidade offline

### Segurança
- **Rate limiting**: Proteção contra ataques
- **Captcha**: Verificação em tentativas excessivas
- **Logs**: Auditoria completa
- **Criptografia**: Dados sensíveis criptografados

## 📞 Suporte

Para questões relacionadas à autenticação:
- **Documentação**: `/docs/AUTH.md`
- **Testes**: `/src/__tests__/auth/`
- **Componentes**: `/src/components/auth/`
- **Páginas**: `/src/app/auth/`

## 🔗 Links Úteis

- [Documentação Next.js](https://nextjs.org/docs)
- [React Hook Form](https://react-hook-form.com/)
- [Zod Validation](https://zod.dev/)
- [NextAuth.js](https://next-auth.js.org/)
- [Auth0 Documentation](https://auth0.com/docs)
