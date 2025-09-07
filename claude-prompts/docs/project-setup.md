# Project Setup Guide

## 🚀 AI-First Project Bootstrap

This guide shows you how to set up new projects with AI-first development in mind, optimized for self-writing applications and Claude Code integration.

## 🎯 Project Types

### 1. Full-Stack Web Application
**Best for**: E-commerce, SaaS, dashboards, social platforms

```bash
# Recommended stack
Framework: Next.js 14 (App Router)
Database: Supabase
Styling: Tailwind CSS + shadcn/ui
State: Zustand
Forms: React Hook Form + Zod
Testing: Jest + Playwright
Deployment: Vercel
```

### 2. Mobile-First PWA
**Best for**: Mobile apps, offline-first applications

```bash
Framework: Next.js 14 PWA
Database: Supabase + offline sync
Styling: Tailwind CSS (mobile-first)
State: Zustand + persist
Push: Web Push API
Testing: Jest + Playwright mobile
```

### 3. AI-Integrated Application
**Best for**: AI-powered tools, content generation, automation

```bash
Framework: Next.js 14
AI APIs: OpenAI, Claude, v0.dev
Database: Supabase + vector storage
Queue: Vercel Functions + Redis
Monitoring: Posthog + Sentry
```

## 🏗️ Quick Setup (5 minutes)

### Option 1: Automated Setup Script

```bash
# Download and run setup script
curl -o setup-ai-project.sh https://raw.githubusercontent.com/your-org/ai-setup/main/setup.sh
chmod +x setup-ai-project.sh
./setup-ai-project.sh

# Interactive prompts will ask for:
# - Project name and type
# - Tech stack preferences
# - AI tools integration
# - Database and auth setup
```

### Option 2: Manual Setup

```bash
# 1. Create Next.js project
npx create-next-app@latest my-ai-app \
  --typescript \
  --tailwind \
  --eslint \
  --app \
  --src-dir \
  --import-alias "@/*"

cd my-ai-app

# 2. Install core dependencies
npm install @supabase/supabase-js @supabase/auth-helpers-nextjs
npm install zustand
npm install react-hook-form @hookform/resolvers zod
npm install @radix-ui/react-slot class-variance-authority clsx tailwind-merge
npm install lucide-react

# 3. Install dev dependencies
npm install -D @types/node
npm install -D prettier prettier-plugin-tailwindcss
npm install -D @testing-library/react @testing-library/jest-dom
npm install -D @playwright/test

# 4. Setup shadcn/ui
npx shadcn-ui@latest init
npx shadcn-ui@latest add button card input label form toast
```

## 📁 Optimal Folder Structure

### AI-Friendly Organization
```
project-root/
├── .ai-context/                 # AI context files
│   ├── project-info.md         # Project overview
│   ├── patterns.json           # Code patterns
│   ├── conventions.md          # Coding conventions
│   └── architecture.md         # System architecture
├── docs/                       # Documentation
│   ├── api/                    # API documentation
│   ├── components/             # Component documentation
│   └── features/               # Feature specifications
├── src/
│   ├── app/                    # Next.js app router
│   │   ├── (auth)/            # Route groups
│   │   ├── (dashboard)/
│   │   └── api/               # API routes
│   ├── components/            # Reusable components
│   │   ├── ui/               # Design system (shadcn/ui)
│   │   ├── forms/            # Form components
│   │   ├── layout/           # Layout components
│   │   └── features/         # Feature-specific components
│   ├── lib/                  # Utilities and config
│   │   ├── supabase.ts      # Database client
│   │   ├── utils.ts         # Utility functions
│   │   ├── validations.ts   # Zod schemas
│   │   └── constants.ts     # App constants
│   ├── hooks/               # Custom React hooks
│   ├── stores/              # Zustand stores
│   ├── services/            # API services
│   ├── types/               # TypeScript definitions
│   └── styles/              # Global styles
├── public/                  # Static assets
├── tests/                   # Test files
│   ├── __mocks__/          # Test mocks
│   ├── components/         # Component tests
│   ├── integration/        # Integration tests
│   └── e2e/               # End-to-end tests
└── config files...
```

## ⚙️ Essential Configuration Files

