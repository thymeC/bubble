# Test Theory Complete Guide

## Testing Fundamentals

### What is Testing?
Testing is the process of evaluating a system or its components to find whether it satisfies specified requirements or to identify differences between expected and actual results.

## Testing Types by Knowledge Level

### Black-Box Testing
**Definition:** Testing without knowledge of internal code structure, focusing on functionality and behavior.

#### Techniques:
- **Equivalence Partitioning:** Divide input data into valid/invalid partitions
- **Boundary Value Analysis:** Test values at boundaries of partitions
- **Decision Table Testing:** Test combinations of inputs and conditions
- **State Transition Testing:** Test system behavior in different states
- **Use Case Testing:** Test complete user scenarios

#### Example:
```python
# Testing a login function without seeing its code
def test_login_blackbox():
    # Valid credentials
    assert login("user@example.com", "password123") == True
    
    # Invalid email format
    assert login("invalid-email", "password123") == False
    
    # Empty password
    assert login("user@example.com", "") == False
    
    # Boundary: very long email
    long_email = "a" * 100 + "@example.com"
    assert login(long_email, "password123") == False
```

### White-Box Testing
**Definition:** Testing with full knowledge of internal code structure, logic, and implementation.

#### Techniques:
- **Statement Coverage:** Execute every statement at least once
- **Branch Coverage:** Execute every branch of conditional statements
- **Path Coverage:** Execute every possible path through the code
- **Condition Coverage:** Test every condition in decision statements
- **Loop Coverage:** Test loop boundaries and iterations

#### Example:
```python
def calculate_discount(age, membership_years, total_amount):
    discount = 0
    if age >= 65:  # Branch 1
        discount += 10
    if membership_years >= 5:  # Branch 2
        discount += 5
    if total_amount >= 1000:  # Branch 3
        discount += 15
    return min(discount, 25)  # Cap at 25%

# White-box test cases to cover all branches
def test_calculate_discount_whitebox():
    # Test all branches true
    assert calculate_discount(70, 10, 1500) == 25  # All conditions met
    
    # Test only age branch
    assert calculate_discount(70, 2, 500) == 10
    
    # Test only membership branch
    assert calculate_discount(30, 10, 500) == 5
    
    # Test only amount branch
    assert calculate_discount(30, 2, 1500) == 15
    
    # Test no branches
    assert calculate_discount(30, 2, 500) == 0
```

### Gray-Box Testing
**Definition:** Testing with partial knowledge of internal structure, combining black-box and white-box approaches.

## Testing Levels

### Unit Testing
**Definition:** Testing individual components or functions in isolation.

#### Characteristics:
- **Scope:** Smallest testable unit (function, method, class)
- **Speed:** Fast execution
- **Isolation:** No external dependencies
- **Purpose:** Verify component logic and behavior

#### Best Practices:
```python
import unittest
from unittest.mock import Mock, patch

class UserServiceTest(unittest.TestCase):
    
    def setUp(self):
        """Setup test fixtures before each test"""
        self.user_service = UserService()
        self.mock_db = Mock()
        self.user_service.db = self.mock_db
    
    def test_create_user_success(self):
        """Test successful user creation"""
        # Arrange
        user_data = {"name": "John", "email": "john@example.com"}
        self.mock_db.save.return_value = {"id": 1, **user_data}
        
        # Act
        result = self.user_service.create_user(user_data)
        
        # Assert
        self.assertEqual(result["id"], 1)
        self.assertEqual(result["name"], "John")
        self.mock_db.save.assert_called_once_with(user_data)
    
    def test_create_user_duplicate_email(self):
        """Test user creation with duplicate email"""
        # Arrange
        user_data = {"name": "John", "email": "existing@example.com"}
        self.mock_db.save.side_effect = DuplicateEmailError()
        
        # Act & Assert
        with self.assertRaises(DuplicateEmailError):
            self.user_service.create_user(user_data)
    
    def tearDown(self):
        """Clean up after each test"""
        self.mock_db.reset_mock()
```

### Integration Testing
**Definition:** Testing the interaction between multiple components or modules.

