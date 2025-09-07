# AI Prompt Templates

Ready-to-use prompts optimized for maximum code generation quality. Copy, customize, and use immediately.

## 🧩 Component Generation Prompts

### Basic Component Template
```
Following the patterns established in my codebase (.ai-context/project-context.md), create a [COMPONENT_TYPE] component that:

Requirements:
- Uses TypeScript with strict typing
- Follows our component structure patterns
- Uses Tailwind CSS with our design tokens
- Includes proper error handling and loading states
- Has responsive design (mobile-first)
- Includes accessibility features (WCAG 2.1)
- Uses our established naming conventions

Integration:
- Import utilities from @/lib/utils
- Use our component patterns from /components/ui/
- Follow our file structure conventions
- Include proper JSDoc documentation

Functionality:
[DESCRIBE SPECIFIC FUNCTIONALITY HERE]

Please generate:
1. The main component file
2. TypeScript interfaces
3. Unit tests using Jest and React Testing Library
4. Usage example
```

### Form Component Template
```
Create a form component following our React Hook Form + Zod patterns:

Component: [FORM_NAME] (e.g., ContactForm, UserRegistrationForm)

Fields:
- [field1]: [type] - [validation rules]
- [field2]: [type] - [validation rules]
- [field3]: [type] - [validation rules]

Features Required:
- Real-time validation with error messages
- Loading states during submission
- Success/error feedback
- Responsive design
- Accessibility compliance
- Form reset after successful submission

Integration:
- Use our established form patterns from /components/forms/
- Follow Zod schema patterns from /lib/validations/
- Include proper TypeScript typing
- Use our Button and Input components from /components/ui/

Please generate:
1. Form component with React Hook Form
2. Zod validation schema
3. TypeScript interfaces
4. Unit tests covering all scenarios
5. Usage example with error handling
```

### Data Display Component Template
```
Create a data display component for [DATA_TYPE]:

Data Structure:
[DESCRIBE YOUR DATA INTERFACE]

Display Requirements:
- Loading skeleton while fetching data
- Empty state when no data
- Error state with retry functionality
- Responsive grid/list layout
- Search and filter capabilities (if applicable)
- Pagination (if applicable)

Features:
- Real-time updates (if applicable)
- Interactive elements (click, hover states)
- Export functionality (if needed)
- Sorting capabilities

Technical Requirements:
- Use our custom hooks for data fetching
- Follow our component patterns
- Include comprehensive error handling
- Mobile-responsive design
- Accessibility features

Please generate:
1. Main display component
2. Loading skeleton component
3. Empty state component
4. Error state component
5. Custom hook for data management
6. TypeScript interfaces
7. Unit tests for all states
```

## 🚀 Feature Development Prompts

### Complete Feature Template
```
I need to implement a complete [FEATURE_NAME] feature. Here's what I need:

Feature Description:
[DETAILED DESCRIPTION OF WHAT THE FEATURE DOES]

User Stories:
- As a [user type], I want to [action] so that [benefit]
- As a [user type], I want to [action] so that [benefit]
- As a [user type], I want to [action] so that [benefit]

Technical Requirements:
- Frontend: React components with TypeScript
- Backend: Next.js API routes
- Database: Supabase with RLS policies
- Authentication: Integration with our auth system
- Real-time: Live updates if needed

Acceptance Criteria:
- [ ] [Specific requirement 1]
- [ ] [Specific requirement 2]
- [ ] [Specific requirement 3]

Please generate following our established patterns:
1. Database schema with RLS policies
2. API routes with proper validation
3. Frontend components with full functionality
4. Custom hooks for state management
5. Comprehensive tests (unit + integration)
6. Documentation and usage examples

Follow our project patterns from .ai-context/project-context.md
```

### API Route Template
```
Create Next.js API routes for [RESOURCE_NAME] with full CRUD operations:

Resource Schema:
[DESCRIBE YOUR DATA SCHEMA]

Required Endpoints:
- GET /api/[resource] - List with pagination, search, filters
- GET /api/[resource]/[id] - Get single item
- POST /api/[resource] - Create new item
- PATCH /api/[resource]/[id] - Update existing item
- DELETE /api/[resource]/[id] - Delete item

Requirements:
- Supabase integration with RLS
- Input validation using Zod schemas
- Proper error handling and status codes
- Authentication/authorization checks
- Rate limiting (if applicable)
- Comprehensive logging

Security:
- Row Level Security policies
- Input sanitization
- Authentication verification
- Authorization by user role

Please generate:
1. All CRUD API routes
2. Zod validation schemas
3. Supabase RLS policies
4. TypeScript interfaces
5. API documentation
6. Integration tests
7. Error handling utilities
```

## 🗄️ Database Schema Prompts

