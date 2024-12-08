# Python Ecosystem and Libraries

## Standard Library

### Core Utilities
1. **Built-in Functions**
   ```python
   # Essential built-ins
   len([1, 2, 3])         # Length of sequence
   range(10)              # Number sequence
   enumerate(['a', 'b'])  # Index-value pairs
   zip([1, 2], ['a', 'b'])  # Combine sequences
   
   # Type conversions
   str(42)                # Convert to string
   int("42")              # Convert to integer
   float("3.14")          # Convert to float
   list("abc")            # Convert to list
   ```

2. **Data Structures**
   ```python
   # Collections module
   from collections import (
       defaultdict,
       Counter,
       deque,
       namedtuple
   )
   
   # Default dictionary
   word_count = defaultdict(int)
   for word in words:
       word_count[word] += 1
   
   # Double-ended queue
   queue = deque(['a', 'b', 'c'])
   queue.append('d')
   queue.appendleft('z')
   ```

### File and System Operations
1. **File Handling**
   ```python
   # File operations
   with open('data.txt', 'r') as f:
       content = f.read()
   
   # Path operations
   from pathlib import Path
   
   path = Path('directory/file.txt')
   if path.exists():
       print(path.read_text())
   ```

2. **System Interaction**
   ```python
   import os
   import sys
   import subprocess
   
   # Environment variables
   os.environ['PATH']
   
   # Command execution
   result = subprocess.run(['ls', '-l'], 
                         capture_output=True)
   ```

## Popular Third-Party Libraries

### Data Science Stack
1. **NumPy and Pandas**
   ```python
   import numpy as np
   import pandas as pd
   
   # NumPy arrays
   arr = np.array([[1, 2, 3], [4, 5, 6]])
   mean = np.mean(arr)
   std = np.std(arr)
   
   # Pandas DataFrames
   df = pd.DataFrame({
       'A': [1, 2, 3],
       'B': ['a', 'b', 'c']
   })
   df.groupby('B').mean()
   ```

2. **Data Visualization**
   ```python
   import matplotlib.pyplot as plt
   import seaborn as sns
   
   # Matplotlib plotting
   plt.plot([1, 2, 3], [1, 4, 9])
   plt.title('Simple Plot')
   plt.show()
   
   # Seaborn statistical plots
   sns.distplot(data)
   sns.heatmap(correlation_matrix)
   ```

### Web Development
1. **Web Frameworks**
   ```python
   # Flask
   from flask import Flask, request
   
   app = Flask(__name__)
   
   @app.route('/api/data', methods=['POST'])
   def handle_data():
       data = request.json
       return {'status': 'success'}
   
   # Django
   from django.db import models
   
   class User(models.Model):
       name = models.CharField(max_length=100)
       email = models.EmailField(unique=True)
   ```

2. **HTTP and APIs**
   ```python
   import requests
   from fastapi import FastAPI
   
   # HTTP requests
   response = requests.get('https://api.example.com/data')
   data = response.json()
   
   # FastAPI
   app = FastAPI()
   
   @app.get("/items/{item_id}")
   async def read_item(item_id: int):
       return {"id": item_id}
   ```

### Machine Learning
1. **Scikit-learn**
   ```python
   from sklearn.model_selection import train_test_split
   from sklearn.linear_model import LogisticRegression
   from sklearn.metrics import accuracy_score
   
   # Model training
   X_train, X_test, y_train, y_test = train_test_split(X, y)
   model = LogisticRegression()
   model.fit(X_train, y_train)
   
   # Evaluation
   predictions = model.predict(X_test)
   accuracy = accuracy_score(y_test, predictions)
   ```

2. **Deep Learning**
   ```python
   import tensorflow as tf
   from tensorflow.keras import layers
   
   # Neural network
   model = tf.keras.Sequential([
       layers.Dense(64, activation='relu'),
       layers.Dense(32, activation='relu'),
       layers.Dense(10, activation='softmax')
   ])
   
   model.compile(optimizer='adam',
                loss='categorical_crossentropy',
                metrics=['accuracy'])
   ```

## Development Tools

### Package Management
1. **pip and virtualenv**
   ```bash
   # Virtual environment
   python -m venv myenv
   source myenv/bin/activate
   
   # Package installation
   pip install numpy pandas
   pip freeze > requirements.txt
   ```

2. **Conda**
   ```yaml
   # environment.yml
   name: myenv
   dependencies:
     - python=3.9
     - numpy
     - pandas
     - scikit-learn
     - pip:
       - custom-package
   ```

### Testing Frameworks
1. **pytest**
   ```python
   # Test functions
   def test_addition():
       assert 1 + 1 == 2
   
   # Fixtures
   import pytest
   
   @pytest.fixture
   def database():
       db = create_test_db()
       yield db
       db.cleanup()
   ```

2. **unittest**
   ```python
   import unittest
   
   class TestMathFunctions(unittest.TestCase):
       def setUp(self):
           self.data = [1, 2, 3]
           
       def test_average(self):
           self.assertEqual(
               calculate_average(self.data),
               2.0
           )
   ```

## Ecosystem Tools

### Documentation
1. **Sphinx**
   ```python
   def complex_function(param1: str, param2: int) -> dict:
       """
       Process data with given parameters.
       
       Args:
           param1: First parameter description
           param2: Second parameter description
           
       Returns:
           Dictionary containing processed data
           
       Raises:
           ValueError: If parameters are invalid
       """
       pass
   ```

2. **Type Hints**
   ```python
   from typing import List, Dict, Optional
   
   def process_data(
       items: List[Dict[str, str]],
       config: Optional[Dict] = None
   ) -> List[str]:
       """Process items with optional configuration."""
       return [item['name'] for item in items]
   ```

## Conclusion
Python's rich ecosystem of libraries and tools makes it a versatile choice for various applications, from web development to data science and machine learning.
