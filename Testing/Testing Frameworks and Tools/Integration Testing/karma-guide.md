# Karma Testing Framework

## Overview

### Introduction
```yaml
Description:
  Test runner that can execute JavaScript tests in multiple real browsers

Key Features:
  - Multi-browser testing
  - Real browser testing
  - CI/CD integration
  - Test framework agnostic
  - Watch mode
  - Plugin system
```

### Installation
```bash
# Basic installation
npm install karma --save-dev

# Essential plugins
npm install karma-jasmine karma-chrome-launcher --save-dev
npm install jasmine-core --save-dev

# Additional plugins
npm install karma-coverage --save-dev
npm install karma-junit-reporter --save-dev
```

## Configuration

### Basic Configuration
```javascript
// karma.conf.js
module.exports = function(config) {
  config.set({
    frameworks: ['jasmine'],
    files: [
      'src/**/*.js',
      'test/**/*.spec.js'
    ],
    browsers: ['Chrome'],
    singleRun: true
  });
};
```

### Advanced Configuration
```javascript
module.exports = function(config) {
  config.set({
    // Test frameworks
    frameworks: ['jasmine', 'mocha', 'chai'],
    
    // File patterns
    files: [
      { pattern: 'src/**/*.js', watched: true },
      { pattern: 'test/**/*.spec.js', watched: true }
    ],
    
    // Preprocessing
    preprocessors: {
      'src/**/*.js': ['coverage'],
      'test/**/*.spec.js': ['babel']
    },
    
    // Reporters
    reporters: ['progress', 'coverage', 'junit'],
    
    // Coverage configuration
    coverageReporter: {
      type: 'html',
      dir: 'coverage/'
    }
  });
};
```

## Browser Setup

### Browser Configuration
```javascript
browsers: ['Chrome', 'Firefox', 'Safari'],

customLaunchers: {
  ChromeHeadless: {
    base: 'Chrome',
    flags: [
      '--headless',
      '--disable-gpu',
      '--remote-debugging-port=9222'
    ]
  }
},

// Browser timeout settings
captureTimeout: 60000,
browserDisconnectTimeout: 10000,
browserNoActivityTimeout: 30000
```

### Browser Selection
```javascript
// Multiple browsers
browsers: ['Chrome', 'Firefox'],

// Headless testing
browsers: ['ChromeHeadless', 'FirefoxHeadless'],

// Custom browser setup
customLaunchers: {
  ChromeDebug: {
    base: 'Chrome',
    flags: ['--remote-debugging-port=9333']
  }
}
```

## Test Execution

### Running Tests
```bash
# Basic test run
karma start

# Single run
karma start --single-run

# Specific configuration
karma start karma.conf.js

# Specific browsers
karma start --browsers Chrome,Firefox
```

### Watch Mode
```javascript
// Configuration
module.exports = function(config) {
  config.set({
    autoWatch: true,
    singleRun: false,
    
    // File watching
    watchPlugins: [
      'karma-watch-preprocessor'
    ],
    
    // Watch options
    watchOptions: {
      ignored: /node_modules/
    }
  });
};
```

## Reporters

### Configuration
```javascript
reporters: ['progress', 'coverage', 'junit'],

// Coverage reporter
coverageReporter: {
  dir: 'coverage/',
  reporters: [
    { type: 'html', subdir: 'html' },
    { type: 'lcov', subdir: 'lcov' },
    { type: 'text-summary' }
  ]
},

// JUnit reporter
junitReporter: {
  outputDir: 'test-results/',
  outputFile: 'test-results.xml',
  useBrowserName: false
}
```

### Custom Reporter
```javascript
function CustomReporter(baseReporterDecorator) {
  baseReporterDecorator(this);

  this.onRunComplete = function(browsers, results) {
    // Custom reporting logic
  };

  this.specSuccess = function(browser, result) {
    // Handle successful test
  };
}
```

## Preprocessors

### Setting Up Preprocessors
```javascript
preprocessors: {
  'src/**/*.js': ['coverage', 'babel'],
  'test/**/*.spec.js': ['babel']
},

// Babel configuration
babelPreprocessor: {
  options: {
    presets: ['@babel/preset-env'],
    sourceMap: 'inline'
  }
}
```

### Coverage Configuration
```javascript
coverageReporter: {
  check: {
    global: {
      statements: 80,
      branches: 80,
      functions: 80,
      lines: 80
    }
  },
  watermarks: {
    statements: [50, 75],
    functions: [50, 75],
    branches: [50, 75],
    lines: [50, 75]
  }
}
```

## Integration

### CI/CD Integration
```yaml
# GitHub Actions
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Install dependencies
        run: npm install
      - name: Run tests
        run: npm test

# Jenkins Pipeline
pipeline {
    agent any
    stages {
        stage('Test') {
            steps {
                sh 'npm install'
                sh 'npm test'
            }
        }
    }
}
```

### Plugin Integration
```javascript
// Plugin configuration
plugins: [
  'karma-jasmine',
  'karma-chrome-launcher',
  'karma-coverage',
  'karma-junit-reporter',
  require('./karma-custom-plugin')
],

// Custom plugin options
customPlugin: {
  option1: 'value1',
  option2: 'value2'
}
```

## Best Practices

### Configuration Best Practices
```yaml
Guidelines:
  - Modular configuration
  - Environment-specific settings
  - Clear file patterns
  - Appropriate timeouts
  - Error handling

Performance:
  - Efficient file watching
  - Browser optimization
  - Resource management
  - Parallel execution
```

### Test Organization
```yaml
Structure:
  - Logical test grouping
  - Clear file naming
  - Proper dependencies
  - Resource management

Maintenance:
  - Regular updates
  - Clean configuration
  - Plugin management
  - Documentation
```