#### Types:
- **Big Bang Integration:** Test all components together
- **Top-Down Integration:** Test from top-level components down
- **Bottom-Up Integration:** Test from lower-level components up
- **Sandwich Integration:** Combine top-down and bottom-up

#### Example:
```python
class OrderIntegrationTest(unittest.TestCase):
    
    def test_order_creation_integration(self):
        """Test complete order creation flow"""
        # Test integration between OrderService, PaymentService, and InventoryService
        order_service = OrderService()
        payment_service = PaymentService()
        inventory_service = InventoryService()
        
        # Create order
        order = order_service.create_order({
            "user_id": 1,
            "items": [{"product_id": 1, "quantity": 2}]
        })
        
        # Verify payment processing
        payment = payment_service.process_payment(order.id, "credit_card")
        self.assertEqual(payment.status, "completed")
        
        # Verify inventory update
        inventory = inventory_service.get_product_inventory(1)
        self.assertEqual(inventory.available_quantity, 8)  # Was 10, now 8
```

### System Testing
**Definition:** Testing the complete system as a whole against requirements.

#### Focus Areas:
- **Functional Requirements:** Does the system do what it's supposed to do?
- **Non-Functional Requirements:** Performance, security, usability
- **User Acceptance:** Does it meet user needs?
- **Business Process:** Does it support business workflows?

#### Example:
```python
class SystemTest(unittest.TestCase):
    
    def test_complete_ecommerce_workflow(self):
        """Test complete e-commerce system workflow"""
        # 1. User registration
        user = self.register_user("test@example.com", "password123")
        
        # 2. Product browsing
        products = self.browse_products(category="electronics")
        self.assertGreater(len(products), 0)
        
        # 3. Add to cart
        cart = self.add_to_cart(user.id, products[0].id, quantity=2)
        self.assertEqual(cart.total_items, 2)
        
        # 4. Checkout process
        order = self.checkout(user.id, cart.id, payment_method="credit_card")
        self.assertEqual(order.status, "confirmed")
        
        # 5. Order confirmation
        confirmation = self.get_order_confirmation(order.id)
        self.assertIsNotNone(confirmation.email_sent)
```

### Acceptance Testing
**Definition:** Testing to determine if the system satisfies business requirements.

#### Types:
- **User Acceptance Testing (UAT):** End users test the system
- **Business Acceptance Testing (BAT):** Business stakeholders test
- **Alpha Testing:** Internal testing by development team
- **Beta Testing:** External testing by selected users

## Functional Testing

### API Testing
**Definition:** Testing application programming interfaces for functionality, reliability, performance, and security.

#### Test Types:
```python
import requests
import json

class APITestCase(unittest.TestCase):
    
    def setUp(self):
        self.base_url = "https://api.example.com"
        self.headers = {"Content-Type": "application/json"}
    
    def test_get_user_api(self):
        """Test GET /users/{id} endpoint"""
        # Arrange
        user_id = 1
        
        # Act
        response = requests.get(f"{self.base_url}/users/{user_id}")
        
        # Assert
        self.assertEqual(response.status_code, 200)
        data = response.json()
        self.assertEqual(data["id"], user_id)
        self.assertIn("name", data)
        self.assertIn("email", data)
    
    def test_create_user_api(self):
        """Test POST /users endpoint"""
        # Arrange
        user_data = {
            "name": "John Doe",
            "email": "john@example.com",
            "password": "secure123"
        }
        
        # Act
        response = requests.post(
            f"{self.base_url}/users",
            headers=self.headers,
            data=json.dumps(user_data)
        )
        
        # Assert
        self.assertEqual(response.status_code, 201)
        data = response.json()
        self.assertIn("id", data)
        self.assertEqual(data["name"], user_data["name"])
        self.assertEqual(data["email"], user_data["email"])
    
    def test_update_user_api(self):
        """Test PUT /users/{id} endpoint"""
        # Arrange
        user_id = 1
        update_data = {"name": "Jane Doe"}
        
        # Act
        response = requests.put(
            f"{self.base_url}/users/{user_id}",
            headers=self.headers,
            data=json.dumps(update_data)
        )
        
        # Assert
        self.assertEqual(response.status_code, 200)
        data = response.json()
        self.assertEqual(data["name"], update_data["name"])
    
    def test_delete_user_api(self):
        """Test DELETE /users/{id} endpoint"""
        # Arrange
        user_id = 1
        
        # Act
        response = requests.delete(f"{self.base_url}/users/{user_id}")
        
        # Assert
        self.assertEqual(response.status_code, 204)
        
        # Verify user is deleted
        get_response = requests.get(f"{self.base_url}/users/{user_id}")
        self.assertEqual(get_response.status_code, 404)
```

