# NumPy Library

## Introduction
NumPy (Numerical Python) is a fundamental package for scientific computing in Python. It provides support for large multi-dimensional arrays and matrices, along with a vast collection of mathematical functions.

## Core Features

### Array Creation
```python
import numpy as np

# Basic array creation
arr = np.array([1, 2, 3, 4, 5])
matrix = np.array([[1, 2, 3], [4, 5, 6]])

# Special arrays
zeros = np.zeros((3, 4))
ones = np.ones((2, 3))
identity = np.eye(3)
random = np.random.rand(3, 3)

# Array from range
range_arr = np.arange(0, 10, 2)  # [0, 2, 4, 6, 8]
linspace = np.linspace(0, 1, 5)  # 5 evenly spaced points
```

### Array Operations
```python
# Basic operations
a = np.array([1, 2, 3])
b = np.array([4, 5, 6])

addition = a + b
multiplication = a * b
dot_product = np.dot(a, b)

# Matrix operations
matrix_a = np.array([[1, 2], [3, 4]])
matrix_b = np.array([[5, 6], [7, 8]])

matrix_product = np.matmul(matrix_a, matrix_b)
# or using @
matrix_product = matrix_a @ matrix_b

# Element-wise operations
squared = np.square(a)
sqrt = np.sqrt(a)
exp = np.exp(a)
log = np.log(a)
```

## Advanced Features

### Broadcasting
```python
def demonstrate_broadcasting():
    # Create a 3x3 array
    matrix = np.array([[1, 2, 3],
                      [4, 5, 6],
                      [7, 8, 9]])
    
    # Add a scalar
    matrix_plus_1 = matrix + 1
    
    # Multiply with a 1D array
    scale_factors = np.array([2, 3, 4])
    scaled_matrix = matrix * scale_factors
    
    return {
        'original': matrix,
        'plus_1': matrix_plus_1,
        'scaled': scaled_matrix
    }
```

### Indexing and Slicing
```python
def array_manipulation(arr):
    """Demonstrate various indexing and slicing operations."""
    
    # Basic slicing
    first_row = arr[0]
    first_col = arr[:, 0]
    sub_matrix = arr[1:3, 1:3]
    
    # Boolean indexing
    positive_values = arr[arr > 0]
    
    # Fancy indexing
    indices = np.array([0, 2])
    selected_rows = arr[indices]
    
    # Setting values
    arr[arr < 0] = 0  # Replace negative values with zero
    
    return {
        'first_row': first_row,
        'first_col': first_col,
        'sub_matrix': sub_matrix,
        'positive_values': positive_values,
        'selected_rows': selected_rows
    }
```

## Linear Algebra

### Matrix Operations
```python
def linear_algebra_operations(matrix):
    """Perform common linear algebra operations."""
    
    # Compute eigenvalues and eigenvectors
    eigenvalues, eigenvectors = np.linalg.eig(matrix)
    
    # Matrix decomposition (SVD)
    U, S, Vh = np.linalg.svd(matrix)
    
    # Solve linear equations (Ax = b)
    b = np.array([1, 2, 3])
    x = np.linalg.solve(matrix, b)
    
    # Matrix inverse
    inverse = np.linalg.inv(matrix)
    
    # Matrix rank
    rank = np.linalg.matrix_rank(matrix)
    
    return {
        'eigenvalues': eigenvalues,
        'eigenvectors': eigenvectors,
        'svd': (U, S, Vh),
        'solution': x,
        'inverse': inverse,
        'rank': rank
    }
```

### Statistical Operations
```python
def statistical_analysis(data):
    """Perform statistical operations on numpy arrays."""
    
    stats = {
        'mean': np.mean(data, axis=0),
        'median': np.median(data, axis=0),
        'std': np.std(data, axis=0),
        'var': np.var(data, axis=0),
        'percentiles': np.percentile(data, [25, 50, 75], axis=0),
        'correlation': np.corrcoef(data.T),
        'covariance': np.cov(data.T)
    }
    
    return stats
```

## Performance Optimization

### Vectorization
```python
def optimize_calculations():
    """Compare vectorized and non-vectorized operations."""
    
    # Non-vectorized (slow)
    def slow_distance(x1, y1, x2, y2):
        result = []
        for i in range(len(x1)):
            result.append(np.sqrt((x2[i] - x1[i])**2 + 
                                (y2[i] - y1[i])**2))
        return np.array(result)
    
    # Vectorized (fast)
    def fast_distance(x1, y1, x2, y2):
        return np.sqrt((x2 - x1)**2 + (y2 - y1)**2)
    
    # Memory management
    def efficient_operations(large_array):
        # Use in-place operations when possible
        large_array *= 2  # Instead of large_array = large_array * 2
        
        # Use built-in methods
        mean = large_array.mean()  # Instead of np.mean(large_array)
        
        return mean
```

### Memory Management
```python
def memory_efficient_processing(data):
    """Demonstrate memory-efficient array operations."""
    
    # View vs Copy
    view = data[::2]  # Creates a view
    copy = data[::2].copy()  # Creates a copy
    
    # Memory mapping for large arrays
    mmap_array = np.memmap('temp.dat', dtype='float64', 
                          mode='w+', shape=(1000, 1000))
    
    # Clean up
    del mmap_array
    import os
    os.remove('temp.dat')
```

## Best Practices

### Error Handling
```python
def safe_array_operations(arr1, arr2):
    """Demonstrate safe array operations with error handling."""
    
    try:
        # Check shapes
        if arr1.shape != arr2.shape:
            raise ValueError("Arrays must have same shape")
        
        # Check for NaN values
        if np.isnan(arr1).any() or np.isnan(arr2).any():
            raise ValueError("Arrays contain NaN values")
        
        # Perform operations
        result = {
            'sum': np.add(arr1, arr2),
            'product': np.multiply(arr1, arr2),
            'quotient': np.divide(arr1, arr2)
        }
        
        return result
        
    except Exception as e:
        print(f"Error in array operations: {str(e)}")
        return None
```

## Conclusion
NumPy provides essential functionality for numerical computing in Python, with efficient operations on large arrays and matrices, making it fundamental for scientific computing and data analysis.
