# Testing Cheat Sheets

Quick reference guides for various testing concepts, tools, and methodologies.

## Testing Types Quick Reference

### Unit Testing
```
✓ Test single components/functions
✓ Independent of external systems
✓ Fast execution
✓ Format: Arrange-Act-Assert
```

### Integration Testing
```
✓ Test component interactions
✓ May involve external systems
✓ Focus on interfaces
✓ Test data flow between modules
```

### End-to-End Testing
```
✓ Test complete workflows
✓ Real environment simulation
✓ User perspective testing
✓ Full system integration
```

## Common Testing Commands

### Jest (JavaScript)
```bash
# Run all tests
npm test

# Run specific test file
npm test path/to/test.js

# Run with coverage
npm test -- --coverage

# Watch mode
npm test -- --watch
```

### PyTest (Python)
```bash
# Run all tests
pytest

# Run specific test file
pytest test_file.py

# Run with verbose output
pytest -v

# Run tests matching pattern
pytest -k "pattern"
```

### JUnit (Java)
```bash
# Run tests
./gradlew test

# Run specific test class
./gradlew test --tests TestClassName

# Run with tag
./gradlew test -Dtest.tags="tagname"
```

## Testing Best Practices

### Test Case Design
- One assertion per test
- Clear test names
- Independent tests
- Consistent naming convention
- Appropriate setup/teardown

### Test Data Management
- Use realistic test data
- Maintain test data separate from code
- Reset data between tests
- Use appropriate test doubles
- Consider edge cases

### Code Coverage Guidelines
```
✓ Line Coverage: >80%
✓ Branch Coverage: >70%
✓ Function Coverage: >90%
✓ Statement Coverage: >85%
```

## Common Testing Patterns

### AAA Pattern
```
Arrange: Set up test conditions
Act: Execute the test
Assert: Verify the results
```

### FIRST Principles
```
Fast: Tests should run quickly
Independent: Tests shouldn't depend on each other
Repeatable: Same results every time
Self-validating: Pass/fail without manual checking
Timely: Written at appropriate time
```

## Common Testing Tools Configuration

### Jest Configuration
```json
{
  "testEnvironment": "node",
  "coverageThreshold": {
    "global": {
      "branches": 80,
      "functions": 80,
      "lines": 80,
      "statements": 80
    }
  },
  "setupFiles": ["./jest.setup.js"],
  "testMatch": ["**/__tests__/**/*.js", "**/?(*.)+(spec|test).js"]
}
```

### PyTest Configuration
```ini
[pytest]
python_files = test_*.py
python_classes = Test*
python_functions = test_*
testpaths = tests
addopts = -v --cov=src
```

## Bug Report Template
```markdown
### Description
[Clear description of the issue]

### Steps to Reproduce
1. [First Step]
2. [Second Step]
3. [Additional Steps...]

### Expected Result
[What should happen]

### Actual Result
[What actually happens]

### Environment
- Browser/Version:
- OS:
- Test Environment:
```

## Performance Testing Metrics
```
Response Time: < 2 seconds
Throughput: > 100 requests/second
Error Rate: < 1%
CPU Usage: < 70%
Memory Usage: < 80%
```

## Quick Debugging Checklist
- [ ] Test environment is correct
- [ ] Test data is valid
- [ ] Test prerequisites are met
- [ ] No interfering background processes
- [ ] Correct test configuration
- [ ] Latest code version is used
- [ ] No network issues
- [ ] Logging is enabled

Remember: These cheat sheets are meant as quick references. Always refer to official documentation for complete information.
