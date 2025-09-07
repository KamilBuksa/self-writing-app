# Common Patterns & Code Snippets

Frequently used code patterns ready to copy and customize for your projects.

## 🧩 Component Patterns

### 1. Loading Component Pattern
```tsx
// components/ui/loading.tsx
import { cn } from '@/lib/utils'
import { Loader2 } from 'lucide-react'

interface LoadingProps {
  size?: 'sm' | 'md' | 'lg'
  text?: string
  className?: string
}

const sizeClasses = {
  sm: 'h-4 w-4',
  md: 'h-6 w-6', 
  lg: 'h-8 w-8'
}

export function Loading({ size = 'md', text, className }: LoadingProps) {
  return (
    <div className={cn('flex items-center justify-center gap-2', className)}>
      <Loader2 className={cn('animate-spin', sizeClasses[size])} />
      {text && <span className="text-sm text-muted-foreground">{text}</span>}
    </div>
  )
}

// Usage
<Loading size="lg" text="Loading your data..." />
```

### 2. Error Boundary Pattern
```tsx
// components/error-boundary.tsx
'use client'

import React from 'react'
import { Button } from '@/components/ui/button'
import { AlertTriangle, RefreshCw } from 'lucide-react'

interface ErrorBoundaryState {
  hasError: boolean
  error?: Error
}

export class ErrorBoundary extends React.Component<
  { children: React.ReactNode; fallback?: React.ComponentType<{ error: Error; reset: () => void }> },
  ErrorBoundaryState
> {
  constructor(props: any) {
    super(props)
    this.state = { hasError: false }
  }

  static getDerivedStateFromError(error: Error): ErrorBoundaryState {
    return { hasError: true, error }
  }

  componentDidCatch(error: Error, errorInfo: React.ErrorInfo) {
    console.error('Error caught by boundary:', error, errorInfo)
  }

  render() {
    if (this.state.hasError) {
      const FallbackComponent = this.props.fallback || DefaultErrorFallback
      return (
        <FallbackComponent 
          error={this.state.error!} 
          reset={() => this.setState({ hasError: false })} 
        />
      )
    }

    return this.props.children
  }
}

function DefaultErrorFallback({ error, reset }: { error: Error; reset: () => void }) {
  return (
    <div className="flex flex-col items-center justify-center p-6 text-center">
      <AlertTriangle className="h-12 w-12 text-destructive mb-4" />
      <h2 className="text-lg font-semibold mb-2">Something went wrong</h2>
      <p className="text-sm text-muted-foreground mb-4">
        {error.message || 'An unexpected error occurred'}
      </p>
      <Button onClick={reset} variant="outline">
        <RefreshCw className="h-4 w-4 mr-2" />
        Try again
      </Button>
    </div>
  )
}

// Usage
<ErrorBoundary>
  <YourComponent />
</ErrorBoundary>
```

### 3. Data Fetching Hook Pattern
```tsx
// hooks/use-fetch.ts
import { useState, useEffect, useCallback } from 'react'

interface UseFetchOptions {
  immediate?: boolean
  onSuccess?: (data: any) => void
  onError?: (error: Error) => void
}

interface UseFetchResult<T> {
  data: T | null
  loading: boolean
  error: Error | null
  refetch: () => void
}

export function useFetch<T = any>(
  url: string | null,
  options: UseFetchOptions = {}
): UseFetchResult<T> {
  const { immediate = true, onSuccess, onError } = options
  
  const [data, setData] = useState<T | null>(null)
  const [loading, setLoading] = useState(false)
  const [error, setError] = useState<Error | null>(null)

  const fetchData = useCallback(async () => {
    if (!url) return

    try {
      setLoading(true)
      setError(null)
      
      const response = await fetch(url)
      if (!response.ok) {
        throw new Error(`HTTP error! status: ${response.status}`)
      }
      
      const result = await response.json()
      setData(result)
      onSuccess?.(result)
    } catch (err) {
      const error = err instanceof Error ? err : new Error('Unknown error')
      setError(error)
      onError?.(error)
    } finally {
      setLoading(false)
    }
  }, [url, onSuccess, onError])

  useEffect(() => {
    if (immediate) {
      fetchData()
    }
  }, [fetchData, immediate])

  return { data, loading, error, refetch: fetchData }
}

// Usage
const { data, loading, error, refetch } = useFetch<User[]>('/api/users')
```