#### API Testing Tools:
- **Postman:** GUI-based API testing
- **RestAssured:** Java-based API testing
- **Pytest:** Python-based API testing
- **Newman:** Command-line Postman runner
- **JMeter:** Load testing for APIs

### Database Testing
**Definition:** Testing database integrity, data consistency, and data manipulation operations.

#### Test Areas:
```python
import sqlite3
import pytest

class DatabaseTest(unittest.TestCase):
    
    def setUp(self):
        self.conn = sqlite3.connect(':memory:')
        self.cursor = self.conn.cursor()
        self.setup_database()
    
    def setup_database(self):
        """Create test database schema"""
        self.cursor.execute('''
            CREATE TABLE users (
                id INTEGER PRIMARY KEY,
                name TEXT NOT NULL,
                email TEXT UNIQUE NOT NULL,
                created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
            )
        ''')
        self.conn.commit()
    
    def test_insert_user(self):
        """Test user insertion"""
        # Act
        self.cursor.execute(
            "INSERT INTO users (name, email) VALUES (?, ?)",
            ("John Doe", "john@example.com")
        )
        self.conn.commit()
        
        # Assert
        self.cursor.execute("SELECT COUNT(*) FROM users")
        count = self.cursor.fetchone()[0]
        self.assertEqual(count, 1)
    
    def test_unique_email_constraint(self):
        """Test unique email constraint"""
        # Arrange
        self.cursor.execute(
            "INSERT INTO users (name, email) VALUES (?, ?)",
            ("John Doe", "john@example.com")
        )
        self.conn.commit()
        
        # Act & Assert
        with self.assertRaises(sqlite3.IntegrityError):
            self.cursor.execute(
                "INSERT INTO users (name, email) VALUES (?, ?)",
                ("Jane Doe", "john@example.com")  # Same email
            )
            self.conn.commit()
    
    def test_data_integrity(self):
        """Test data integrity after operations"""
        # Arrange
        self.cursor.execute(
            "INSERT INTO users (name, email) VALUES (?, ?)",
            ("John Doe", "john@example.com")
        )
        self.conn.commit()
        
        # Act
        self.cursor.execute("UPDATE users SET name = ? WHERE email = ?", 
                           ("Jane Doe", "john@example.com"))
        self.conn.commit()
        
        # Assert
        self.cursor.execute("SELECT name FROM users WHERE email = ?", 
                           ("john@example.com",))
        name = self.cursor.fetchone()[0]
        self.assertEqual(name, "Jane Doe")
    
    def tearDown(self):
        self.conn.close()
```

## Performance Testing

### Performance Test Types

#### 1. Load Testing
**Definition:** Testing system behavior under expected load.

