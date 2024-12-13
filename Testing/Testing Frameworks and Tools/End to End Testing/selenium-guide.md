# Selenium Testing Framework

## Overview

### Introduction
```yaml
Description:
  Industry-standard web browser automation and testing framework

Key Features:
  - Multi-browser support
  - Multiple language bindings
  - Browser automation
  - Cross-platform
  - WebDriver protocol
  - Grid support
```

### Setup
```python
# Python installation
pip install selenium

# Java installation
<dependency>
    <groupId>org.seleniumhq.selenium</groupId>
    <artifactId>selenium-java</artifactId>
    <version>4.10.0</version>
</dependency>

# JavaScript installation
npm install selenium-webdriver
```

## Basic Usage

### Browser Initialization
```python
# Chrome WebDriver
from selenium import webdriver
from selenium.webdriver.chrome.service import Service

service = Service('path/to/chromedriver')
driver = webdriver.Chrome(service=service)

# Firefox WebDriver
driver = webdriver.Firefox()

# Edge WebDriver
driver = webdriver.Edge()
```

### Element Location
```python
# Finding elements
from selenium.webdriver.common.by import By

# By ID
element = driver.find_element(By.ID, "search")

# By Class Name
elements = driver.find_elements(By.CLASS_NAME, "result")

# By CSS Selector
element = driver.find_element(By.CSS_SELECTOR, "#main .content")

# By XPath
element = driver.find_element(By.XPATH, "//button[@type='submit']")
```

## Advanced Features

### Wait Mechanisms
```python
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC

# Explicit wait
wait = WebDriverWait(driver, 10)
element = wait.until(
    EC.presence_of_element_located((By.ID, "myElement"))
)

# Custom wait condition
def custom_condition(locator):
    return lambda driver: driver.find_element(*locator).is_enabled()

wait.until(custom_condition((By.ID, "button")))

# Implicit wait
driver.implicitly_wait(10)
```

### Actions
```python
from selenium.webdriver.common.action_chains import ActionChains

# Mouse actions
actions = ActionChains(driver)
actions.move_to_element(element)\
       .click()\
       .perform()

# Keyboard actions
actions.key_down(Keys.CONTROL)\
       .send_keys('a')\
       .key_up(Keys.CONTROL)\
       .perform()

# Drag and drop
actions.drag_and_drop(source, target).perform()
```

## Page Objects

### Basic Structure
```python
class LoginPage:
    def __init__(self, driver):
        self.driver = driver
        self.username = (By.ID, "username")
        self.password = (By.ID, "password")
        self.login_button = (By.CSS_SELECTOR, "button[type='submit']")
    
    def login(self, username, password):
        self.driver.find_element(*self.username).send_keys(username)
        self.driver.find_element(*self.password).send_keys(password)
        self.driver.find_element(*self.login_button).click()
```

### Implementation
```python
# Using page object
login_page = LoginPage(driver)
login_page.login("user@example.com", "password123")

# Page factory pattern
from selenium.webdriver.support.ui import WebDriverWait

class BasePage:
    def __init__(self, driver):
        self.driver = driver
        self.wait = WebDriverWait(driver, 10)

    def find_element(self, locator):
        return self.wait.until(
            EC.presence_of_element_located(locator)
        )
```

## Testing Framework Integration

### PyTest Integration
```python
import pytest

@pytest.fixture
def driver():
    driver = webdriver.Chrome()
    driver.maximize_window()
    yield driver
    driver.quit()

def test_login(driver):
    login_page = LoginPage(driver)
    login_page.login("user", "pass")
    assert "Dashboard" in driver.title
```

### Test Suites
```python
class TestLogin:
    @pytest.fixture(autouse=True)
    def setup(self, driver):
        self.driver = driver
        self.login_page = LoginPage(driver)
    
    def test_valid_login(self):
        self.login_page.login("valid@user.com", "valid_pass")
        assert self.login_page.is_logged_in()
    
    def test_invalid_login(self):
        self.login_page.login("invalid@user.com", "invalid_pass")
        assert self.login_page.has_error_message()
```

## Selenium Grid

### Grid Setup
```yaml
# Docker Compose configuration
services:
  hub:
    image: selenium/hub
    ports:
      - "4444:4444"
  
  chrome:
    image: selenium/node-chrome
    depends_on:
      - hub
    environment:
      - HUB_HOST=hub
```

### Remote WebDriver
```python
from selenium import webdriver
from selenium.webdriver.common.desired_capabilities import DesiredCapabilities

# Remote driver setup
driver = webdriver.Remote(
    command_executor='http://hub:4444/wd/hub',
    desired_capabilities=DesiredCapabilities.CHROME
)

# Capabilities configuration
capabilities = {
    "browserName": "chrome",
    "platformName": "LINUX",
    "browserVersion": "latest"
}
```

## Best Practices

### Element Location
```yaml
Best Practices:
  - Use explicit IDs
  - Prefer CSS over XPath
  - Use meaningful locators
  - Maintain locator repository

Anti-patterns:
  - Complex XPath
  - Fragile selectors
  - Hard-coded waits
  - Duplicate locators
```

### Test Organization
```yaml
Guidelines:
  - Page object pattern
  - Clear test structure
  - Independent tests
  - Proper setup/teardown

Maintenance:
  - Regular updates
  - Browser compatibility
  - Error handling
  - Logging
```

## Error Handling

### Exception Handling
```python
from selenium.common.exceptions import *

try:
    element = driver.find_element(By.ID, "element")
except NoSuchElementException:
    logger.error("Element not found")
except TimeoutException:
    logger.error("Operation timed out")
except WebDriverException as e:
    logger.error(f"WebDriver error: {e}")
```

### Retry Mechanism
```python
from retrying import retry

@retry(stop_max_attempt_number=3, wait_fixed=2000)
def click_element(driver, locator):
    element = driver.find_element(*locator)
    element.click()
    return element
```