## 🗄️ Database Patterns

### 1. Supabase Service Pattern
```typescript
// services/base-service.ts
import { createClientComponentClient } from '@supabase/auth-helpers-nextjs'
import type { Database } from '@/types/supabase'

export abstract class BaseService<T extends Record<string, any>> {
  protected supabase = createClientComponentClient<Database>()
  protected abstract tableName: string

  async findAll(options: {
    page?: number
    limit?: number
    orderBy?: string
    ascending?: boolean
  } = {}): Promise<{ data: T[]; count: number }> {
    const { page = 0, limit = 20, orderBy = 'created_at', ascending = false } = options
    
    let query = this.supabase
      .from(this.tableName)
      .select('*', { count: 'exact' })
      .range(page * limit, (page + 1) * limit - 1)
    
    if (orderBy) {
      query = query.order(orderBy, { ascending })
    }

    const { data, error, count } = await query

    if (error) throw error

    return { data: data || [], count: count || 0 }
  }

  async findById(id: string): Promise<T | null> {
    const { data, error } = await this.supabase
      .from(this.tableName)
      .select('*')
      .eq('id', id)
      .single()

    if (error) {
      if (error.code === 'PGRST116') return null
      throw error
    }

    return data
  }

  async create(item: Omit<T, 'id' | 'created_at' | 'updated_at'>): Promise<T> {
    const { data, error } = await this.supabase
      .from(this.tableName)
      .insert(item)
      .select()
      .single()

    if (error) throw error
    return data
  }

  async update(id: string, updates: Partial<T>): Promise<T> {
    const { data, error } = await this.supabase
      .from(this.tableName)
      .update(updates)
      .eq('id', id)
      .select()
      .single()

    if (error) throw error
    return data
  }

  async delete(id: string): Promise<void> {
    const { error } = await this.supabase
      .from(this.tableName)
      .delete()
      .eq('id', id)

    if (error) throw error
  }
}

// Concrete implementation
class UsersService extends BaseService<User> {
  protected tableName = 'users'
  
  async findByEmail(email: string): Promise<User | null> {
    const { data, error } = await this.supabase
      .from(this.tableName)
      .select('*')
      .eq('email', email)
      .single()

    if (error) {
      if (error.code === 'PGRST116') return null
      throw error
    }

    return data
  }
}

export const usersService = new UsersService()
```

### 2. RLS Policy Templates
```sql
-- User-specific data access
CREATE POLICY "Users can view own records" ON table_name
  FOR SELECT USING (auth.uid() = user_id);

CREATE POLICY "Users can create own records" ON table_name
  FOR INSERT WITH CHECK (auth.uid() = user_id);

CREATE POLICY "Users can update own records" ON table_name
  FOR UPDATE USING (auth.uid() = user_id);

-- Role-based access
CREATE POLICY "Admins can manage all records" ON table_name
  FOR ALL USING (
    auth.uid() IN (
      SELECT id FROM profiles WHERE role = 'admin'
    )
  );

-- Organization-based access
CREATE POLICY "Organization members can view records" ON table_name
  FOR SELECT USING (
    organization_id IN (
      SELECT organization_id FROM organization_members 
      WHERE user_id = auth.uid()
    )
  );

-- Public read, authenticated write
CREATE POLICY "Anyone can view" ON table_name
  FOR SELECT USING (true);

CREATE POLICY "Authenticated users can create" ON table_name
  FOR INSERT WITH CHECK (auth.uid() IS NOT NULL);
```

## 🎨 Form Patterns

