# Quick Reference Card

One-page cheatsheet for maximum AI development productivity. Bookmark this page! 🔖

## ⚡ Most-Used Prompts

### Component Generation
```
Following the patterns in my codebase (.ai-context/), create a [COMPONENT_TYPE] that:
- Uses TypeScript with strict typing
- Follows our Tailwind + shadcn/ui patterns
- Includes proper error handling and loading states
- Has responsive design and accessibility
- Uses our established naming conventions

Functionality: [DESCRIBE WHAT IT DOES]
```

### API Route Creation
```
Create Next.js API route for [RESOURCE] following our patterns:
- Full CRUD operations (GET, POST, PATCH, DELETE)
- Supabase integration with RLS
- Input validation with Zod schemas  
- Proper error handling and status codes
- Authentication checks

Generate: routes + validation + types + tests
```

### Form Component
```
Create a form component using React Hook Form + Zod:
- Fields: [LIST FIELDS WITH TYPES]
- Real-time validation with error messages
- Loading states and success feedback
- Responsive design and accessibility
- Uses our Button/Input components from /components/ui/
```

### Database Schema
```
Create Supabase schema for [DOMAIN]:
- Tables: [LIST ENTITIES]
- Relationships: [DESCRIBE CONNECTIONS]
- RLS policies for security
- Indexes for performance
- Helper functions and triggers
- TypeScript interfaces
```

### Bug Fix
```
Fix this issue in my code:
[PASTE CODE AND DESCRIBE PROBLEM]

Requirements:
- Maintain existing functionality
- Follow our error handling patterns
- Add proper TypeScript types
- Include tests for the fix
- Update documentation if needed
```

## 🎯 Tool Selection Guide

| Task | Best Tool | Why | Time Savings |
|------|-----------|-----|--------------|
| **UI Components** | v0.dev | Visual design + code generation | 10x faster |
| **Business Logic** | Claude Code | Complex reasoning + patterns | 5x faster |
| **API Development** | Claude Code | Full-stack understanding | 8x faster |  
| **Code Review** | Claude Code | Context awareness + suggestions | 3x faster |
| **Debugging** | Claude Code | Problem analysis + solutions | 4x faster |
| **Documentation** | Claude Code | Comprehensive + structured | 6x faster |
| **Testing** | Claude Code | Comprehensive test scenarios | 5x faster |
| **Refactoring** | Claude Code | Pattern recognition + updates | 7x faster |

## 📋 Pattern Lookup Table

### Component Patterns
| Pattern | File Location | Use When |
|---------|---------------|----------|
| Base Component | `/docs/standards/component-patterns.md:15-65` | Building reusable UI |
| Form Component | `/docs/standards/component-patterns.md:67-145` | User input handling |
| Data Display | `/getting-started/examples/common-patterns.md:180-250` | Showing lists/tables |
| Modal/Dialog | `/docs/standards/component-patterns.md:280-350` | Overlays and popups |
| Loading States | `/getting-started/examples/common-patterns.md:15-45` | Async operations |

### API Patterns
| Pattern | File Location | Use When |
|---------|---------------|----------|
| CRUD Operations | `/docs/standards/nextjs-patterns.md:200-280` | Standard data operations |
| Authentication | `/docs/standards/nextjs-patterns.md:350-400` | Route protection |
| Validation | `/docs/standards/supabase-patterns.md:50-100` | Input sanitization |
| Error Handling | `/getting-started/examples/common-patterns.md:400-450` | Robust error responses |

### Database Patterns  
| Pattern | File Location | Use When |
|---------|---------------|----------|
| User Management | `/docs/standards/supabase-patterns.md:15-85` | Authentication system |
| Content Management | `/docs/standards/supabase-patterns.md:90-150` | CMS functionality |
| E-commerce | `/docs/standards/supabase-patterns.md:155-220` | Shopping/payments |
| RLS Policies | `/docs/standards/supabase-patterns.md:225-300` | Data security |

## 🛠️ Troubleshooting Quick Fixes