```python
import time
import threading
import requests

class LoadTest:
    
    def __init__(self, target_url, num_users, duration):
        self.target_url = target_url
        self.num_users = num_users
        self.duration = duration
        self.results = []
    
    def simulate_user(self):
        """Simulate a single user making requests"""
        start_time = time.time()
        while time.time() - start_time < self.duration:
            try:
                start = time.time()
                response = requests.get(self.target_url)
                end = time.time()
                
                self.results.append({
                    'status_code': response.status_code,
                    'response_time': end - start,
                    'timestamp': start
                })
                
                time.sleep(1)  # Wait 1 second between requests
            except Exception as e:
                self.results.append({
                    'error': str(e),
                    'timestamp': time.time()
                })
    
    def run_load_test(self):
        """Run the load test with multiple users"""
        threads = []
        for i in range(self.num_users):
            thread = threading.Thread(target=self.simulate_user)
            threads.append(thread)
            thread.start()
        
        for thread in threads:
            thread.join()
        
        return self.analyze_results()
    
    def analyze_results(self):
        """Analyze test results"""
        successful_requests = [r for r in self.results if 'status_code' in r]
        failed_requests = [r for r in self.results if 'error' in r]
        
        if successful_requests:
            response_times = [r['response_time'] for r in successful_requests]
            avg_response_time = sum(response_times) / len(response_times)
            max_response_time = max(response_times)
            min_response_time = min(response_times)
        else:
            avg_response_time = max_response_time = min_response_time = 0
        
        return {
            'total_requests': len(self.results),
            'successful_requests': len(successful_requests),
            'failed_requests': len(failed_requests),
            'success_rate': len(successful_requests) / len(self.results) * 100,
            'avg_response_time': avg_response_time,
            'max_response_time': max_response_time,
            'min_response_time': min_response_time
        }
```

#### 2. Stress Testing
**Definition:** Testing system behavior beyond normal capacity.

#### 3. Endurance Testing
**Definition:** Testing system behavior over extended periods.

#### 4. Spike Testing
**Definition:** Testing system response to sudden load spikes.

### Performance Metrics

#### Key Performance Indicators (KPIs):

| Metric | Description | Formula | Target |
|--------|-------------|---------|---------|
| **Response Time** | Time to receive first byte | End time - Start time | < 200ms |
| **Throughput** | Requests per second | Total requests / Total time | > 1000 RPS |
| **Error Rate** | Percentage of failed requests | Failed requests / Total requests | < 1% |
| **Availability** | System uptime percentage | Uptime / Total time | > 99.9% |
| **Concurrent Users** | Number of simultaneous users | Active sessions | Based on requirements |
| **CPU Usage** | Processor utilization | CPU time / Total time | < 80% |
| **Memory Usage** | RAM utilization | Used RAM / Total RAM | < 85% |
| **Database Connections** | Active DB connections | Count of active connections | < Max connections |

#### Performance Testing Tools:
- **JMeter:** Apache JMeter for load testing
- **Gatling:** Scala-based performance testing
- **K6:** Modern JavaScript-based load testing
- **Artillery:** Node.js-based load testing
- **Locust:** Python-based load testing

## Security Testing

### Security Test Types

#### 1. Vulnerability Assessment
**Definition:** Identifying security vulnerabilities in the system.

#### 2. Penetration Testing
**Definition:** Attempting to exploit vulnerabilities to assess security.

#### 3. Security Scanning
**Definition:** Automated scanning for known security issues.

### Common Security Test Cases:

```python
class SecurityTest(unittest.TestCase):
    
    def test_sql_injection(self):
        """Test for SQL injection vulnerability"""
        malicious_input = "'; DROP TABLE users; --"
        
        response = requests.post("/api/users", data={
            "username": malicious_input,
            "password": "password123"
        })
        
        # Should not cause database error or data loss
        self.assertNotEqual(response.status_code, 500)
    
    def test_xss_vulnerability(self):
        """Test for Cross-Site Scripting vulnerability"""
        malicious_script = "<script>alert('XSS')</script>"
        
        response = requests.post("/api/comments", data={
            "comment": malicious_script
        })
        
        # Response should not contain the script
        self.assertNotIn(malicious_script, response.text)
    
    def test_authentication_bypass(self):
        """Test authentication bypass attempts"""
        # Try to access protected resource without authentication
        response = requests.get("/api/admin/users")
        self.assertEqual(response.status_code, 401)
        
        # Try with invalid token
        response = requests.get("/api/admin/users", 
                              headers={"Authorization": "Bearer invalid_token"})
        self.assertEqual(response.status_code, 401)
    
    def test_input_validation(self):
        """Test input validation and sanitization"""
        # Test with oversized input
        oversized_input = "a" * 10000
        
        response = requests.post("/api/users", data={
            "name": oversized_input,
            "email": "test@example.com"
        })
        
        # Should handle oversized input gracefully
        self.assertNotEqual(response.status_code, 500)
```

