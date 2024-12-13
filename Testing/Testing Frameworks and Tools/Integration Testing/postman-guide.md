# Postman API Testing Guide

## Overview

### Introduction
```yaml
Description:
  Complete API development and testing platform

Key Features:
  - API testing
  - Request collections
  - Automated testing
  - Environment management
  - Test scripts
  - Newman CLI
```

## Request Management

### Basic Requests
```javascript
// GET Request
GET https://api.example.com/users
Headers:
  Content-Type: application/json
  Authorization: Bearer {{token}}

// POST Request
POST https://api.example.com/users
Body:
{
    "name": "John Doe",
    "email": "john@example.com"
}
```

### Request Configuration
```javascript
// Request Headers
{
    "Content-Type": "application/json",
    "Authorization": "Bearer {{token}}",
    "Custom-Header": "value"
}

// Query Parameters
{
    "page": 1,
    "limit": 10,
    "sort": "desc"
}
```

## Test Scripts

### Basic Tests
```javascript
// Response status test
pm.test("Status code is 200", () => {
    pm.response.to.have.status(200);
});

// Response body test
pm.test("Response has correct data", () => {
    const response = pm.response.json();
    pm.expect(response.name).to.equal("John");
    pm.expect(response.id).to.exist;
});
```

### Advanced Testing
```javascript
// Schema validation
pm.test("Schema is valid", () => {
    const schema = {
        type: "object",
        properties: {
            id: { type: "number" },
            name: { type: "string" }
        },
        required: ["id", "name"]
    };
    
    pm.response.to.have.jsonSchema(schema);
});

// Response time test
pm.test("Response time is acceptable", () => {
    pm.expect(pm.response.responseTime).to.be.below(200);
});
```

## Environment Management

### Environment Variables
```javascript
// Setting variables
pm.environment.set("variable_key", "variable_value");
pm.globals.set("global_variable", "value");

// Getting variables
const value = pm.environment.get("variable_key");
const globalValue = pm.globals.get("global_variable");

// Dynamic variables
const timestamp = new Date().getTime();
pm.environment.set("timestamp", timestamp);
```

### Environment Setup
```yaml
Development:
  - base_url: http://dev-api.example.com
  - api_key: dev_key
  - debug: true

Production:
  - base_url: https://api.example.com
  - api_key: prod_key
  - debug: false
```

## Collection Management

### Collection Structure
```yaml
Collection:
  - Folder 1:
      - Request 1
      - Request 2
      - Subfolder:
          - Request 3
          - Request 4
  - Folder 2:
      - Request 5
      - Request 6
```

### Collection Scripts
```javascript
// Pre-request script
pm.sendRequest({
    url: pm.environment.get("auth_url"),
    method: 'POST',
    header: {
        'Content-Type': 'application/json'
    },
    body: {
        mode: 'raw',
        raw: JSON.stringify({
            username: pm.environment.get("username"),
            password: pm.environment.get("password")
        })
    }
}, function (err, res) {
    pm.environment.set("token", res.json().token);
});
```

## Automation

### Newman CLI
```bash
# Run collection
newman run collection.json -e environment.json

# Run with options
newman run collection.json \
    -e environment.json \
    -r html,cli \
    --delay-request 1000

# Run with data file
newman run collection.json \
    -d data.json \
    --iteration-count 5
```

### CI/CD Integration
```yaml
# GitHub Actions Example
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Install Newman
        run: npm install -g newman
      - name: Run API Tests
        run: newman run collection.json -e env.json
```

## Advanced Features

### Request Chaining
```javascript
// Save response data
pm.test("Save user ID", () => {
    const response = pm.response.json();
    pm.environment.set("user_id", response.id);
});

// Use in next request
GET https://api.example.com/users/{{user_id}}/details
```

### Data Driven Testing
```javascript
// CSV Data
[
    {"username": "user1", "password": "pass1"},
    {"username": "user2", "password": "pass2"}
]

// Using data in request
POST https://api.example.com/login
{
    "username": "{{username}}",
    "password": "{{password}}"
}
```

## Best Practices

### Organization
```yaml
Best Practices:
  - Logical folder structure
  - Clear naming conventions
  - Consistent test patterns
  - Environment management
  - Documentation

Test Strategy:
  - Independent tests
  - Clear assertions
  - Error handling
  - Response validation
```

### Maintenance
```yaml
Guidelines:
  - Regular updates
  - Version control
  - Documentation
  - Team sharing

Monitoring:
  - Test results
  - Performance metrics
  - Error tracking
  - Usage patterns
```

## Troubleshooting

### Common Issues
```yaml
Authentication:
  - Token expiration
  - Invalid credentials
  - Missing headers
  
Request Issues:
  - Timeout errors
  - Network problems
  - Invalid data format
```

### Debug Tools
```javascript
// Console logging
console.log("Debug info:", value);

// Request logging
pm.request.headers.all()
pm.request.body

// Response logging
pm.response.headers.all()
pm.response.json()
```