### Database Schema Template
```
Create a Supabase database schema for [FEATURE/DOMAIN]:

Entities:
[DESCRIBE YOUR MAIN ENTITIES AND THEIR RELATIONSHIPS]

Requirements:
- PostgreSQL with Supabase extensions
- UUID primary keys
- Timestamps (created_at, updated_at)
- Proper foreign key relationships
- Indexes for performance
- RLS policies for security
- Triggers for automation (if needed)

Security:
- Row Level Security enabled
- Policies for different user roles
- Secure functions with SECURITY DEFINER

Please generate:
1. CREATE TABLE statements
2. Indexes for performance
3. RLS policies for all tables
4. Helper functions and triggers
5. Sample data for testing
6. TypeScript interfaces matching the schema
7. Migration scripts

Follow our database patterns from /docs/standards/supabase-patterns.md
```

## 🎨 Styling & UI Prompts

### Styling Template
```
Style this component following our design system:

Component: [COMPONENT_NAME]
Design Requirements: [DESCRIBE VISUAL REQUIREMENTS]

Our Design System:
- Tailwind CSS with custom design tokens
- shadcn/ui component library
- Dark/light mode support
- Mobile-first responsive design
- Consistent spacing and typography

Styling Requirements:
- Use our color palette (CSS custom properties)
- Follow our spacing conventions
- Responsive breakpoints: sm, md, lg, xl, 2xl
- Hover and focus states
- Loading and disabled states
- Accessibility considerations

Please generate:
1. Styled component with Tailwind classes
2. Variant support using class-variance-authority
3. Dark/light mode compatibility
4. Responsive design implementation
5. Interactive states (hover, focus, active)
6. Accessibility attributes
```

## 🧪 Testing Prompts

### Test Generation Template
```
Generate comprehensive tests for [COMPONENT/FEATURE]:

Code to Test:
[PASTE YOUR CODE OR DESCRIBE THE FUNCTIONALITY]

Test Requirements:
- Unit tests for all functions/methods
- Component tests with React Testing Library
- Integration tests for user workflows
- Edge cases and error scenarios
- Accessibility tests with jest-axe
- Mock external dependencies

Test Coverage:
- Happy path scenarios
- Error handling
- Loading states
- Empty states
- User interactions
- Form validation (if applicable)

Please generate:
1. Unit tests with Jest
2. Component tests with React Testing Library
3. Mock utilities and test data
4. Integration test scenarios
5. Accessibility tests
6. Performance tests (if applicable)

Follow our testing patterns from existing tests in __tests__/
```

## 🔧 Utility & Hook Prompts

### Custom Hook Template
```
Create a custom React hook for [FUNCTIONALITY]:

Hook Purpose:
[DESCRIBE WHAT THE HOOK SHOULD DO]

Parameters:
[LIST EXPECTED PARAMETERS]

Return Value:
[DESCRIBE WHAT THE HOOK RETURNS]

Features:
- Error handling
- Loading states
- Caching (if applicable)
- Cleanup on unmount
- TypeScript support
- Optimized re-renders

Dependencies:
[LIST ANY EXTERNAL DEPENDENCIES]

Please generate:
1. Custom hook implementation
2. TypeScript interfaces
3. Usage examples
4. Unit tests
5. JSDoc documentation
6. Error handling scenarios
```

## 📊 Performance Optimization Prompts

### Optimization Template
```
Optimize this code for performance:

Current Code:
[PASTE YOUR CODE]

Performance Issues:
[DESCRIBE CURRENT PERFORMANCE PROBLEMS]

Optimization Goals:
- Reduce bundle size
- Improve rendering performance
- Optimize API calls
- Reduce memory usage
- Improve Core Web Vitals

Techniques to Apply:
- React.memo, useMemo, useCallback
- Code splitting and lazy loading
- Image optimization
- API caching and batching
- Virtual scrolling (if applicable)

Please provide:
1. Optimized code with explanations
2. Performance measurement suggestions
3. Bundle size impact analysis
4. Before/after comparison
5. Monitoring recommendations
```

## 💡 Usage Tips

### How to Use These Templates:
1. **Copy the template** that matches your need
2. **Replace placeholders** with your specific requirements
3. **Add context** from your project (.ai-context/ files)
4. **Paste into Claude Code** or your preferred AI tool
5. **Iterate and refine** the results

### Getting Better Results:
- **Be specific** about requirements and constraints
- **Reference existing patterns** in your codebase
- **Include error scenarios** you want handled
- **Specify testing requirements** upfront
- **Ask for documentation** and usage examples

### Template Customization:
- **Adjust for your tech stack** (different frameworks, libraries)
- **Add your specific conventions** and patterns
- **Include your quality standards** and requirements
- **Reference your existing code** for consistency

---

*These templates are starting points. Customize them based on your project needs and coding standards for best results.*