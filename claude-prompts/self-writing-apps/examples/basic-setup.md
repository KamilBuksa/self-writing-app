# Basic Setup - Self-Writing Apps

## 🚀 Quick Start Guide

Ten przewodnik pokaże Ci, jak skonfigurować Twój pierwszy projekt Self-Writing Apps od zera do pełnej automatyzacji w 30 minut.

## 📋 Prerequisites

### Required Tools
- **Node.js** 18+ 
- **Git** 
- **VS Code** (recommended)
- **Vercel CLI** (optional, for deployment)

### API Keys
- **v0.dev API Key** (for UI generation)
- **Claude API Key** (for logic implementation)
- **Supabase Project** (for backend)

## 🏗️ Project Setup

### 1. Create Next.js Project
```bash
# Create new Next.js project with TypeScript
npx create-next-app@latest my-self-writing-app \
  --typescript \
  --tailwind \
  --eslint \
  --app \
  --src-dir \
  --import-alias "@/*"

cd my-self-writing-app
```

### 2. Install Dependencies
```bash
# Core dependencies
npm install @supabase/supabase-js
npm install zustand
npm install @hookform/resolvers zod react-hook-form

# UI dependencies (shadcn/ui)
npx shadcn-ui@latest init
npx shadcn-ui@latest add button card input label
npx shadcn-ui@latest add form dialog toast

# Development dependencies
npm install -D @types/node
npm install -D prettier prettier-plugin-tailwindcss
```

### 3. Environment Setup
```bash
# Create environment file
touch .env.local
```

```env
# .env.local
# Supabase
NEXT_PUBLIC_SUPABASE_URL=your_supabase_url
NEXT_PUBLIC_SUPABASE_ANON_KEY=your_supabase_anon_key
SUPABASE_SERVICE_ROLE_KEY=your_service_role_key

# v0.dev (if available)
V0_API_KEY=your_v0_api_key

# Claude Code (if available)
CLAUDE_API_KEY=your_claude_api_key
```

### 4. Supabase Configuration
```sql
-- Create initial tables
-- Run this in Supabase SQL editor

-- Enable RLS
ALTER DATABASE postgres SET "app.jwt_secret" TO 'your-jwt-secret';

-- Create profiles table
CREATE TABLE profiles (
  id UUID REFERENCES auth.users(id) PRIMARY KEY,
  email TEXT UNIQUE NOT NULL,
  name TEXT,
  avatar_url TEXT,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- Enable RLS
ALTER TABLE profiles ENABLE ROW LEVEL SECURITY;

-- Create policies
CREATE POLICY "Users can view own profile" ON profiles 
  FOR SELECT USING (auth.uid() = id);
  
CREATE POLICY "Users can update own profile" ON profiles 
  FOR UPDATE USING (auth.uid() = id);

-- Create function to handle new users
CREATE OR REPLACE FUNCTION handle_new_user()
RETURNS TRIGGER AS $$
BEGIN
  INSERT INTO profiles (id, email, name)
  VALUES (NEW.id, NEW.email, NEW.raw_user_meta_data->>'name');
  RETURN NEW;
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;

-- Create trigger
CREATE TRIGGER on_auth_user_created
  AFTER INSERT ON auth.users
  FOR EACH ROW
  EXECUTE FUNCTION handle_new_user();
```

## 🔧 Project Configuration

### 1. Supabase Client
```typescript
// src/lib/supabase.ts
import { createClientComponentClient, createServerComponentClient } from '@supabase/auth-helpers-nextjs'
import { cookies } from 'next/headers'

export const createClient = () => createClientComponentClient()

export const createServerClient = () => createServerComponentClient({ cookies })

export type Database = {
  public: {
    Tables: {
      profiles: {
        Row: {
          id: string
          email: string
          name: string | null
          avatar_url: string | null
          created_at: string
          updated_at: string
        }
        Insert: {
          id: string
          email: string
          name?: string | null
          avatar_url?: string | null
        }
        Update: {
          email?: string
          name?: string | null
          avatar_url?: string | null
        }
      }
    }
  }
}
```

