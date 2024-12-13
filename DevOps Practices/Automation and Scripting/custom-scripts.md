# Custom Scripts

## Introduction

Custom scripts are essential tools in DevOps for automating specific tasks and workflows. This guide covers best practices, patterns, and examples for creating and maintaining custom scripts.

## Script Development

### 1. Basic Template
```python
#!/usr/bin/env python3

"""
Script Name: example_script.py
Description: Template for custom script development
Author: DevOps Team
Date: 2024-03-15
"""

import argparse
import logging
import sys
from typing import Optional, List

# Configure logging
logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s'
)
logger = logging.getLogger(__name__)

def parse_arguments() -> argparse.Namespace:
    """Parse command line arguments."""
    parser = argparse.ArgumentParser(description='Script description')
    parser.add_argument('--input', required=True, help='Input file path')
    parser.add_argument('--output', required=True, help='Output file path')
    parser.add_argument('--verbose', action='store_true', help='Enable verbose output')
    return parser.parse_args()

def main() -> int:
    """Main function."""
    try:
        args = parse_arguments()
        if args.verbose:
            logger.setLevel(logging.DEBUG)
        
        # Main logic here
        logger.info('Script completed successfully')
        return 0
    except Exception as e:
        logger.error(f'Script failed: {str(e)}')
        return 1

if __name__ == '__main__':
    sys.exit(main())
```

### 2. Error Handling
```bash
#!/bin/bash

# Bash script error handling template
set -e  # Exit on error
set -u  # Exit on undefined variable
set -o pipefail  # Exit on pipe failure

# Error handler
trap 'echo "Error occurred at line $LINENO"' ERR

# Function with error handling
function process_file() {
    local file=$1
    if [[ ! -f "$file" ]]; then
        echo "Error: File $file not found" >&2
        return 1
    fi
    
    # Process file
    echo "Processing $file"
}
```

## Configuration Management

### 1. Configuration File
```yaml
# config.yaml
application:
  name: CustomScript
  version: 1.0.0
  
environment:
  dev:
    database:
      host: localhost
      port: 5432
  prod:
    database:
      host: db.example.com
      port: 5432

logging:
  level: INFO
  file: logs/app.log
```

### 2. Configuration Loader
```python
import yaml
import os
from typing import Dict, Any

class Config:
    def __init__(self, config_path: str):
        self.config_path = config_path
        self.config: Dict[str, Any] = {}
        self.load_config()
    
    def load_config(self) -> None:
        """Load configuration from YAML file."""
        if not os.path.exists(self.config_path):
            raise FileNotFoundError(f"Config file not found: {self.config_path}")
        
        with open(self.config_path, 'r') as f:
            self.config = yaml.safe_load(f)
    
    def get(self, key: str, default: Any = None) -> Any:
        """Get configuration value."""
        return self.config.get(key, default)
```

## Logging and Monitoring

### 1. Advanced Logging
```python
import logging.handlers
import os

def setup_logging(
    log_file: str,
    level: str = 'INFO',
    max_bytes: int = 1048576,
    backup_count: int = 5
) -> logging.Logger:
    """Configure rotating file logger."""
    logger = logging.getLogger('custom_script')
    logger.setLevel(getattr(logging, level.upper()))
    
    # Create logs directory if it doesn't exist
    os.makedirs(os.path.dirname(log_file), exist_ok=True)
    
    # File handler with rotation
    handler = logging.handlers.RotatingFileHandler(
        log_file,
        maxBytes=max_bytes,
        backupCount=backup_count
    )
    
    # Format
    formatter = logging.Formatter(
        '%(asctime)s - %(name)s - %(levelname)s - %(message)s'
    )
    handler.setFormatter(formatter)
    
    logger.addHandler(handler)
    return logger
```

### 2. Performance Monitoring
```python
import time
from functools import wraps
from typing import Callable, Any

def measure_time(func: Callable) -> Callable:
    """Decorator to measure function execution time."""
    @wraps(func)
    def wrapper(*args: Any, **kwargs: Any) -> Any:
        start_time = time.time()
        result = func(*args, **kwargs)
        end_time = time.time()
        
        logger.info(
            f"Function {func.__name__} took {end_time - start_time:.2f} seconds"
        )
        return result
    return wrapper
```

## File Operations