### AI Not Following Patterns
```yaml
Problem: Generated code doesn't match project style
Quick Fix: Add this to your prompt:
  "Reference the patterns in .ai-context/project-context.md and follow 
   the exact same structure, naming, and conventions used in [EXISTING_FILE]"
```

### Poor Code Quality  
```yaml
Problem: AI generates buggy or inconsistent code
Quick Fix: Add quality requirements:
  "Include comprehensive error handling, proper TypeScript typing, 
   unit tests, and follow our established quality standards"
```

### Wrong Dependencies
```yaml
Problem: AI uses libraries not in your project
Quick Fix: Specify exact dependencies:
  "Only use dependencies already installed in package.json: 
   [LIST YOUR DEPENDENCIES]"
```

### Missing Context
```yaml
Problem: AI doesn't understand your domain
Quick Fix: Provide more context:
  "This is for a [DOMAIN] application where [BUSINESS_CONTEXT]. 
   Users are [USER_TYPE] who need to [USER_GOALS]"
```

### Slow Response
```yaml
Problem: AI takes too long to respond
Quick Fix: Break down the request:
  "First, create just the basic structure. Then I'll ask for 
   the implementation details in separate prompts"
```

## 📊 Success Metrics At-a-Glance

### Development Speed
```
🎯 Target: 2-5x faster development
📈 Track: Feature completion time
✅ Good: 2-3x improvement in first month
🚀 Excellent: 5x+ improvement after 3 months
```

### Code Quality
```
🎯 Target: 90%+ pattern consistency  
📈 Track: Code review feedback
✅ Good: 85%+ consistency
🚀 Excellent: 95%+ consistency + fewer bugs
```

### Team Adoption
```
🎯 Target: 80%+ daily AI tool usage
📈 Track: Team surveys and tool analytics
✅ Good: 60%+ team using tools regularly
🚀 Excellent: 90%+ team reporting productivity gains
```

### Business Impact
```
🎯 Target: 30% faster feature delivery
📈 Track: Sprint velocity and customer satisfaction
✅ Good: 20% improvement in delivery speed
🚀 Excellent: 50%+ improvement + higher quality
```

## ⚡ Emergency Commands

### Quick Project Setup
```bash
# Copy essential templates
cp claude-prompts/getting-started/examples/project-context.md .ai-context/
cp claude-prompts/docs/project-setup.md .
```

### Instant Pattern Reference
```bash
# Find patterns quickly
grep -r "pattern" claude-prompts/docs/standards/
grep -r "template" claude-prompts/getting-started/examples/
```

### Fast Integration Test
```bash
# Verify AI setup works
open claude-prompts/getting-started/examples/integration-test.md
```

## 🔗 Essential Links

| Resource | Link | When to Use |
|----------|------|-------------|
| **Getting Started** | `/getting-started/README.md` | New to AI development |
| **Project Setup** | `/docs/project-setup.md` | Starting new project |
| **Component Patterns** | `/docs/standards/component-patterns.md` | Building UI components |
| **API Patterns** | `/docs/standards/nextjs-patterns.md` | Creating APIs |
| **Database Patterns** | `/docs/standards/supabase-patterns.md` | Database design |
| **Team Adoption** | `/getting-started/team-adoption.md` | Rolling out to team |
| **Prompt Templates** | `/getting-started/examples/prompt-templates.md` | Need specific prompts |
| **Common Patterns** | `/getting-started/examples/common-patterns.md` | Copy-paste code |

## 💡 Pro Tips

### Prompt Engineering
- **Be specific** about requirements and constraints
- **Reference existing code** for consistency
- **Ask for tests** and documentation upfront
- **Iterate and refine** rather than expecting perfection

### Quality Assurance  
- **Always review** AI-generated code before using
- **Test thoroughly** especially edge cases
- **Maintain standards** even with AI assistance
- **Share learnings** with your team

### Team Success
- **Start small** with pilot projects
- **Share wins** to build confidence
- **Document patterns** that work well
- **Continuously improve** based on feedback

---

**🚀 Ready to 10x your development speed? Start with the 5-minute integration test!**

*Print this page and keep it handy - it's your AI development survival guide.*