### 1. Form Hook Pattern
```tsx
// hooks/use-form-with-schema.ts
import { useForm } from 'react-hook-form'
import { zodResolver } from '@hookform/resolvers/zod'
import { z } from 'zod'

export function useFormWithSchema<T extends z.ZodSchema>(
  schema: T,
  defaultValues?: Partial<z.infer<T>>
) {
  const form = useForm<z.infer<T>>({
    resolver: zodResolver(schema),
    defaultValues,
  })

  return form
}

// Usage
const loginSchema = z.object({
  email: z.string().email(),
  password: z.string().min(6),
})

const LoginForm = () => {
  const form = useFormWithSchema(loginSchema, {
    email: '',
    password: '',
  })

  const onSubmit = (data: z.infer<typeof loginSchema>) => {
    console.log(data)
  }

  return (
    <form onSubmit={form.handleSubmit(onSubmit)}>
      {/* Form fields */}
    </form>
  )
}
```

### 2. Form Field Component Pattern
```tsx
// components/form/form-field.tsx
import { cn } from '@/lib/utils'
import { Label } from '@/components/ui/label'
import { Input } from '@/components/ui/input'

interface FormFieldProps {
  label: string
  name: string
  error?: string
  required?: boolean
  type?: string
  placeholder?: string
  className?: string
  children?: React.ReactNode
}

export function FormField({
  label,
  name,
  error,
  required = false,
  type = 'text',
  placeholder,
  className,
  children,
  ...props
}: FormFieldProps) {
  return (
    <div className={cn('space-y-2', className)}>
      <Label htmlFor={name}>
        {label}
        {required && <span className="text-destructive ml-1">*</span>}
      </Label>
      
      {children || (
        <Input
          id={name}
          name={name}
          type={type}
          placeholder={placeholder}
          error={!!error}
          aria-describedby={error ? `${name}-error` : undefined}
          {...props}
        />
      )}
      
      {error && (
        <p id={`${name}-error`} className="text-sm text-destructive">
          {error}
        </p>
      )}
    </div>
  )
}

// Usage
<FormField
  label="Email"
  name="email"
  type="email"
  placeholder="Enter your email"
  error={errors.email?.message}
  required
  {...register('email')}
/>
```

## 🎯 API Patterns

### 1. API Response Pattern
```typescript
// types/api.ts
export interface ApiResponse<T = any> {
  success: boolean
  data?: T
  error?: string
  message?: string
}

export interface PaginatedResponse<T = any> extends ApiResponse<T[]> {
  pagination: {
    page: number
    limit: number
    total: number
    pages: number
  }
}

// lib/api-client.ts
class ApiClient {
  private baseUrl: string

  constructor(baseUrl: string = '/api') {
    this.baseUrl = baseUrl
  }

  private async request<T>(
    endpoint: string,
    options: RequestInit = {}
  ): Promise<T> {
    const url = `${this.baseUrl}${endpoint}`
    
    const response = await fetch(url, {
      headers: {
        'Content-Type': 'application/json',
        ...options.headers,
      },
      ...options,
    })

    if (!response.ok) {
      const error = await response.json().catch(() => ({
        error: `HTTP ${response.status}: ${response.statusText}`,
      }))
      throw new Error(error.error || 'Request failed')
    }

    return response.json()
  }

  async get<T>(endpoint: string): Promise<T> {
    return this.request<T>(endpoint, { method: 'GET' })
  }

  async post<T>(endpoint: string, data: any): Promise<T> {
    return this.request<T>(endpoint, {
      method: 'POST',
      body: JSON.stringify(data),
    })
  }

  async patch<T>(endpoint: string, data: any): Promise<T> {
    return this.request<T>(endpoint, {
      method: 'PATCH',
      body: JSON.stringify(data),
    })
  }

  async delete<T>(endpoint: string): Promise<T> {
    return this.request<T>(endpoint, { method: 'DELETE' })
  }
}

export const apiClient = new ApiClient()
```