### 1. AI Context Setup
```markdown
# .ai-context/project-info.md
# Project: [Your App Name]

## Overview
[Brief description of what the app does]

## Tech Stack
- Framework: Next.js 14 with App Router
- Database: Supabase (PostgreSQL + Auth + Storage)
- Styling: Tailwind CSS + shadcn/ui
- State Management: Zustand
- Forms: React Hook Form + Zod
- Testing: Jest + React Testing Library + Playwright

## Key Features
1. [Feature 1 description]
2. [Feature 2 description]
3. [Feature 3 description]

## Architecture Principles
- Component-driven development
- Atomic design methodology
- Service layer pattern
- Feature-based folder structure
- TypeScript strict mode

## Coding Conventions
- Use TypeScript interfaces over types
- Prefer named exports over default exports
- Use kebab-case for file names
- Use PascalCase for component names
- Use camelCase for functions and variables
```

### 2. Environment Configuration
```bash
# .env.local
# Supabase
NEXT_PUBLIC_SUPABASE_URL=your_supabase_url
NEXT_PUBLIC_SUPABASE_ANON_KEY=your_supabase_anon_key
SUPABASE_SERVICE_ROLE_KEY=your_service_role_key

# AI Tools (optional)
OPENAI_API_KEY=your_openai_key
CLAUDE_API_KEY=your_claude_key
V0_API_KEY=your_v0_key

# App Configuration
NEXT_PUBLIC_APP_URL=http://localhost:3000
NEXT_PUBLIC_APP_NAME="Your App Name"

# Analytics (optional)
NEXT_PUBLIC_POSTHOG_KEY=your_posthog_key
NEXT_PUBLIC_POSTHOG_HOST=https://app.posthog.com
```

### 3. TypeScript Configuration
```json
// tsconfig.json (enhanced)
{
  "compilerOptions": {
    "target": "ES2017",
    "lib": ["dom", "dom.iterable", "ES6"],
    "allowJs": true,
    "skipLibCheck": true,
    "strict": true,
    "noEmit": true,
    "esModuleInterop": true,
    "module": "esnext",
    "moduleResolution": "bundler",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "jsx": "preserve",
    "incremental": true,
    "plugins": [
      {
        "name": "next"
      }
    ],
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"],
      "@/components/*": ["./src/components/*"],
      "@/lib/*": ["./src/lib/*"],
      "@/hooks/*": ["./src/hooks/*"],
      "@/stores/*": ["./src/stores/*"],
      "@/types/*": ["./src/types/*"],
      "@/services/*": ["./src/services/*"]
    }
  },
  "include": ["next-env.d.ts", "**/*.ts", "**/*.tsx", ".next/types/**/*.ts"],
  "exclude": ["node_modules"]
}
```

### 4. Tailwind Configuration
```javascript
// tailwind.config.js
/** @type {import('tailwindcss').Config} */
module.exports = {
  darkMode: ["class"],
  content: [
    './pages/**/*.{ts,tsx}',
    './components/**/*.{ts,tsx}',
    './app/**/*.{ts,tsx}',
    './src/**/*.{ts,tsx}',
  ],
  prefix: "",
  theme: {
    container: {
      center: true,
      padding: "2rem",
      screens: {
        "2xl": "1400px",
      },
    },
    extend: {
      colors: {
        border: "hsl(var(--border))",
        input: "hsl(var(--input))",
        ring: "hsl(var(--ring))",
        background: "hsl(var(--background))",
        foreground: "hsl(var(--foreground))",
        primary: {
          DEFAULT: "hsl(var(--primary))",
          foreground: "hsl(var(--primary-foreground))",
        },
        secondary: {
          DEFAULT: "hsl(var(--secondary))",
          foreground: "hsl(var(--secondary-foreground))",
        },
        destructive: {
          DEFAULT: "hsl(var(--destructive))",
          foreground: "hsl(var(--destructive-foreground))",
        },
        muted: {
          DEFAULT: "hsl(var(--muted))",
          foreground: "hsl(var(--muted-foreground))",
        },
        accent: {
          DEFAULT: "hsl(var(--accent))",
          foreground: "hsl(var(--accent-foreground))",
        },
        popover: {
          DEFAULT: "hsl(var(--popover))",
          foreground: "hsl(var(--popover-foreground))",
        },
        card: {
          DEFAULT: "hsl(var(--card))",
          foreground: "hsl(var(--card-foreground))",
        },
      },
      borderRadius: {
        lg: "var(--radius)",
        md: "calc(var(--radius) - 2px)",
        sm: "calc(var(--radius) - 4px)",
      },
      keyframes: {
        "accordion-down": {
          from: { height: "0" },
          to: { height: "var(--radix-accordion-content-height)" },
        },
        "accordion-up": {
          from: { height: "var(--radix-accordion-content-height)" },
          to: { height: "0" },
        },
      },
      animation: {
        "accordion-down": "accordion-down 0.2s ease-out",
        "accordion-up": "accordion-up 0.2s ease-out",
      },
    },
  },
  plugins: [require("tailwindcss-animate")],
}
```

