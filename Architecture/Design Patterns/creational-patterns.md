# Creational Design Patterns

## Introduction
Creational design patterns deal with object creation mechanisms, trying to create objects in a manner suitable to the situation. These patterns abstract the instantiation process, making a system independent of how its objects are created, composed, and represented.

## Singleton Pattern

### Intent
Ensure a class has only one instance and provide a global point of access to it.

### Structure
```java
public class Singleton {
    private static Singleton instance;
    private static final Object lock = new Object();
    
    private Singleton() { }
    
    public static Singleton getInstance() {
        if (instance == null) {
            synchronized (lock) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```

### Use Cases
- Database connections
- Configuration managers
- Logging services
- Thread pools
- Caching systems

### Considerations
1. **Thread Safety**
   - Double-check locking
   - Synchronized access
   - Volatile declaration
   - Static initialization
   - Enum implementation

2. **Implementation Variations**
   ```java
   // Eager Initialization
   public class EagerSingleton {
       private static final EagerSingleton instance = new EagerSingleton();
       private EagerSingleton() { }
       public static EagerSingleton getInstance() {
           return instance;
       }
   }
   
   // Enum Singleton
   public enum EnumSingleton {
       INSTANCE;
       public void doSomething() { }
   }
   ```

## Factory Method Pattern

### Intent
Define an interface for creating an object, but let subclasses decide which class to instantiate.

### Structure
```java
public interface Product { }

public class ConcreteProductA implements Product { }
public class ConcreteProductB implements Product { }

public abstract class Creator {
    abstract Product createProduct();
    
    public void someOperation() {
        Product product = createProduct();
        // Use the product
    }
}

public class ConcreteCreatorA extends Creator {
    @Override
    Product createProduct() {
        return new ConcreteProductA();
    }
}
```

### Use Cases
- Plugin architectures
- Framework extensions
- Object family creation
- Product variations
- Configurable systems

### Implementation Example
```java
// Document creation example
interface Document { }
class PDFDocument implements Document { }
class WordDocument implements Document { }

abstract class DocumentCreator {
    abstract Document createDocument();
    
    public void processDocument() {
        Document doc = createDocument();
        // Process the document
    }
}

class PDFCreator extends DocumentCreator {
    @Override
    Document createDocument() {
        return new PDFDocument();
    }
}
```

## Abstract Factory Pattern

### Intent
Provide an interface for creating families of related or dependent objects without specifying their concrete classes.

### Structure
```java
// Abstract products
interface Button { }
interface Checkbox { }

// Concrete products
class WindowsButton implements Button { }
class WindowsCheckbox implements Checkbox { }
class MacButton implements Button { }
class MacCheckbox implements Checkbox { }

// Abstract factory
interface GUIFactory {
    Button createButton();
    Checkbox createCheckbox();
}

// Concrete factories
class WindowsFactory implements GUIFactory {
    @Override
    public Button createButton() {
        return new WindowsButton();
    }
    
    @Override
    public Checkbox createCheckbox() {
        return new WindowsCheckbox();
    }
}
```

### Use Cases
- Cross-platform UI components
- Multiple database support
- Multiple rendering engines
- Theme implementations
- Hardware abstraction

## Builder Pattern

### Intent
Separate the construction of a complex object from its representation.

### Structure
```java
public class Product {
    private String partA;
    private String partB;
    // Additional parts...
    
    public void setPartA(String partA) { this.partA = partA; }
    public void setPartB(String partB) { this.partB = partB; }
}

public interface Builder {
    void buildPartA();
    void buildPartB();
    Product getResult();
}

public class ConcreteBuilder implements Builder {
    private Product product = new Product();
    
    @Override
    public void buildPartA() {
        product.setPartA("Part A");
    }
    
    @Override
    public void buildPartB() {
        product.setPartB("Part B");
    }
    
    @Override
    public Product getResult() {
        return product;
    }
}
```

### Modern Implementation
```java
// Modern builder with fluent interface
public class User {
    private final String firstName;
    private final String lastName;
    private final String email;
    
    private User(Builder builder) {
        this.firstName = builder.firstName;
        this.lastName = builder.lastName;
        this.email = builder.email;
    }
    
    public static class Builder {
        private String firstName;
        private String lastName;
        private String email;
        
        public Builder firstName(String firstName) {
            this.firstName = firstName;
            return this;
        }
        
        public Builder lastName(String lastName) {
            this.lastName = lastName;
            return this;
        }
        
        public Builder email(String email) {
            this.email = email;
            return this;
        }
        
        public User build() {
            return new User(this);
        }
    }
}
```

## Prototype Pattern

### Intent
Specify the kinds of objects to create using a prototypical instance, and create new objects by cloning this prototype.

### Structure
```java
public interface Prototype extends Cloneable {
    Prototype clone();
}

public class ConcretePrototype implements Prototype {
    private String field;
    
    public ConcretePrototype(String field) {
        this.field = field;
    }
    
    @Override
    public Prototype clone() {
        return new ConcretePrototype(this.field);
    }
}
```

### Use Cases
- Object copying
- Complex object creation
- Dynamic object generation
- Performance optimization
- Template objects

## Best Practices

### Pattern Selection
1. **When to Use**
   - Object creation complexity
   - System configuration
   - Performance requirements
   - Flexibility needs
   - Maintenance considerations

2. **Implementation Guidelines**
   - Clear documentation
   - Error handling
   - Thread safety
   - Performance optimization
   - Code organization

### Common Pitfalls
1. **Anti-patterns**
   - Overuse of Singleton
   - Complex factory hierarchies
   - Unnecessary abstraction
   - Poor error handling
   - Thread safety issues

2. **Solutions**
   - Dependency injection
   - Simple factories
   - Clear abstractions
   - Proper error handling
   - Thread-safe design

## Conclusion
Creational patterns provide powerful solutions for object creation problems. Choose the appropriate pattern based on specific requirements and constraints while considering maintenance and flexibility needs.
