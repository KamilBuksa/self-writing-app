# Project Context for AI Development

> Copy this file to your project's `.ai-context/project-context.md` and fill in your details

## 📋 Project Information

### Basic Details
```
Project Name: [Your App Name]
Description: [Brief description of what your app does]
Domain: [e.g., e-commerce, SaaS, social, etc.]
Target Users: [Who will use this app]
```

### Tech Stack
```
Framework: Next.js 14 (App Router)
Database: Supabase (PostgreSQL + Auth + Storage)
Styling: Tailwind CSS + shadcn/ui
State Management: Zustand
Forms: React Hook Form + Zod
Testing: Jest + Playwright
Deployment: Vercel
```

## 🎯 Key Features

### Core Features
1. [Feature 1 - e.g., User authentication and profiles]
2. [Feature 2 - e.g., Content creation and management]
3. [Feature 3 - e.g., Payment processing]

### Planned Features
1. [Future feature 1]
2. [Future feature 2]
3. [Future feature 3]

## 🏗️ Architecture Principles

### Code Organization
- **Component-driven development**: Every UI element is a reusable component
- **Feature-based folders**: Group related code by feature, not by file type
- **Atomic design**: Components organized by complexity (atoms → molecules → organisms)
- **Service layer pattern**: Business logic separated from UI components

### Development Standards
- **TypeScript strict mode**: All code must be properly typed
- **ESLint + Prettier**: Automated code formatting and linting
- **Test-driven development**: All features must have comprehensive tests
- **Documentation-first**: Every component and function must be documented

## 🎨 Design System

### Component Library
```
Base: shadcn/ui components
Custom: Built on top of shadcn/ui following our design tokens
Icons: Lucide React
Typography: Inter font family
```

### Design Tokens
```
Colors: Using CSS custom properties with light/dark mode support
Spacing: Tailwind default spacing scale (4px base unit)
Typography: Responsive typography scale
Breakpoints: sm(640px), md(768px), lg(1024px), xl(1280px), 2xl(1536px)
```

## 🔧 Coding Conventions

### Naming Conventions
- **Components**: PascalCase (e.g., `UserProfile`, `ProductCard`)
- **Files**: kebab-case (e.g., `user-profile.tsx`, `product-card.module.css`)
- **Functions**: camelCase (e.g., `getUserProfile`, `calculateTotal`)
- **Constants**: SCREAMING_SNAKE_CASE (e.g., `API_BASE_URL`, `MAX_RETRIES`)

### File Structure
```
src/
├── app/                    # Next.js app router
├── components/             # Reusable components
│   ├── ui/                # Design system components
│   └── features/          # Feature-specific components
├── lib/                   # Utilities and configuration
├── hooks/                 # Custom React hooks
├── stores/                # Zustand stores
├── services/              # API and business logic
├── types/                 # TypeScript type definitions
└── utils/                 # Helper functions
```

### Import/Export Patterns
- **Prefer named exports** over default exports
- **Use index files** for clean imports from directories
- **Import order**: External packages → Internal modules → Relative imports
- **Use absolute imports** with `@/` alias for better maintainability

## 🗄️ Database Schema

### Core Tables
```sql
-- Users and authentication (handled by Supabase Auth)
profiles (id, email, name, avatar_url, created_at, updated_at)

-- [Add your main entities here]
-- Example:
-- posts (id, title, content, author_id, status, created_at, updated_at)
-- comments (id, post_id, author_id, content, created_at)
```

### Relationships
```
[Describe your main data relationships]
Examples:
- Users have many Posts (one-to-many)
- Posts have many Comments (one-to-many)
- Users can like Posts (many-to-many)
```

## 🔒 Authentication & Authorization

### Authentication Strategy
```
Primary: Email/Password via Supabase Auth
Social: [List social providers you support]
Session: JWT tokens with automatic refresh
```

### User Roles
```
admin: Full system access
moderator: Content moderation capabilities
user: Standard user capabilities
```

### Protected Routes
```
/dashboard/*: Requires authentication
/admin/*: Requires admin role
/api/admin/*: Requires admin role
```

## 📱 Responsive Design Strategy

### Breakpoint Strategy
```
Mobile First: Design for mobile, enhance for larger screens
Key Breakpoints:
- Mobile: 320px - 767px
- Tablet: 768px - 1023px  
- Desktop: 1024px+
```

### Component Responsiveness
- **Navigation**: Hamburger menu on mobile, full nav on desktop
- **Layout**: Single column on mobile, multi-column on larger screens
- **Typography**: Responsive font sizes using Tailwind's responsive utilities
- **Images**: Responsive images with Next.js Image component

## 🧪 Testing Strategy

### Testing Levels
- **Unit Tests**: Individual functions and components
- **Integration Tests**: Feature workflows and API integrations
- **E2E Tests**: Complete user journeys and critical paths

### Testing Tools
```
Unit: Jest + React Testing Library
Integration: Jest + MSW (Mock Service Worker)
E2E: Playwright
Visual: Storybook (optional)
```

## 🚀 Deployment & Environment

### Environments
```
Development: Local development server
Staging: Preview deployments on Vercel
Production: Main deployment on Vercel
```

### Environment Variables
```
NEXT_PUBLIC_SUPABASE_URL: Supabase project URL
NEXT_PUBLIC_SUPABASE_ANON_KEY: Supabase anonymous key
SUPABASE_SERVICE_ROLE_KEY: Supabase service role key (server-only)
[Add your custom environment variables]
```

## 📊 Performance Standards

### Performance Metrics
- **Time to Interactive**: < 3 seconds
- **Largest Contentful Paint**: < 2.5 seconds
- **Cumulative Layout Shift**: < 0.1
- **Bundle Size**: < 250kb initial load

### Optimization Strategies
- **Code Splitting**: Dynamic imports for heavy components
- **Image Optimization**: Next.js Image component with proper sizing
- **Caching**: Aggressive caching for static content
- **Database**: Optimized queries and proper indexing

## 🎯 AI Development Context

### Preferred Patterns
When generating code, always follow these established patterns:
1. Use our component structure and naming conventions
2. Follow our TypeScript strict mode requirements
3. Include proper error handling and loading states
4. Add comprehensive tests for all generated code
5. Use our established Tailwind CSS classes and design tokens

### Code Generation Guidelines
- Always include TypeScript interfaces and proper typing
- Use our established folder structure and file naming
- Include proper imports following our import conventions
- Add JSDoc comments for functions and components
- Follow our responsive design principles

---

*Update this file as your project evolves. The more context you provide, the better AI tools can assist with code generation that fits your project perfectly.*