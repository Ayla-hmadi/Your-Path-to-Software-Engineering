# Cypress Testing Framework

## Overview

### Introduction
```yaml
Description:
  Modern end-to-end testing framework for web applications

Key Features:
  - Real-time reloading
  - Automatic waiting
  - Network stubbing
  - Time travel debugging
  - Screenshot capture
  - Video recording
```

### Installation
```bash
# NPM installation
npm install cypress --save-dev

# Yarn installation
yarn add cypress --dev

# Opening Cypress
npx cypress open

# Running headless
npx cypress run
```

## Basic Testing

### Test Structure
```javascript
// Basic test structure
describe('My First Test', () => {
  it('Visits the homepage', () => {
    cy.visit('/')
    cy.contains('Welcome').should('be.visible')
  })
})

// Before and after hooks
beforeEach(() => {
  cy.visit('/')
  cy.login()
})

afterEach(() => {
  cy.clearCookies()
})
```

### Selectors and Actions
```javascript
// Basic selectors
cy.get('#myId')
cy.get('.myClass')
cy.get('[data-testid=myElement]')

// Chainable actions
cy.get('button')
  .should('be.visible')
  .and('contain', 'Click me')
  .click()

// Form interactions
cy.get('input[name="email"]')
  .type('user@example.com')
  .should('have.value', 'user@example.com')
```

## Advanced Features

### Custom Commands
```javascript
// Define custom command
Cypress.Commands.add('login', (email, password) => {
  cy.get('#email').type(email)
  cy.get('#password').type(password)
  cy.get('#login-button').click()
})

// Using custom command
cy.login('user@example.com', 'password123')

// Command with options
Cypress.Commands.add('dataCy', (value) => {
  return cy.get(`[data-cy=${value}]`)
})
```

### Network Requests
```javascript
// Intercepting requests
cy.intercept('GET', '/api/users', { fixture: 'users.json' })

// Waiting for requests
cy.intercept('POST', '/api/save').as('saveRequest')
cy.get('#save').click()
cy.wait('@saveRequest')

// Modifying responses
cy.intercept('GET', '/api/data', (req) => {
  req.reply({
    statusCode: 200,
    body: { modified: true }
  })
})
```

## Configuration

### cypress.config.js
```javascript
const { defineConfig } = require('cypress')

module.exports = defineConfig({
  e2e: {
    baseUrl: 'http://localhost:3000',
    viewportWidth: 1280,
    viewportHeight: 720,
    defaultCommandTimeout: 5000,
    requestTimeout: 10000,
    responseTimeout: 30000,
    
    setupNodeEvents(on, config) {
      // Node event listeners
    }
  },
  
  env: {
    apiUrl: 'http://api.example.com',
    auth_token: 'xxx'
  }
})
```

### Environment Variables
```javascript
// Using environment variables
cy.visit(Cypress.env('BASE_URL'))
cy.request({
  url: `${Cypress.env('API_URL')}/users`,
  headers: {
    'Authorization': `Bearer ${Cypress.env('AUTH_TOKEN')}`
  }
})
```

## Testing Patterns

### Authentication
```javascript
// Login helper
const login = () => {
  cy.session('user', () => {
    cy.visit('/login')
    cy.get('#email').type(Cypress.env('USER_EMAIL'))
    cy.get('#password').type(Cypress.env('USER_PASSWORD'))
    cy.get('#login-button').click()
  })
}

// Preserve login state
beforeEach(() => {
  cy.session('user', login)
})
```

### API Testing
```javascript
// Basic API test
describe('API Tests', () => {
  it('fetches users', () => {
    cy.request('/api/users')
      .its('status')
      .should('equal', 200)
  })
  
  it('creates a user', () => {
    cy.request('POST', '/api/users', {
      name: 'John Doe',
      email: 'john@example.com'
    }).then((response) => {
      expect(response.body).to.have.property('id')
    })
  })
})
```

## Visual Testing

### Screenshots and Videos
```javascript
// Screenshot commands
cy.screenshot()
cy.screenshot('login-page')

// Screenshot configuration
module.exports = defineConfig({
  screenshotOnRunFailure: true,
  screenshotsFolder: 'cypress/screenshots',
  video: true,
  videosFolder: 'cypress/videos',
  videoCompression: 32
})
```

### Visual Testing
```javascript
// Visual regression testing
cy.compareSnapshot('homepage')

// Element snapshot
cy.get('.header')
  .compareSnapshot('header')

// Configuration
module.exports = {
  imageConfig: {
    threshold: 0.1,
    thresholdType: 'percent'
  }
}
```

## Performance Testing

### Performance Monitoring
```javascript
// Measuring performance
cy.window().then((win) => {
  const performance = win.performance
  const navigation = performance.getEntriesByType('navigation')[0]
  expect(navigation.duration).to.be.lessThan(3000)
})

// Resource timing
cy.window().then((win) => {
  const resources = win.performance.getEntriesByType('resource')
  resources.forEach((resource) => {
    expect(resource.duration).to.be.lessThan(1000)
  })
})
```

## Best Practices

### Test Organization
```yaml
Guidelines:
  - Clear test structure
  - Single responsibility
  - Independent tests
  - Meaningful descriptions
  
File Organization:
  - Feature-based grouping
  - Shared commands
  - Common utilities
  - Fixtures organization
```

### Performance Optimization
```yaml
Best Practices:
  - Minimize waits
  - Efficient selectors
  - Resource caching
  - State preservation
  
Anti-patterns:
  - Unnecessary visits
  - Complex setup
  - Tight coupling
  - Slow selectors
```

## Debugging

### Debug Tools
```javascript
// Debugging commands
cy.debug()
cy.pause()

// Console output
cy.log('Debug message')

// Time travel debugging
cy.get('.element').click().then(() => {
  debugger
})
```
