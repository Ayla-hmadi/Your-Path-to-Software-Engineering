# Mocha and Chai Testing Guide

## Overview
Mocha is a feature-rich JavaScript test framework running on Node.js and in the browser, making asynchronous testing simple and fun. Chai is a BDD/TDD assertion library that can be paired with any JavaScript testing framework.

## Installation

```bash
# Install Mocha and Chai
npm install --save-dev mocha chai
```

## Basic Test Structure

```javascript
const chai = require('chai');
const expect = chai.expect;

describe('Array', () => {
  describe('#indexOf()', () => {
    it('should return -1 when the value is not present', () => {
      expect([1, 2, 3].indexOf(4)).to.equal(-1);
    });
  });
});
```

## Key Concepts

### Mocha Hooks
- `before()`: Runs once before all tests
- `after()`: Runs once after all tests
- `beforeEach()`: Runs before each test
- `afterEach()`: Runs after each test

```javascript
describe('User Authentication', () => {
  before(() => {
    // Setup test database connection
  });

  beforeEach(() => {
    // Clear test data
  });

  after(() => {
    // Close database connection
  });
});
```

### Chai Assertion Styles

1. **Assert Style**
```javascript
const assert = chai.assert;
assert.equal(foo, 'bar');
assert.lengthOf(foo, 3);
assert.property(tea, 'flavors');
```

2. **Expect Style**
```javascript
const expect = chai.expect;
expect(foo).to.equal('bar');
expect(tea).to.have.property('flavors');
expect([1, 2, 3]).to.include(2);
```

3. **Should Style**
```javascript
chai.should();
foo.should.equal('bar');
tea.should.have.property('flavors');
```

## Asynchronous Testing

### Promise-Based Tests
```javascript
describe('Async operations', () => {
  it('should handle promises', () => {
    return fetch('https://api.example.com/data')
      .then(response => {
        expect(response.status).to.equal(200);
      });
  });
});
```

### Async/Await Tests
```javascript
describe('Async operations', () => {
  it('should handle async/await', async () => {
    const response = await fetch('https://api.example.com/data');
    expect(response.status).to.equal(200);
  });
});
```

## Best Practices

1. **Test Organization**
   - Group related tests using `describe` blocks
   - Use clear, descriptive test names
   - Keep tests isolated and independent

2. **Assertion Best Practices**
   - Test one concept per test case
   - Use appropriate assertions for the data type
   - Include both positive and negative test cases

3. **Code Coverage**
   ```bash
   # Install NYC (Istanbul) for code coverage
   npm install --save-dev nyc
   
   # Run tests with coverage
   nyc mocha
   ```

## Common Testing Patterns

### Testing API Endpoints
```javascript
const chai = require('chai');
const chaiHttp = require('chai-http');
const app = require('../app');

chai.use(chaiHttp);
const expect = chai.expect;

describe('API Endpoints', () => {
  it('should GET all users', () => {
    return chai.request(app)
      .get('/api/users')
      .then(res => {
        expect(res).to.have.status(200);
        expect(res.body).to.be.an('array');
      });
  });
});
```

### Testing React Components
```javascript
import { expect } from 'chai';
import { shallow } from 'enzyme';
import MyComponent from './MyComponent';

describe('MyComponent', () => {
  it('should render correctly', () => {
    const wrapper = shallow(<MyComponent />);
    expect(wrapper.find('.my-class')).to.have.length(1);
  });
});
```

## Troubleshooting

Common issues and solutions:
- **Timeout errors**: Increase timeout duration using `this.timeout(5000)`
- **Async errors**: Ensure proper use of done() callback or return promises
- **Hook failures**: Check for proper cleanup in after/afterEach hooks

## Resources

- [Mocha Documentation](https://mochajs.org/)
- [Chai Documentation](https://www.chaijs.com/)
- [Chai HTTP Plugin](https://www.chaijs.com/plugins/chai-http/)
