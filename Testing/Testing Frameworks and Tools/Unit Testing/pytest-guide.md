# Pytest Testing Framework

## Overview

### Introduction
```yaml
Description:
  Python testing framework emphasizing simplicity, scalability, and reusability

Key Features:
  - Simple syntax
  - Rich fixture system
  - Parameterized testing
  - Plugin ecosystem
  - Detailed assertions
  - Auto-discovery
```

### Installation
```bash
# Basic installation
pip install pytest

# With coverage
pip install pytest pytest-cov

# Common plugins
pip install pytest-xdist  # parallel testing
pip install pytest-mock   # mocking support
```

## Basic Testing

### Test Structure
```python
# Simple test function
def test_addition():
    assert 1 + 1 == 2

# Test class
class TestCalculator:
    def test_add(self):
        assert Calculator.add(2, 2) == 4
    
    def test_subtract(self):
        assert Calculator.subtract(5, 3) == 2
```

### Assertions
```python
# Basic assertions
assert value == expected
assert value != unexpected
assert condition

# Compare values
assert a > b
assert x in y
assert isinstance(obj, Class)

# Exceptions
with pytest.raises(ValueError):
    raise ValueError("message")
```

## Advanced Features

### Fixtures
```python
# Basic fixture
@pytest.fixture
def calculator():
    return Calculator()

# Using fixtures
def test_calculation(calculator):
    assert calculator.add(2, 2) == 4

# Fixture with teardown
@pytest.fixture
def database():
    db = Database()
    yield db
    db.cleanup()

# Fixture scopes
@pytest.fixture(scope="module")
def expensive_resource():
    resource = create_resource()
    yield resource
    cleanup_resource(resource)
```

### Parameterized Testing
```python
# Simple parameterization
@pytest.mark.parametrize("input,expected", [
    (2, 4),
    (3, 6),
    (4, 8)
])
def test_double(input, expected):
    assert double(input) == expected

# Multiple parameters
@pytest.mark.parametrize("x,y,result", [
    (1, 2, 3),
    (2, 3, 5),
    (5, 5, 10)
])
def test_add(x, y, result):
    assert add(x, y) == result
```

## Test Organization

### Test Discovery
```python
# File naming
test_*.py
*_test.py

# Class naming
Test*
*Test

# Function naming
test_*
*_test
```

### Test Selection
```bash
# Run specific test
pytest test_file.py::test_function

# Run tests by marker
pytest -m slow

# Run tests by name pattern
pytest -k "test_add"

# Run tests in directory
pytest tests/
```

## Configuration

### pytest.ini
```ini
[pytest]
markers =
    slow: marks tests as slow
    integration: marks tests as integration tests

testpaths = tests
python_files = test_*.py
python_classes = Test*
python_functions = test_*

addopts = 
    --verbose
    --tb=short
```

### Conftest.py
```python
# Shared fixtures
@pytest.fixture(scope="session")
def app():
    return create_app()

# Custom markers
def pytest_configure(config):
    config.addinivalue_line(
        "markers", "integration: mark test as integration test"
    )
```

## Advanced Testing

### Mocking
```python
# Using mocker fixture
def test_api_call(mocker):
    mock_get = mocker.patch('requests.get')
    mock_get.return_value.json.return_value = {'key': 'value'}
    result = make_api_call()
    assert result == {'key': 'value'}

# Mock context manager
with mock.patch('module.function') as mock_func:
    mock_func.return_value = 'mocked'
    assert function() == 'mocked'
```

### Async Testing
```python
# Async test
@pytest.mark.asyncio
async def test_async_function():
    result = await async_function()
    assert result == expected

# Async fixture
@pytest.fixture
async def async_resource():
    resource = await create_resource()
    yield resource
    await cleanup_resource(resource)
```

## Test Coverage

### Coverage Configuration
```ini
[coverage:run]
source = src
omit = tests/*

[coverage:report]
exclude_lines =
    pragma: no cover
    def __repr__
    raise NotImplementedError
```

### Running Coverage
```bash
# Run with coverage
pytest --cov=src

# Generate HTML report
pytest --cov=src --cov-report=html

# Enforce minimum coverage
pytest --cov=src --cov-fail-under=90
```

## Best Practices

### Test Structure
```python
class TestFeature:
    def setup_method(self):
        # Setup for each test
        pass
    
    def teardown_method(self):
        # Cleanup after each test
        pass
    
    def test_specific_case(self):
        # Test implementation
        pass
```

### Fixture Guidelines
```yaml
Best Practices:
  - Keep fixtures focused
  - Use appropriate scope
  - Clear naming
  - Document purpose
  - Proper cleanup

Organization:
  - Reusable fixtures
  - Modular design
  - Proper dependencies
  - Efficient setup
```

## Performance Optimization

### Parallel Testing
```bash
# Run tests in parallel
pytest -n auto

# Run specific number of workers
pytest -n 4

# Distribute by module
pytest --dist=loadfile
```

### Test Execution
```yaml
Optimization Tips:
  - Use appropriate fixtures
  - Minimize setup/teardown
  - Efficient resource use
  - Smart test organization
  - Proper isolation
```