## 🗄️ Database Setup

### Supabase Initial Schema
```sql
-- Enable necessary extensions
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

-- Create profiles table
CREATE TABLE profiles (
  id UUID REFERENCES auth.users(id) PRIMARY KEY,
  email TEXT UNIQUE NOT NULL,
  name TEXT,
  avatar_url TEXT,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- Enable Row Level Security
ALTER TABLE profiles ENABLE ROW LEVEL SECURITY;

-- Create policies
CREATE POLICY "Users can view own profile" ON profiles 
  FOR SELECT USING (auth.uid() = id);

CREATE POLICY "Users can update own profile" ON profiles 
  FOR UPDATE USING (auth.uid() = id);

-- Create function to handle new user signup
CREATE OR REPLACE FUNCTION handle_new_user() 
RETURNS TRIGGER AS $$
BEGIN
  INSERT INTO profiles (id, email, name)
  VALUES (NEW.id, NEW.email, NEW.raw_user_meta_data->>'name');
  RETURN NEW;
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;

-- Create trigger for new user signup
CREATE TRIGGER on_auth_user_created
  AFTER INSERT ON auth.users
  FOR EACH ROW
  EXECUTE FUNCTION handle_new_user();

-- Create updated_at trigger function
CREATE OR REPLACE FUNCTION update_updated_at_column()
RETURNS TRIGGER AS $$
BEGIN
  NEW.updated_at = NOW();
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

-- Apply updated_at trigger to profiles
CREATE TRIGGER update_profiles_updated_at
  BEFORE UPDATE ON profiles
  FOR EACH ROW
  EXECUTE FUNCTION update_updated_at_column();
```

## 🧪 Testing Setup

### Jest Configuration
```javascript
// jest.config.js
const nextJest = require('next/jest')

const createJestConfig = nextJest({
  dir: './',
})

const customJestConfig = {
  setupFilesAfterEnv: ['<rootDir>/jest.setup.js'],
  testEnvironment: 'jest-environment-jsdom',
  moduleNameMapping: {
    '^@/(.*)$': '<rootDir>/src/$1',
  },
  testPathIgnorePatterns: ['/node_modules/', '/.next/', '/tests/e2e/'],
  collectCoverageFrom: [
    'src/**/*.{js,jsx,ts,tsx}',
    '!src/**/*.d.ts',
    '!src/**/*.stories.{js,jsx,ts,tsx}',
  ],
}

module.exports = createJestConfig(customJestConfig)
```

### Playwright Configuration
```javascript
// playwright.config.js
import { defineConfig, devices } from '@playwright/test'

export default defineConfig({
  testDir: './tests/e2e',
  fullyParallel: true,
  forbidOnly: !!process.env.CI,
  retries: process.env.CI ? 2 : 0,
  workers: process.env.CI ? 1 : undefined,
  reporter: 'html',
  use: {
    baseURL: 'http://localhost:3000',
    trace: 'on-first-retry',
  },
  projects: [
    {
      name: 'chromium',
      use: { ...devices['Desktop Chrome'] },
    },
    {
      name: 'firefox',
      use: { ...devices['Desktop Firefox'] },
    },
    {
      name: 'webkit',
      use: { ...devices['Desktop Safari'] },
    },
    {
      name: 'Mobile Chrome',
      use: { ...devices['Pixel 5'] },
    },
    {
      name: 'Mobile Safari',
      use: { ...devices['iPhone 12'] },
    },
  ],
  webServer: {
    command: 'npm run dev',
    url: 'http://localhost:3000',
    reuseExistingServer: !process.env.CI,
  },
})
```

## 📦 Essential Utilities

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