### 2. Utils Configuration
```typescript
// src/lib/utils.ts
import { clsx, type ClassValue } from 'clsx'
import { twMerge } from 'tailwind-merge'

export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs))
}

export const formatDate = (date: string | Date) => {
  return new Intl.DateTimeFormat('pl-PL', {
    year: 'numeric',
    month: 'long',
    day: 'numeric'
  }).format(new Date(date))
}

export const formatCurrency = (amount: number) => {
  return new Intl.NumberFormat('pl-PL', {
    style: 'currency',
    currency: 'PLN'
  }).format(amount)
}
```

### 3. Auth Store Setup
```typescript
// src/stores/auth-store.ts
import { create } from 'zustand'
import { createClient } from '@/lib/supabase'
import type { User } from '@supabase/supabase-js'

interface AuthState {
  user: User | null
  loading: boolean
  signIn: (email: string, password: string) => Promise<void>
  signUp: (email: string, password: string, name: string) => Promise<void>
  signOut: () => Promise<void>
  initialize: () => Promise<void>
}

export const useAuthStore = create<AuthState>((set, get) => ({
  user: null,
  loading: true,
  
  signIn: async (email: string, password: string) => {
    const supabase = createClient()
    const { error } = await supabase.auth.signInWithPassword({
      email,
      password
    })
    if (error) throw error
  },
  
  signUp: async (email: string, password: string, name: string) => {
    const supabase = createClient()
    const { error } = await supabase.auth.signUp({
      email,
      password,
      options: {
        data: { name }
      }
    })
    if (error) throw error
  },
  
  signOut: async () => {
    const supabase = createClient()
    const { error } = await supabase.auth.signOut()
    if (error) throw error
    set({ user: null })
  },
  
  initialize: async () => {
    const supabase = createClient()
    
    // Get initial user
    const { data: { user } } = await supabase.auth.getUser()
    set({ user, loading: false })
    
    // Listen for auth changes
    supabase.auth.onAuthStateChange((_event, session) => {
      set({ user: session?.user ?? null, loading: false })
    })
  }
}))
```

### 4. Root Layout Setup
```tsx
// src/app/layout.tsx
import type { Metadata } from 'next'
import { Inter } from 'next/font/google'
import './globals.css'
import { AuthProvider } from '@/components/providers/auth-provider'
import { Toaster } from '@/components/ui/toaster'

const inter = Inter({ subsets: ['latin'] })

export const metadata: Metadata = {
  title: 'My Self-Writing App',
  description: 'An app that writes itself',
}

export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="en">
      <body className={inter.className}>
        <AuthProvider>
          {children}
        </AuthProvider>
        <Toaster />
      </body>
    </html>
  )
}
```

### 5. Auth Provider
```tsx
// src/components/providers/auth-provider.tsx
'use client'

import { useEffect } from 'react'
import { useAuthStore } from '@/stores/auth-store'

export function AuthProvider({ children }: { children: React.ReactNode }) {
  const initialize = useAuthStore(state => state.initialize)
  
  useEffect(() => {
    initialize()
  }, [initialize])
  
  return <>{children}</>
}
```

## 🎯 First Feature: User Authentication