### 1. File Processing
```python
import os
import shutil
from typing import List, Optional

class FileProcessor:
    def __init__(self, input_dir: str, output_dir: str):
        self.input_dir = input_dir
        self.output_dir = output_dir
        os.makedirs(output_dir, exist_ok=True)
    
    def process_files(self, extension: Optional[str] = None) -> List[str]:
        """Process files in input directory."""
        processed_files = []
        
        for filename in os.listdir(self.input_dir):
            if extension and not filename.endswith(extension):
                continue
                
            input_path = os.path.join(self.input_dir, filename)
            output_path = os.path.join(self.output_dir, filename)
            
            try:
                self._process_file(input_path, output_path)
                processed_files.append(filename)
            except Exception as e:
                logger.error(f"Error processing {filename}: {str(e)}")
        
        return processed_files
    
    def _process_file(self, input_path: str, output_path: str) -> None:
        """Process individual file."""
        # Implementation specific to file type
        pass
```

### 2. Backup Operations
```python
import tarfile
from datetime import datetime

class BackupManager:
    def __init__(self, backup_dir: str):
        self.backup_dir = backup_dir
        os.makedirs(backup_dir, exist_ok=True)
    
    def create_backup(self, source_dir: str) -> str:
        """Create backup of source directory."""
        timestamp = datetime.now().strftime('%Y%m%d_%H%M%S')
        backup_file = os.path.join(
            self.backup_dir,
            f'backup_{timestamp}.tar.gz'
        )
        
        with tarfile.open(backup_file, 'w:gz') as tar:
            tar.add(source_dir, arcname=os.path.basename(source_dir))
        
        return backup_file
    
    def restore_backup(self, backup_file: str, restore_dir: str) -> None:
        """Restore from backup file."""
        with tarfile.open(backup_file, 'r:gz') as tar:
            tar.extractall(path=restore_dir)
```

## Database Operations

### 1. Database Connection
```python
import psycopg2
from contextlib import contextmanager
from typing import Generator

class DatabaseManager:
    def __init__(self, config: Dict[str, Any]):
        self.config = config
    
    @contextmanager
    def get_connection(self) -> Generator[psycopg2.extensions.connection, None, None]:
        """Database connection context manager."""
        conn = None
        try:
            conn = psycopg2.connect(**self.config)
            yield conn
            conn.commit()
        except Exception as e:
            if conn:
                conn.rollback()
            raise
        finally:
            if conn:
                conn.close()
    
    def execute_query(self, query: str, params: Optional[tuple] = None) -> List[tuple]:
        """Execute database query."""
        with self.get_connection() as conn:
            with conn.cursor() as cur:
                cur.execute(query, params)
                return cur.fetchall()
```

## Testing Framework

### 1. Unit Tests
```python
import unittest
from unittest.mock import Mock, patch

class TestCustomScript(unittest.TestCase):
    def setUp(self):
        """Set up test environment."""
        self.config = Config('test_config.yaml')
        self.processor = FileProcessor('input', 'output')
    
    def test_file_processing(self):
        """Test file processing functionality."""
        test_file = 'test.txt'
        result = self.processor.process_files('.txt')
        self.assertIn(test_file, result)
    
    @patch('custom_script.DatabaseManager')
    def test_database_operations(self, mock_db):
        """Test database operations with mocking."""
        mock_db.execute_query.return_value = [(1, 'test')]
        result = mock_db.execute_query('SELECT * FROM test')
        self.assertEqual(len(result), 1)
```

### 2. Integration Tests
```python
class TestIntegration(unittest.TestCase):
    @classmethod
    def setUpClass(cls):
        """Set up test environment."""
        cls.temp_dir = tempfile.mkdtemp()
        cls.config = Config('test_config.yaml')
    
    @classmethod
    def tearDownClass(cls):
        """Clean up test environment."""
        shutil.rmtree(cls.temp_dir)
    
    def test_end_to_end(self):
        """Test complete workflow."""
        # Implementation of end-to-end test
        pass
```

## Conclusion

Custom scripts are powerful tools for automation when developed with proper structure, error handling, and testing. Following these patterns ensures maintainable and reliable scripts.

## Next Steps
1. Identify automation needs
2. Plan script structure
3. Implement core functionality
4. Add error handling
5. Write tests
6. Document usage