## Test Automation

### Test Automation Framework

#### 1. Test Pyramid
```
        /\
       /  \     E2E Tests (Few)
      /____\    
     /      \   Integration Tests (Some)
    /________\  
   /          \ Unit Tests (Many)
  /____________\
```

#### 2. Test Automation Best Practices:

```python
# Page Object Model Example
class LoginPage:
    def __init__(self, driver):
        self.driver = driver
        self.username_field = driver.find_element(By.ID, "username")
        self.password_field = driver.find_element(By.ID, "password")
        self.login_button = driver.find_element(By.ID, "login-btn")
    
    def login(self, username, password):
        self.username_field.clear()
        self.username_field.send_keys(username)
        self.password_field.clear()
        self.password_field.send_keys(password)
        self.login_button.click()
    
    def get_error_message(self):
        return self.driver.find_element(By.CLASS_NAME, "error-message").text

# Test using Page Object
class LoginTest(unittest.TestCase):
    
    def setUp(self):
        self.driver = webdriver.Chrome()
        self.login_page = LoginPage(self.driver)
    
    def test_successful_login(self):
        self.driver.get("https://example.com/login")
        self.login_page.login("valid_user", "valid_password")
        
        # Verify successful login
        self.assertIn("dashboard", self.driver.current_url)
    
    def test_failed_login(self):
        self.driver.get("https://example.com/login")
        self.login_page.login("invalid_user", "invalid_password")
        
        # Verify error message
        error_message = self.login_page.get_error_message()
        self.assertIn("Invalid credentials", error_message)
    
    def tearDown(self):
        self.driver.quit()
```

## Test Management

### Test Planning
1. **Test Strategy:** Overall approach to testing
2. **Test Plan:** Detailed testing approach for a project
3. **Test Cases:** Specific test scenarios
4. **Test Data:** Data required for testing

### Test Execution
1. **Test Environment Setup**
2. **Test Execution Schedule**
3. **Defect Management**
4. **Test Reporting**

### Test Metrics
- **Test Coverage:** Percentage of code/requirements covered
- **Defect Density:** Defects per KLOC (thousand lines of code)
- **Test Execution Rate:** Tests executed per day
- **Defect Detection Rate:** Defects found per test case

## Continuous Testing

### CI/CD Integration
```yaml
# Example GitHub Actions workflow
name: Test Suite
on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    
    - name: Set up Python
      uses: actions/setup-python@v2
      with:
        python-version: 3.9
    
    - name: Install dependencies
      run: |
        pip install -r requirements.txt
        pip install pytest pytest-cov
    
    - name: Run unit tests
      run: pytest tests/unit/ --cov=src --cov-report=xml
    
    - name: Run integration tests
      run: pytest tests/integration/
    
    - name: Run API tests
      run: pytest tests/api/
    
    - name: Upload coverage
      uses: codecov/codecov-action@v1
      with:
        file: ./coverage.xml
```

## Best Practices for Test Engineers

### 1. Test Design Principles
- **Test Independence:** Each test should be independent
- **Test Repeatability:** Tests should produce same results
- **Test Maintainability:** Tests should be easy to update
- **Test Clarity:** Tests should be self-documenting

### 2. Test Data Management
- Use realistic test data
- Maintain test data separately
- Clean up test data after tests
- Use data factories for test data generation

### 3. Test Environment Management
- Maintain separate test environments
- Automate environment setup
- Version control environment configurations
- Monitor environment health

### 4. Defect Management
- Clear defect reporting
- Proper defect categorization
- Defect tracking and metrics
- Root cause analysis

### 5. Test Automation Strategy
- Start with unit tests
- Automate repetitive tests
- Maintain test automation code
- Regular test suite maintenance

## Conclusion

Testing is a critical component of software development that ensures quality, reliability, and user satisfaction. A comprehensive testing strategy should include multiple testing types, proper test automation, and continuous improvement based on metrics and feedback.

The key to successful testing is understanding the different testing types, when to use them, and how to implement them effectively in your development process.