### 1. Login Form (Manual Example)
```tsx
// src/components/auth/login-form.tsx
'use client'

import { useState } from 'react'
import { useRouter } from 'next/navigation'
import { Button } from '@/components/ui/button'
import { Input } from '@/components/ui/input'
import { Label } from '@/components/ui/label'
import { Card, CardContent, CardHeader, CardTitle } from '@/components/ui/card'
import { useAuthStore } from '@/stores/auth-store'
import { useToast } from '@/components/ui/use-toast'

export function LoginForm() {
  const [email, setEmail] = useState('')
  const [password, setPassword] = useState('')
  const [loading, setLoading] = useState(false)
  
  const signIn = useAuthStore(state => state.signIn)
  const router = useRouter()
  const { toast } = useToast()
  
  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault()
    setLoading(true)
    
    try {
      await signIn(email, password)
      router.push('/dashboard')
      toast({
        title: 'Zalogowano pomyślnie',
        description: 'Witamy z powrotem!'
      })
    } catch (error) {
      toast({
        title: 'Błąd logowania',
        description: 'Sprawdź dane i spróbuj ponownie',
        variant: 'destructive'
      })
    } finally {
      setLoading(false)
    }
  }
  
  return (
    <Card className="w-full max-w-md mx-auto">
      <CardHeader>
        <CardTitle>Logowanie</CardTitle>
      </CardHeader>
      <CardContent>
        <form onSubmit={handleSubmit} className="space-y-4">
          <div>
            <Label htmlFor="email">Email</Label>
            <Input
              id="email"
              type="email"
              value={email}
              onChange={(e) => setEmail(e.target.value)}
              required
            />
          </div>
          <div>
            <Label htmlFor="password">Hasło</Label>
            <Input
              id="password"
              type="password"
              value={password}
              onChange={(e) => setPassword(e.target.value)}
              required
            />
          </div>
          <Button type="submit" className="w-full" disabled={loading}>
            {loading ? 'Logowanie...' : 'Zaloguj'}
          </Button>
        </form>
      </CardContent>
    </Card>
  )
}
```

### 2. Login Page
```tsx
// src/app/(auth)/login/page.tsx
import { LoginForm } from '@/components/auth/login-form'

export default function LoginPage() {
  return (
    <div className="min-h-screen flex items-center justify-center bg-gray-50">
      <div className="max-w-md w-full">
        <div className="text-center mb-8">
          <h1 className="text-3xl font-bold">Self-Writing App</h1>
          <p className="text-gray-600">Zaloguj się do swojego konta</p>
        </div>
        <LoginForm />
      </div>
    </div>
  )
}
```

### 3. Protected Dashboard
```tsx
// src/app/dashboard/page.tsx
'use client'

import { useAuthStore } from '@/stores/auth-store'
import { redirect } from 'next/navigation'
import { Button } from '@/components/ui/button'
import { Card, CardContent, CardHeader, CardTitle } from '@/components/ui/card'

export default function DashboardPage() {
  const { user, signOut } = useAuthStore()
  
  if (!user) {
    redirect('/login')
  }
  
  return (
    <div className="min-h-screen bg-gray-50">
      <nav className="bg-white shadow">
        <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
          <div className="flex justify-between h-16 items-center">
            <h1 className="text-xl font-semibold">Dashboard</h1>
            <Button variant="outline" onClick={() => signOut()}>
              Wyloguj
            </Button>
          </div>
        </div>
      </nav>
      
      <main className="max-w-7xl mx-auto py-6 sm:px-6 lg:px-8">
        <Card>
          <CardHeader>
            <CardTitle>Witaj, {user.email}!</CardTitle>
          </CardHeader>
          <CardContent>
            <p>Twoja aplikacja jest gotowa do auto-generowania funkcji!</p>
            <div className="mt-4 p-4 bg-blue-50 rounded-lg">
              <h3 className="font-medium text-blue-900">Następne kroki:</h3>
              <ol className="mt-2 text-sm text-blue-800 list-decimal list-inside space-y-1">
                <li>Skonfiguruj integrację z v0.dev</li>
                <li>Dodaj Claude Code MCP</li>
                <li>Opisz swoją pierwszą funkcję</li>
                <li>Obserwuj jak kod pisze się sam!</li>
              </ol>
            </div>
          </CardContent>
        </Card>
      </main>
    </div>
  )
}
```

## 🧪 Testing Setup

