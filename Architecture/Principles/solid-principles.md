# SOLID Principles

## Introduction
SOLID is a mnemonic acronym representing five fundamental principles of object-oriented programming and design. These principles, introduced by Robert C. Martin, facilitate the creation of maintainable, understandable, and flexible software.

## Single Responsibility Principle (SRP)

### Definition
"A class should have one, and only one, reason to change."

### Key Concepts
1. **Responsibility Focus**
   - One primary purpose
   - Clear functionality
   - Specific role
   - Cohesive operations
   - Logical grouping

2. **Change Management**
   - Isolated changes
   - Minimal impact
   - Predictable modifications
   - Reduced side effects
   - Easier maintenance

### Example
```java
// Bad Example
class UserManager {
    public void createUser(User user) { /* ... */ }
    public void saveUserToDatabase(User user) { /* ... */ }
    public void sendWelcomeEmail(User user) { /* ... */ }
    public void generateUserReport(User user) { /* ... */ }
}

// Good Example
class UserCreator {
    public void createUser(User user) { /* ... */ }
}

class UserRepository {
    public void saveUser(User user) { /* ... */ }
}

class UserNotifier {
    public void sendWelcomeEmail(User user) { /* ... */ }
}

class UserReporter {
    public void generateReport(User user) { /* ... */ }
}
```

## Open/Closed Principle (OCP)

### Definition
"Software entities should be open for extension but closed for modification."

### Key Concepts
1. **Extension Mechanisms**
   - Interface-based design
   - Abstract base classes
   - Plugin architecture
   - Strategy pattern
   - Template pattern

2. **Modification Prevention**
   - Stable interfaces
   - Encapsulated implementation
   - Protected variations
   - Version stability
   - Backwards compatibility

### Example
```java
// Bad Example
class PaymentProcessor {
    public void processPayment(String type) {
        if (type.equals("credit")) {
            // process credit payment
        } else if (type.equals("debit")) {
            // process debit payment
        }
        // Adding new payment type requires modifying this class
    }
}

// Good Example
interface PaymentMethod {
    void processPayment();
}

class CreditPayment implements PaymentMethod {
    public void processPayment() { /* ... */ }
}

class DebitPayment implements PaymentMethod {
    public void processPayment() { /* ... */ }
}

// New payment methods can be added without modifying existing code
class CryptoPayment implements PaymentMethod {
    public void processPayment() { /* ... */ }
}
```

## Liskov Substitution Principle (LSP)

### Definition
"Subtypes must be substitutable for their base types."

### Key Concepts
1. **Behavioral Compatibility**
   - Contract adherence
   - Type substitution
   - Invariant preservation
   - Precondition weakening
   - Postcondition strengthening

2. **Design Guidelines**
   - Interface compliance
   - Inheritance appropriateness
   - Method signature compatibility
   - Exception handling consistency
   - State preservation

### Example
```java
// Bad Example
class Bird {
    public void fly() { /* ... */ }
}

class Penguin extends Bird {
    public void fly() {
        throw new UnsupportedOperationException(); // Violates LSP
    }
}

// Good Example
interface Flyable {
    void fly();
}

class Bird {
    // Common bird properties
}

class Sparrow extends Bird implements Flyable {
    public void fly() { /* ... */ }
}

class Penguin extends Bird {
    public void swim() { /* ... */ }
}
```

## Interface Segregation Principle (ISP)

### Definition
"Clients should not be forced to depend on interfaces they do not use."

### Key Concepts
1. **Interface Design**
   - Minimal interfaces
   - Client-specific contracts
   - Role interfaces
   - Focused functionality
   - Clear purpose

2. **Implementation Benefits**
   - Reduced coupling
   - Simplified testing
   - Easier maintenance
   - Flexible evolution
   - Clear dependencies

### Example
```java
// Bad Example
interface Worker {
    void work();
    void eat();
    void sleep();
}

// Good Example
interface Workable {
    void work();
}

interface Eatable {
    void eat();
}

interface Sleepable {
    void sleep();
}

class Human implements Workable, Eatable, Sleepable {
    public void work() { /* ... */ }
    public void eat() { /* ... */ }
    public void sleep() { /* ... */ }
}

class Robot implements Workable {
    public void work() { /* ... */ }
}
```

## Dependency Inversion Principle (DIP)

### Definition
"High-level modules should not depend on low-level modules. Both should depend on abstractions."

### Key Concepts
1. **Abstraction Dependency**
   - Interface-based design
   - Abstract coupling
   - Implementation independence
   - Flexible configuration
   - Plugin architecture

2. **Inversion Benefits**
   - Reduced coupling
   - Enhanced testability
   - Easier maintenance
   - Flexible evolution
   - Module independence

### Example
```java
// Bad Example
class EmailNotifier {
    public void sendNotification(String message) {
        // Send email directly
    }
}

class NotificationService {
    private EmailNotifier emailNotifier = new EmailNotifier();
    
    public void notify(String message) {
        emailNotifier.sendNotification(message);
    }
}

// Good Example
interface NotificationStrategy {
    void sendNotification(String message);
}

class EmailNotifier implements NotificationStrategy {
    public void sendNotification(String message) {
        // Send email
    }
}

class SMSNotifier implements NotificationStrategy {
    public void sendNotification(String message) {
        // Send SMS
    }
}

class NotificationService {
    private NotificationStrategy notifier;
    
    public NotificationService(NotificationStrategy notifier) {
        this.notifier = notifier;
    }
    
    public void notify(String message) {
        notifier.sendNotification(message);
    }
}
```

## Best Practices

### Implementation Guidelines
1. **Design Phase**
   - Early consideration
   - Architecture alignment
   - Interface design
   - Component separation
   - Responsibility mapping

2. **Code Review**
   - Principle adherence
   - Violation detection
   - Refactoring opportunities
   - Pattern recognition
   - Design improvement

### Common Pitfalls
1. **Over-engineering**
   - Unnecessary abstraction
   - Complex hierarchies
   - Excessive interfaces
   - Premature optimization
   - Pattern abuse

2. **Under-engineering**
   - Tight coupling
   - Mixed responsibilities
   - Rigid design
   - Direct dependencies
   - Inflexible structure

## Conclusion
SOLID principles provide a foundation for creating maintainable and flexible software. Their proper application leads to more robust and adaptable systems.
