# Commit Message Guidelines

## Overview
Clear and consistent commit messages are crucial for maintaining a useful version history. Good commit messages help with debugging, code reviews, and generating meaningful changelogs.

## Message Structure

### Basic Format
```
<type>(<scope>): <subject>

<body>

<footer>
```

### Components
1. **Type**
   ```
   feat:     New feature
   fix:      Bug fix
   docs:     Documentation
   style:    Formatting
   refactor: Code restructuring
   perf:     Performance improvement
   test:     Adding tests
   chore:    Maintenance
   ```

2. **Scope**
   ```
   auth:     Authentication
   user:     User management
   api:      API changes
   db:       Database
   config:   Configuration
   ```

3. **Subject**
   ```
   - Imperative mood
   - No capitalization
   - No period at end
   - Maximum 50 characters
   ```

## Writing Guidelines

### Subject Line
1. **Do**
   ```
   feat(auth): add password reset functionality
   fix(api): resolve user session timeout issue
   docs(readme): update installation instructions
   ```

2. **Don't**
   ```
   Fixed bug           # No type
   feat: Added tests   # Not imperative
   style(css): Fixed.  # Ends with period
   ```

### Message Body
1. **Format**
   ```
   - Blank line after subject
   - Wrapped at 72 characters
   - Explain what and why, not how
   - Use bullet points for multiple items
   ```

2. **Content**
   ```
   - Motivation for change
   - Contrast with previous behavior
   - Breaking changes
   - Side effects
   ```

## Examples

### Simple Changes
```
feat(user): add email verification

Implement email verification flow for new user registration.
Includes:
- Verification token generation
- Email sending service integration
- Token validation endpoint
```

### Bug Fixes
```
fix(auth): resolve token expiration issue

The JWT tokens were not checking expiration dates correctly,
leading to invalid sessions being accepted.

Fixes #123
```

### Breaking Changes
```
feat(api)!: change authentication endpoint response

BREAKING CHANGE: Authentication endpoint now returns a JWT
token instead of a session cookie. This change improves
security and enables better mobile client support.

Migration guide:
1. Update client authentication handling
2. Replace cookie parsing with JWT parsing
3. Update security configurations
```

## Special Cases

### Reverting Changes
```
revert: feat(user): add email verification

This reverts commit abc123def456.

Reason for revert:
- Performance issues in production
- High error rate in email delivery
```

### Co-authored Commits
```
feat(db): implement caching layer

Improve database performance with Redis caching.

Co-authored-by: Jane Doe <jane@example.com>
Co-authored-by: John Smith <john@example.com>
```

## Best Practices

### General Guidelines
1. **Commit Scope**
   - Single responsibility
   - Complete functionality
   - Independent changes
   - Testable state

2. **Message Quality**
   ```
   - Clear and concise
   - Proper grammar
   - No redundancy
   - Meaningful content
   ```

### Common Patterns

1. **Feature Development**
   ```
   feat(module): add new capability
   
   Implement new feature that enables users to:
   - Capability one
   - Capability two
   
   Issue: #234
   ```

2. **Bug Fixing**
   ```
   fix(module): resolve specific issue
   
   Problem: Description of the issue
   Solution: Description of the fix
   
   Fixes #345
   ```

3. **Documentation Updates**
   ```
   docs(module): update API documentation
   
   - Add missing endpoints
   - Update response formats
   - Include usage examples
   ```

## Integration

### Issue Tracking
1. **References**
   ```
   Fixes #123
   Relates to #456
   Part of #789
   ```

2. **Keywords**
   ```
   Closes
   Fixes
   Resolves
   Related to
   Implements
   ```

### Automation
1. **CI/CD Triggers**
   ```
   [ci skip]       # Skip CI
   [deploy prod]   # Trigger deployment
   [run tests]     # Run test suite
   ```

2. **Changelog Generation**
   ```
   BREAKING CHANGE:
   Deprecated:
   Migration guide:
   ```

## Review Process

### Validation
1. **Automated Checks**
   ```
   - Message format
   - Length limits
   - Required components
   - Issue references
   ```

2. **Manual Review**
   ```
   - Clear description
   - Proper type/scope
   - Complete information
   - Linked issues
   ```

### Common Issues
1. **Problems to Avoid**
   ```
   - Vague messages
   - Missing context
   - Wrong type/scope
   - Incomplete information
   ```

2. **Solutions**
   ```
   - Use templates
   - Follow guidelines
   - Review before commit
   - Seek feedback
   ```