### 1. Jest Configuration
```javascript
// jest.config.js
const nextJest = require('next/jest')

const createJestConfig = nextJest({
  dir: './',
})

const customJestConfig = {
  setupFilesAfterEnv: ['<rootDir>/jest.setup.js'],
  moduleNameMapping: {
    '^@/components/(.*)$': '<rootDir>/src/components/$1',
    '^@/lib/(.*)$': '<rootDir>/src/lib/$1',
    '^@/stores/(.*)$': '<rootDir>/src/stores/$1',
  },
  testEnvironment: 'jest-environment-jsdom',
}

module.exports = createJestConfig(customJestConfig)
```

### 2. Test Example
```typescript
// src/components/auth/__tests__/login-form.test.tsx
import { render, screen, fireEvent, waitFor } from '@testing-library/react'
import { LoginForm } from '../login-form'
import { useAuthStore } from '@/stores/auth-store'

// Mock the auth store
jest.mock('@/stores/auth-store')
const mockUseAuthStore = useAuthStore as jest.MockedFunction<typeof useAuthStore>

describe('LoginForm', () => {
  const mockSignIn = jest.fn()
  
  beforeEach(() => {
    mockUseAuthStore.mockReturnValue({
      signIn: mockSignIn,
      user: null,
      loading: false,
      signUp: jest.fn(),
      signOut: jest.fn(),
      initialize: jest.fn(),
    })
  })
  
  test('renders login form', () => {
    render(<LoginForm />)
    
    expect(screen.getByLabelText(/email/i)).toBeInTheDocument()
    expect(screen.getByLabelText(/hasło/i)).toBeInTheDocument()
    expect(screen.getByRole('button', { name: /zaloguj/i })).toBeInTheDocument()
  })
  
  test('calls signIn on form submission', async () => {
    render(<LoginForm />)
    
    fireEvent.change(screen.getByLabelText(/email/i), {
      target: { value: 'test@example.com' }
    })
    fireEvent.change(screen.getByLabelText(/hasło/i), {
      target: { value: 'password123' }
    })
    
    fireEvent.click(screen.getByRole('button', { name: /zaloguj/i }))
    
    await waitFor(() => {
      expect(mockSignIn).toHaveBeenCalledWith('test@example.com', 'password123')
    })
  })
})
```

## 🚀 Deployment

### 1. Vercel Deployment
```bash
# Install Vercel CLI
npm i -g vercel

# Deploy
vercel

# Set environment variables
vercel env add NEXT_PUBLIC_SUPABASE_URL
vercel env add NEXT_PUBLIC_SUPABASE_ANON_KEY
vercel env add SUPABASE_SERVICE_ROLE_KEY

# Deploy to production
vercel --prod
```

### 2. Environment Configuration
```bash
# .env.example (template for other developers)
NEXT_PUBLIC_SUPABASE_URL=your_supabase_url
NEXT_PUBLIC_SUPABASE_ANON_KEY=your_supabase_anon_key
SUPABASE_SERVICE_ROLE_KEY=your_service_role_key
V0_API_KEY=your_v0_api_key
CLAUDE_API_KEY=your_claude_api_key
```

## ✅ Verification Checklist

- [ ] Project created with Next.js + TypeScript
- [ ] Supabase configured with authentication
- [ ] Basic UI components working (shadcn/ui)
- [ ] Authentication flow complete
- [ ] Protected routes working
- [ ] Environment variables set
- [ ] Tests passing
- [ ] Deployed to Vercel

## 🎉 Success!

Gratulacje! Masz teraz podstawową konfigurację Self-Writing Apps. Projekt jest gotowy na:

1. **Integrację z v0.dev** - automatyczne generowanie komponentów UI
2. **Claude Code MCP** - implementacja logiki biznesowej
3. **Pattern Learning** - system uczenia się z Twojego kodu

### Następne kroki:
- Przeczytaj [Component Factory Example](./component-factory.md)
- Zapoznaj się z [API Generation Example](./api-generation.md)
- Skonfiguruj [AI Development Workflow](../workflow.md)

---

*Twoja podróż w świat self-writing aplikacji właśnie się zaczęła! 🚀*