### 2. Utility Functions
```typescript
// src/lib/utils.ts
import { clsx, type ClassValue } from 'clsx'
import { twMerge } from 'tailwind-merge'

export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs))
}

export const formatDate = (date: string | Date) => {
  return new Intl.DateTimeFormat('en-US', {
    year: 'numeric',
    month: 'long',
    day: 'numeric'
  }).format(new Date(date))
}

export const formatCurrency = (amount: number, currency = 'USD') => {
  return new Intl.NumberFormat('en-US', {
    style: 'currency',
    currency
  }).format(amount)
}

export const slugify = (str: string) => {
  return str
    .toLowerCase()
    .replace(/[^\w ]+/g, '')
    .replace(/ +/g, '-')
}

export const debounce = <T extends (...args: any[]) => any>(
  func: T,
  wait: number
): ((...args: Parameters<T>) => void) => {
  let timeout: NodeJS.Timeout
  return (...args: Parameters<T>) => {
    clearTimeout(timeout)
    timeout = setTimeout(() => func(...args), wait)
  }
}
```

### 3. Validation Schemas
```typescript
// src/lib/validations.ts
import { z } from 'zod'

export const profileSchema = z.object({
  name: z.string().min(2, 'Name must be at least 2 characters'),
  email: z.string().email('Invalid email address'),
  avatar_url: z.string().url().optional().or(z.literal(''))
})

export const loginSchema = z.object({
  email: z.string().email('Invalid email address'),
  password: z.string().min(6, 'Password must be at least 6 characters')
})

export const registerSchema = z.object({
  name: z.string().min(2, 'Name must be at least 2 characters'),
  email: z.string().email('Invalid email address'),
  password: z.string().min(6, 'Password must be at least 6 characters'),
  confirmPassword: z.string()
}).refine((data) => data.password === data.confirmPassword, {
  message: "Passwords don't match",
  path: ["confirmPassword"]
})

export type ProfileFormData = z.infer<typeof profileSchema>
export type LoginFormData = z.infer<typeof loginSchema>
export type RegisterFormData = z.infer<typeof registerSchema>
```

## 🚀 Deployment Setup

### Vercel Configuration
```json
// vercel.json
{
  "buildCommand": "npm run build",
  "outputDirectory": ".next",
  "installCommand": "npm install",
  "functions": {
    "app/api/**/*.ts": {
      "maxDuration": 30
    }
  },
  "env": {
    "NEXT_PUBLIC_SUPABASE_URL": "@supabase_url",
    "NEXT_PUBLIC_SUPABASE_ANON_KEY": "@supabase_anon_key",
    "SUPABASE_SERVICE_ROLE_KEY": "@supabase_service_role_key"
  }
}
```

### Environment Variables Setup
```bash
# Production environment variables to set in Vercel dashboard:
NEXT_PUBLIC_SUPABASE_URL
NEXT_PUBLIC_SUPABASE_ANON_KEY  
SUPABASE_SERVICE_ROLE_KEY
NEXT_PUBLIC_APP_URL
OPENAI_API_KEY (if using AI features)
CLAUDE_API_KEY (if using Claude)
```

## ✅ Post-Setup Checklist

### Development Environment
- [ ] Project created and dependencies installed
- [ ] Folder structure organized
- [ ] AI context files created (.ai-context/)
- [ ] Environment variables configured
- [ ] Supabase project connected and schema applied
- [ ] shadcn/ui components installed
- [ ] Testing framework configured (Jest + Playwright)

### AI Optimization
- [ ] Project documentation complete
- [ ] Code patterns documented
- [ ] Conventions clearly defined
- [ ] Architecture decisions recorded
- [ ] Component examples created

### Quality Assurance
- [ ] TypeScript strict mode enabled
- [ ] ESLint and Prettier configured
- [ ] Pre-commit hooks set up
- [ ] CI/CD pipeline configured
- [ ] Error tracking set up (Sentry)
- [ ] Analytics configured (PostHog)

### Deployment Ready
- [ ] Environment variables set in Vercel
- [ ] Domain configured (if custom)
- [ ] Performance optimization completed
- [ ] SEO basics implemented
- [ ] Security headers configured

## 🎯 Success Metrics

After setup completion, you should have:
- **Development Speed**: Ready for AI-first development
- **Code Quality**: TypeScript strict, linting, testing configured
- **Scalability**: Folder structure and patterns for growth
- **AI Integration**: Optimized for Claude Code and other AI tools
- **Production Ready**: Deployment and monitoring configured

---

*Perfect foundation for building self-writing applications with maximum AI assistance and minimum manual setup.*