# Jest Testing Framework

## Introduction
Jest is a delightful JavaScript testing framework with a focus on simplicity. It provides a complete testing solution with built-in assertion library, mocking support, and code coverage reporting.

## Basic Configuration

### Project Setup
```javascript
// jest.config.js
module.exports = {
    verbose: true,
    testEnvironment: 'node',
    moduleFileExtensions: ['js', 'jsx', 'ts', 'tsx'],
    transform: {
        '^.+\\.(ts|tsx)$': 'ts-jest',
        '^.+\\.(js|jsx)$': 'babel-jest',
    },
    testMatch: [
        '**/__tests__/**/*.[jt]s?(x)',
        '**/?(*.)+(spec|test).[jt]s?(x)'
    ],
    collectCoverageFrom: [
        'src/**/*.{js,jsx,ts,tsx}',
        '!src/**/*.d.ts'
    ],
    coverageThreshold: {
        global: {
            branches: 80,
            functions: 80,
            lines: 80,
            statements: 80
        }
    }
};
```

## Writing Tests

### Test Structure
```javascript
// user.test.js
import { User } from './user';

describe('User', () => {
    let user;
    
    beforeEach(() => {
        user = new User('John Doe', 'john@example.com');
    });
    
    describe('profile management', () => {
        test('updates user profile', () => {
            user.updateProfile({ age: 30 });
            expect(user.age).toBe(30);
        });
        
        test('validates email format', () => {
            expect(() => {
                user.updateProfile({ email: 'invalid-email' });
            }).toThrow('Invalid email format');
        });
    });
});
```

### Assertion Examples
```javascript
describe('Assertions', () => {
    test('numeric comparisons', () => {
        expect(2 + 2).toBe(4);
        expect(0.1 + 0.2).toBeCloseTo(0.3);
        expect(4).toBeGreaterThan(3);
    });
    
    test('string matching', () => {
        expect('hello world').toMatch(/world/);
        expect('team').not.toMatch(/I/);
    });
    
    test('array containment', () => {
        const shoppingList = ['apple', 'banana', 'orange'];
        expect(shoppingList).toContain('banana');
        expect(new Set(shoppingList)).toContain('apple');
    });
    
    test('object matching', () => {
        const user = { name: 'John', age: 30 };
        expect(user).toEqual({
            name: 'John',
            age: expect.any(Number)
        });
    });
});
```

## Mocking

### Function Mocking
```javascript
// serviceClient.test.js
import { fetchUserData } from './serviceClient';

jest.mock('./serviceClient');

describe('Service Client', () => {
    beforeEach(() => {
        fetchUserData.mockClear();
    });
    
    test('fetches user data successfully', async () => {
        const mockData = { id: 1, name: 'John' };
        fetchUserData.mockResolvedValue(mockData);
        
        const result = await fetchUserData(1);
        expect(result).toEqual(mockData);
        expect(fetchUserData).toHaveBeenCalledWith(1);
    });
    
    test('handles fetch error', async () => {
        const error = new Error('Network error');
        fetchUserData.mockRejectedValue(error);
        
        await expect(fetchUserData(1)).rejects.toThrow('Network error');
    });
});
```

### Module Mocking
```javascript
// database.test.js
jest.mock('./database', () => ({
    query: jest.fn(),
    connect: jest.fn(),
    disconnect: jest.fn()
}));

import { db } from './database';

describe('Database', () => {
    beforeEach(() => {
        jest.clearAllMocks();
    });
    
    test('executes query successfully', async () => {
        const mockResults = [{ id: 1 }];
        db.query.mockResolvedValue(mockResults);
        
        const results = await db.query('SELECT * FROM users');
        expect(results).toEqual(mockResults);
    });
});
```

## Asynchronous Testing

### Promises
```javascript
describe('Async Operations', () => {
    test('resolves with data', () => {
        return expect(fetchData())
            .resolves
            .toEqual({ data: 'success' });
    });
    
    test('rejects with error', () => {
        return expect(failingOperation())
            .rejects
            .toThrow('Operation failed');
    });
    
    test('async/await syntax', async () => {
        await expect(async () => {
            await riskyOperation();
        }).rejects.toThrow();
    });
});
```

## Test Coverage

### Coverage Configuration
```javascript
// package.json
{
    "jest": {
        "collectCoverage": true,
        "coverageReporters": ["text", "html"],
        "coverageDirectory": "coverage",
        "coverageThreshold": {
            "global": {
                "branches": 80,
                "functions": 80,
                "lines": 80,
                "statements": 80
            }
        }
    }
}
```

## Snapshot Testing

### Component Snapshots
```javascript
import renderer from 'react-test-renderer';
import Button from './Button';

describe('Button', () => {
    test('renders correctly', () => {
        const tree = renderer
            .create(<Button label="Click me" />)
            .toJSON();
        expect(tree).toMatchSnapshot();
    });
    
    test('renders in disabled state', () => {
        const tree = renderer
            .create(<Button label="Click me" disabled />)
            .toJSON();
        expect(tree).toMatchSnapshot();
    });
});
```

## Best Practices

### Test Organization
```javascript
// utils.test.js
describe('Utility Functions', () => {
    // Group related tests
    describe('string manipulation', () => {
        test('capitalizes string', () => {
            expect(capitalize('hello')).toBe('Hello');
        });
        
        test('trims whitespace', () => {
            expect(trim('  hello  ')).toBe('hello');
        });
    });
    
    // Separate setup for different groups
    describe('array operations', () => {
        let testArray;
        
        beforeEach(() => {
            testArray = [1, 2, 3];
        });
        
        test('filters even numbers', () => {
            expect(filterEven(testArray)).toEqual([2]);
        });
    });
});
```

## Conclusion
Jest provides a comprehensive testing solution with an extensive feature set, making it suitable for testing JavaScript applications of any size.