### 2. API Route Handler Pattern
```typescript
// lib/api-handler.ts
import { NextRequest, NextResponse } from 'next/server'
import { ZodSchema } from 'zod'

interface HandlerOptions {
  requireAuth?: boolean
  requiredRole?: string
  validationSchema?: ZodSchema
}

export function createApiHandler(
  handlers: {
    GET?: (req: NextRequest, context: any) => Promise<Response>
    POST?: (req: NextRequest, context: any) => Promise<Response>
    PATCH?: (req: NextRequest, context: any) => Promise<Response>
    DELETE?: (req: NextRequest, context: any) => Promise<Response>
  },
  options: HandlerOptions = {}
) {
  return async function handler(req: NextRequest, context: any) {
    try {
      const method = req.method as keyof typeof handlers
      const handlerFn = handlers[method]

      if (!handlerFn) {
        return NextResponse.json(
          { error: `Method ${method} not allowed` },
          { status: 405 }
        )
      }

      // Authentication check
      if (options.requireAuth) {
        // Add your auth check logic here
      }

      // Validation
      if (options.validationSchema && ['POST', 'PATCH'].includes(method)) {
        const body = await req.json()
        const validation = options.validationSchema.safeParse(body)
        
        if (!validation.success) {
          return NextResponse.json(
            { error: 'Validation failed', details: validation.error.errors },
            { status: 400 }
          )
        }
      }

      return await handlerFn(req, context)
    } catch (error) {
      console.error('API handler error:', error)
      return NextResponse.json(
        { error: 'Internal server error' },
        { status: 500 }
      )
    }
  }
}

// Usage in API route
export const { GET, POST } = createApiHandler({
  GET: async (req) => {
    // Handle GET request
    return NextResponse.json({ success: true, data: [] })
  },
  POST: async (req) => {
    // Handle POST request
    const data = await req.json()
    return NextResponse.json({ success: true, data })
  }
}, {
  requireAuth: true,
  validationSchema: createUserSchema
})
```

## 🎨 Layout Patterns

### 1. Dashboard Layout Pattern
```tsx
// components/layout/dashboard-layout.tsx
import { Sidebar } from './sidebar'
import { Header } from './header'

interface DashboardLayoutProps {
  children: React.ReactNode
}

export function DashboardLayout({ children }: DashboardLayoutProps) {
  return (
    <div className="min-h-screen bg-background">
      <div className="flex h-screen">
        {/* Sidebar */}
        <aside className="hidden md:flex md:w-64 md:flex-col">
          <Sidebar />
        </aside>
        
        {/* Main content */}
        <div className="flex flex-1 flex-col overflow-hidden">
          <Header />
          <main className="flex-1 overflow-y-auto">
            <div className="container mx-auto px-4 py-8">
              {children}
            </div>
          </main>
        </div>
      </div>
    </div>
  )
}
```

### 2. Responsive Grid Pattern
```tsx
// components/ui/responsive-grid.tsx
interface GridProps {
  children: React.ReactNode
  cols?: {
    default: number
    sm?: number
    md?: number
    lg?: number
    xl?: number
  }
  gap?: number
  className?: string
}

export function ResponsiveGrid({ 
  children, 
  cols = { default: 1 }, 
  gap = 4,
  className 
}: GridProps) {
  const gridClass = cn(
    'grid',
    `gap-${gap}`,
    `grid-cols-${cols.default}`,
    cols.sm && `sm:grid-cols-${cols.sm}`,
    cols.md && `md:grid-cols-${cols.md}`,
    cols.lg && `lg:grid-cols-${cols.lg}`,
    cols.xl && `xl:grid-cols-${cols.xl}`,
    className
  )

  return (
    <div className={gridClass}>
      {children}
    </div>
  )
}

// Usage
<ResponsiveGrid 
  cols={{ default: 1, sm: 2, lg: 3, xl: 4 }}
  gap={6}
>
  {items.map(item => <Card key={item.id} {...item} />)}
</ResponsiveGrid>
```

---

*These patterns provide a solid foundation for common development scenarios. Customize them according to your specific needs and project requirements.*