```markdown
# blog-resource Development Patterns

> Auto-generated skill from repository analysis

## Overview
This skill covers the core development patterns and conventions used in the `blog-resource` TypeScript codebase. It documents established practices for file naming, import/export style, commit messaging, and testing. While no specific framework or automated workflows are detected, this guide provides clear instructions and code examples to help maintain consistency and efficiency in development.

## Coding Conventions

### File Naming
- Use **camelCase** for file names.
  - Example: `blogResource.ts`, `userProfile.ts`

### Import Style
- Use **relative imports** for importing modules within the project.
  - Example:
    ```typescript
    import { getUser } from './userProfile';
    ```

### Export Style
- Use **named exports** for all modules.
  - Example:
    ```typescript
    // In blogResource.ts
    export function createBlogPost() { ... }
    export const BLOG_RESOURCE_VERSION = '1.0.0';
    ```

### Commit Messages
- Commit messages are **freeform** (no enforced prefix or type).
- Average commit message length: ~36 characters.
  - Example:  
    ```
    Fix typo in blog post title validation
    ```

## Workflows

_No automated workflows detected in this repository._

## Testing Patterns

- **Testing Framework:** Not explicitly detected.
- **Test File Pattern:** Test files are named with the `*.test.*` pattern.
  - Example: `blogResource.test.ts`
- **Test Structure:**  
  - Place test files alongside or within the same directory as the modules they test.
  - Example test file:
    ```typescript
    import { createBlogPost } from './blogResource';

    describe('createBlogPost', () => {
      it('should create a new blog post', () => {
        const post = createBlogPost('Title', 'Content');
        expect(post.title).toBe('Title');
      });
    });
    ```

## Commands

| Command | Purpose |
|---------|---------|
| /run-tests | Run all test files matching `*.test.*` |
| /format-code | Format code according to project conventions |
| /lint | Lint the codebase for style and errors |
| /add-module | Scaffold a new module with camelCase naming and named exports |
```