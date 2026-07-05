# Common Coding Standards

## General Guidelines
- Always use meaningful names for variables, functions, and classes
- Functions should do one thing and do it well (Single Responsibility)
- Keep functions small - if a function exceeds 30 lines, consider refactoring
- Prefer composition over inheritance
- Use dependency injection for testability
- Document public APIs with clear JSDoc comments

## Error Handling
- Never swallow errors silently
- Use custom error classes for domain-specific errors
- Always handle promise rejections
- Validate inputs at function boundaries
- Provide meaningful error messages

## Testing
- Write tests for all business logic
- Use descriptive test names (should... when...)
- Follow Arrange-Act-Assert pattern
- Mock external dependencies
- Aim for >80% code coverage

## Performance
- Avoid premature optimization
- Use lazy loading for heavy dependencies
- Implement caching for expensive operations
- Be mindful of memory leaks in closures/event listeners
- Profile before optimizing
