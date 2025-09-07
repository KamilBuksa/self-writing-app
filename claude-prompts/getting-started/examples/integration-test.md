# Integration Test Guide

Quick 5-minute verification that your AI development setup is working correctly.

## 🎯 Quick Integration Test

This test will verify that you can successfully use AI tools with your project setup.

### Prerequisites
- [ ] Project setup completed
- [ ] `.ai-context/` folder created with project context
- [ ] AI development tools installed (Claude Code, etc.)
- [ ] Basic dependencies installed

## 🧪 Test 1: AI Context Recognition (2 minutes)

### Test the AI's understanding of your project
```
Prompt to use in Claude Code:

"Based on my project context in .ai-context/project-context.md, create a simple utility function that follows our established patterns for:
- TypeScript strict typing
- File naming conventions  
- Import/export patterns
- JSDoc documentation

Create a function that formats a date according to our project standards."
```

### Expected Result
AI should generate code that:
- ✅ Uses TypeScript with proper typing
- ✅ Follows your naming conventions
- ✅ Uses proper imports (with @/ alias if configured)
- ✅ Includes JSDoc documentation
- ✅ References your project's patterns

### Sample Expected Output
```typescript
// utils/date-formatter.ts
import { format } from 'date-fns'

/**
 * Formats a date according to project standards
 * @param date - The date to format
 * @param formatString - The format string (default: 'PPP')
 * @returns Formatted date string
 */
export function formatDate(
  date: Date | string, 
  formatString: string = 'PPP'
): string {
  const dateObj = typeof date === 'string' ? new Date(date) : date
  return format(dateObj, formatString)
}
```

## 🧪 Test 2: Component Generation (2 minutes)

### Test AI component generation with your patterns
```
Prompt to use:

"Following our established component patterns, create a loading button component that:
- Uses our Button component from @/components/ui/button
- Shows loading state with spinner
- Follows our TypeScript and styling conventions
- Includes proper accessibility attributes
- Has variants for different sizes

Name it LoadingButton and make it reusable."
```

### Expected Result
AI should generate:
- ✅ Component that extends your base Button
- ✅ Proper TypeScript interfaces
- ✅ Loading state management
- ✅ Accessibility attributes
- ✅ Variant support with class-variance-authority (if you use it)

### Sample Expected Output
```tsx
// components/ui/loading-button.tsx
import * as React from 'react'
import { Button, type ButtonProps } from '@/components/ui/button'
import { Loader2 } from 'lucide-react'
import { cn } from '@/lib/utils'

interface LoadingButtonProps extends ButtonProps {
  loading?: boolean
  loadingText?: string
}

const LoadingButton = React.forwardRef<HTMLButtonElement, LoadingButtonProps>(
  ({ loading = false, loadingText, children, disabled, ...props }, ref) => {
    return (
      <Button
        ref={ref}
        disabled={loading || disabled}
        {...props}
      >
        {loading && <Loader2 className="mr-2 h-4 w-4 animate-spin" />}
        {loading && loadingText ? loadingText : children}
      </Button>
    )
  }
)
LoadingButton.displayName = 'LoadingButton'

export { LoadingButton }
```

## 🧪 Test 3: API Route Generation (1 minute)

### Test API development assistance
```
Prompt to use:

"Create a Next.js API route for user profile management following our patterns:
- GET /api/profile - get current user profile
- PATCH /api/profile - update user profile
- Use Supabase integration
- Include proper error handling
- Add input validation with Zod
- Follow our API response patterns"
```

### Expected Result
AI should generate:
- ✅ Next.js API route with proper structure
- ✅ Supabase integration following your patterns
- ✅ Zod validation schemas
- ✅ Error handling with your established patterns
- ✅ TypeScript typing throughout

## ✅ Integration Success Criteria

### Pass Criteria (All must be true)
- [ ] AI recognizes your project context and patterns
- [ ] Generated code follows your established conventions
- [ ] Code includes proper TypeScript typing
- [ ] Import/export patterns match your project
- [ ] File naming follows your conventions
- [ ] Error handling matches your patterns

### If Tests Fail

#### Common Issues & Solutions

**Issue: AI doesn't recognize project context**
```yaml
Solution:
  - Check .ai-context/project-context.md exists and is complete
  - Verify you're referencing it in your prompts
  - Add more detail to your project context
  - Make sure AI tool has access to your project files
```

**Issue: Generated code doesn't follow patterns**
```yaml
Solution:
  - Update .ai-context/project-context.md with more specific examples
  - Reference existing files in your prompts
  - Be more explicit about patterns in your prompts
  - Add examples of your preferred code style
```

**Issue: Wrong dependencies or imports**
```yaml
Solution:
  - Update project context with correct dependency list
  - Specify exact import patterns you use
  - Reference your package.json in the context
  - Provide examples of proper import statements
```

## 🚀 Next Steps After Successful Integration

### Immediate Actions
1. **Save successful prompts** to your team prompt library
2. **Document any adjustments** you made to get good results
3. **Share results** with your team
4. **Identify patterns** that work well for your project

### Optimization
1. **Refine your project context** based on test results
2. **Create more specific prompt templates** for your common tasks
3. **Establish team conventions** for AI tool usage
4. **Set up regular reviews** of AI-generated code quality

### Scale Up
1. **Train team members** using these same tests
2. **Create more complex test scenarios** for your specific domain
3. **Establish quality gates** for AI-generated code
4. **Track success metrics** over time

## 🎯 Success Metrics to Track

### Development Speed
```typescript
interface IntegrationMetrics {
  // Time comparisons
  beforeAI: {
    componentCreationTime: number // minutes
    apiRouteCreationTime: number // minutes
    utilityFunctionTime: number // minutes
  }
  afterAI: {
    componentCreationTime: number // minutes
    apiRouteCreationTime: number // minutes  
    utilityFunctionTime: number // minutes
  }
  
  // Quality metrics
  codeQuality: {
    followsPatterns: boolean
    hasProperTypes: boolean
    includesTests: boolean
    hasDocumentation: boolean
  }
}
```

### Example Tracking
```yaml
Week 1 Results:
  Component Creation: 45min → 8min (5.6x faster)
  API Route Creation: 60min → 12min (5x faster)
  Utility Function: 15min → 3min (5x faster)
  Pattern Compliance: 95%
  Team Satisfaction: 8.2/10
```

## 🔄 Continuous Improvement

### Weekly Reviews
- Review AI-generated code quality
- Identify successful prompt patterns
- Update project context based on learnings
- Share best practices with team

### Monthly Assessment
- Measure development speed improvements
- Track code quality metrics
- Survey team satisfaction
- Adjust processes based on feedback

---

*This test should take 5 minutes and give you confidence that your AI development setup is working effectively. If you pass all tests, you're ready for productive AI-assisted development!*