# Pandas Framework

## Introduction
Pandas is a powerful Python library for data manipulation and analysis. It provides high-performance, easy-to-use data structures and tools for working with structured data.

## Core Data Structures

### DataFrame
```python
import pandas as pd
import numpy as np

# Creating DataFrames
df = pd.DataFrame({
    'name': ['John', 'Alice', 'Bob'],
    'age': [25, 30, 35],
    'salary': [50000, 60000, 75000]
})

# From CSV
df_csv = pd.read_csv('data.csv')

# From Excel
df_excel = pd.read_excel('data.xlsx')
```

### Series
```python
# Creating Series
s = pd.Series([1, 2, 3, 4, 5], name='numbers')

# With custom index
s_indexed = pd.Series(
    [1, 2, 3, 4, 5],
    index=['a', 'b', 'c', 'd', 'e'],
    name='indexed_numbers'
)
```

## Data Manipulation

### Basic Operations
```python
# Filtering
adults = df[df['age'] >= 18]

# Sorting
sorted_df = df.sort_values('age', ascending=False)

# Group operations
grouped = df.groupby('department').agg({
    'salary': ['mean', 'median', 'std'],
    'age': ['min', 'max', 'count']
})

# Adding columns
df['bonus'] = df['salary'] * 0.1
df['full_name'] = df['first_name'] + ' ' + df['last_name']
```

### Data Cleaning
```python
def clean_dataframe(df):
    # Remove duplicates
    df = df.drop_duplicates()
    
    # Handle missing values
    df['age'] = df['age'].fillna(df['age'].mean())
    df['salary'] = df['salary'].fillna(df['salary'].median())
    
    # Remove rows with any remaining NaN values
    df = df.dropna()
    
    # Reset index
    df = df.reset_index(drop=True)
    
    return df

# Handle outliers
def remove_outliers(df, column, n_std=3):
    mean = df[column].mean()
    std = df[column].std()
    
    return df[
        (df[column] >= mean - n_std * std) &
        (df[column] <= mean + n_std * std)
    ]
```

## Data Analysis

### Statistical Operations
```python
# Basic statistics
stats = df.describe()

# Correlation analysis
correlation = df.corr()

# Custom analysis
def analyze_numeric_columns(df):
    numeric_cols = df.select_dtypes(include=[np.number]).columns
    
    analysis = {
        'basic_stats': df[numeric_cols].describe(),
        'correlation': df[numeric_cols].corr(),
        'skewness': df[numeric_cols].skew(),
        'kurtosis': df[numeric_cols].kurtosis()
    }
    
    return analysis
```

### Time Series Analysis
```python
# Date operations
df['date'] = pd.to_datetime(df['date'])
df.set_index('date', inplace=True)

# Resampling
monthly = df.resample('M').mean()
weekly = df.resample('W').sum()

# Rolling windows
def analyze_time_series(df, column, window_size=30):
    return pd.DataFrame({
        'original': df[column],
        'rolling_mean': df[column].rolling(window=window_size).mean(),
        'rolling_std': df[column].rolling(window=window_size).std(),
        'cumulative_sum': df[column].cumsum()
    })
```

## Performance Optimization

### Memory Management
```python
def optimize_dtypes(df):
    # Optimize numeric columns
    for col in df.select_dtypes(include=['int']).columns:
        df[col] = pd.to_numeric(df[col], downcast='integer')
    
    for col in df.select_dtypes(include=['float']).columns:
        df[col] = pd.to_numeric(df[col], downcast='float')
    
    # Convert object to category for low-cardinality columns
    for col in df.select_dtypes(include=['object']):
        if df[col].nunique() / len(df) < 0.5:
            df[col] = df[col].astype('category')
    
    return df

# Chunk processing for large files
def process_large_file(filename, chunksize=10000):
    chunks = []
    for chunk in pd.read_csv(filename, chunksize=chunksize):
        processed = process_chunk(chunk)
        chunks.append(processed)
    
    return pd.concat(chunks)
```

## Advanced Features

### Merging and Joining
```python
def combine_data(df1, df2, join_type='inner'):
    """Combine two dataframes with error handling and validation."""
    try:
        # Validate common columns
        common_cols = set(df1.columns) & set(df2.columns)
        if not common_cols:
            raise ValueError("No common columns for joining")
        
        # Perform merge
        result = pd.merge(
            df1, df2,
            how=join_type,
            indicator=True
        )
        
        # Report on merge results
        merge_report = {
            'total_rows': len(result),
            'left_only': len(result[result['_merge'] == 'left_only']),
            'right_only': len(result[result['_merge'] == 'right_only']),
            'both': len(result[result['_merge'] == 'both'])
        }
        
        return result.drop('_merge', axis=1), merge_report
        
    except Exception as e:
        raise Exception(f"Merge failed: {str(e)}")
```

### Pivot Tables
```python
def create_pivot_analysis(df, index_cols, value_cols):
    """Create comprehensive pivot table analysis."""
    return pd.pivot_table(
        df,
        index=index_cols,
        values=value_cols,
        aggfunc={
            'numeric': ['mean', 'sum', 'count', 'std'],
            'categorical': lambda x: len(x.unique())
        },
        margins=True
    )
```

## Best Practices

### Error Handling
```python
class DataFrameValidator:
    def __init__(self, df):
        self.df = df
        self.errors = []
    
    def validate_no_nulls(self, columns):
        for col in columns:
            if self.df[col].isnull().any():
                self.errors.append(f"Null values found in column: {col}")
    
    def validate_unique(self, columns):
        for col in columns:
            if self.df[col].duplicated().any():
                self.errors.append(f"Duplicate values found in column: {col}")
    
    def validate_range(self, column, min_val, max_val):
        invalid = self.df[
            (self.df[column] < min_val) |
            (self.df[column] > max_val)
        ]
        if not invalid.empty:
            self.errors.append(
                f"Values out of range in column {column}"
            )
    
    def get_validation_report(self):
        return {
            'valid': len(self.errors) == 0,
            'errors': self.errors
        }
```

## Conclusion
Pandas provides a comprehensive set of tools for data manipulation and analysis, making it essential for data science and analysis tasks in Python.
