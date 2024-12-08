# Structural Design Patterns

## Introduction
Structural patterns deal with object composition and typically identify simple ways to realize relationships between different objects. These patterns focus on how classes and objects are composed to form larger structures while keeping these structures flexible and efficient.

## Adapter Pattern

### Intent
Convert the interface of a class into another interface that clients expect. Adapter lets classes work together that couldn't otherwise because of incompatible interfaces.

### Structure
```java
// Target interface that the client expects
interface Target {
    void request();
}

// Existing class with incompatible interface
class Adaptee {
    void specificRequest() {
        System.out.println("Specific request");
    }
}

// Class adapter implementation
class ClassAdapter extends Adaptee implements Target {
    @Override
    public void request() {
        specificRequest();
    }
}

// Object adapter implementation
class ObjectAdapter implements Target {
    private Adaptee adaptee;
    
    public ObjectAdapter(Adaptee adaptee) {
        this.adaptee = adaptee;
    }
    
    @Override
    public void request() {
        adaptee.specificRequest();
    }
}
```

### Use Cases
- Legacy system integration
- Third-party library adaptation
- Interface compatibility
- System migration
- API wrapper creation

## Bridge Pattern

### Intent
Decouple an abstraction from its implementation so that the two can vary independently.

### Structure
```java
// Abstraction
abstract class Shape {
    protected Renderer renderer;
    
    protected Shape(Renderer renderer) {
        this.renderer = renderer;
    }
    
    abstract void draw();
}

// Implementation interface
interface Renderer {
    void renderShape();
}

// Concrete implementations
class VectorRenderer implements Renderer {
    @Override
    public void renderShape() {
        System.out.println("Rendering shape in vector");
    }
}

class RasterRenderer implements Renderer {
    @Override
    public void renderShape() {
        System.out.println("Rendering shape in raster");
    }
}

// Refined abstraction
class Circle extends Shape {
    public Circle(Renderer renderer) {
        super(renderer);
    }
    
    @Override
    void draw() {
        renderer.renderShape();
    }
}
```

### Use Cases
- Platform independence
- Graphics and GUI frameworks
- Persistent data access
- Device drivers
- Cross-platform development

## Composite Pattern

### Intent
Compose objects into tree structures to represent part-whole hierarchies. Composite lets clients treat individual objects and compositions uniformly.

### Structure
```java
// Component interface
interface Component {
    void operation();
    void add(Component component);
    void remove(Component component);
    Component getChild(int index);
}

// Leaf class
class Leaf implements Component {
    @Override
    public void operation() {
        System.out.println("Leaf operation");
    }
    
    @Override
    public void add(Component component) {
        throw new UnsupportedOperationException();
    }
    
    @Override
    public void remove(Component component) {
        throw new UnsupportedOperationException();
    }
    
    @Override
    public Component getChild(int index) {
        throw new UnsupportedOperationException();
    }
}

// Composite class
class Composite implements Component {
    private List<Component> children = new ArrayList<>();
    
    @Override
    public void operation() {
        for (Component child : children) {
            child.operation();
        }
    }
    
    @Override
    public void add(Component component) {
        children.add(component);
    }
    
    @Override
    public void remove(Component component) {
        children.remove(component);
    }
    
    @Override
    public Component getChild(int index) {
        return children.get(index);
    }
}
```

### Use Cases
- File system structures
- GUI component hierarchies
- Organization structures
- Document object models
- Menu systems

## Decorator Pattern

### Intent
Attach additional responsibilities to an object dynamically. Decorators provide a flexible alternative to subclassing for extending functionality.

### Structure
```java
// Component interface
interface Coffee {
    double cost();
    String description();
}

// Concrete component
class SimpleCoffee implements Coffee {
    @Override
    public double cost() {
        return 1.0;
    }
    
    @Override
    public String description() {
        return "Simple coffee";
    }
}

// Decorator base class
abstract class CoffeeDecorator implements Coffee {
    protected Coffee decoratedCoffee;
    
    public CoffeeDecorator(Coffee coffee) {
        this.decoratedCoffee = coffee;
    }
    
    @Override
    public double cost() {
        return decoratedCoffee.cost();
    }
    
    @Override
    public String description() {
        return decoratedCoffee.description();
    }
}

// Concrete decorators
class MilkDecorator extends CoffeeDecorator {
    public MilkDecorator(Coffee coffee) {
        super(coffee);
    }
    
    @Override
    public double cost() {
        return super.cost() + 0.5;
    }
    
    @Override
    public String description() {
        return super.description() + ", milk";
    }
}
```

### Use Cases
- UI component enhancement
- Stream operations
- Middleware functionality
- Dynamic behavior addition
- Feature configuration

## Facade Pattern

### Intent
Provide a unified interface to a set of interfaces in a subsystem. Facade defines a higher-level interface that makes the subsystem easier to use.

### Structure
```java
// Complex subsystem classes
class CPU {
    public void freeze() { }
    public void jump(long position) { }
    public void execute() { }
}

class Memory {
    public void load(long position, byte[] data) { }
}

class HardDrive {
    public byte[] read(long lba, int size) { }
}

// Facade
class ComputerFacade {
    private CPU cpu;
    private Memory memory;
    private HardDrive hardDrive;
    
    public ComputerFacade() {
        this.cpu = new CPU();
        this.memory = new Memory();
        this.hardDrive = new HardDrive();
    }
    
    public void start() {
        cpu.freeze();
        memory.load(0, hardDrive.read(0, 1024));
        cpu.jump(0);
        cpu.execute();
    }
}
```

### Use Cases
- System simplification
- Library wrapping
- Complex system access
- API unification
- Legacy system modernization

## Proxy Pattern

### Intent
Provide a surrogate or placeholder for another object to control access to it.

### Structure
```java
// Subject interface
interface Image {
    void display();
}

// Real subject
class RealImage implements Image {
    private String filename;
    
    public RealImage(String filename) {
        this.filename = filename;
        loadFromDisk();
    }
    
    private void loadFromDisk() {
        System.out.println("Loading " + filename);
    }
    
    @Override
    public void display() {
        System.out.println("Displaying " + filename);
    }
}

// Proxy
class ProxyImage implements Image {
    private RealImage realImage;
    private String filename;
    
    public ProxyImage(String filename) {
        this.filename = filename;
    }
    
    @Override
    public void display() {
        if (realImage == null) {
            realImage = new RealImage(filename);
        }
        realImage.display();
    }
}
```

### Use Cases
- Lazy loading
- Access control
- Logging/monitoring
- Caching
- Remote resource access

## Best Practices

### Pattern Selection
1. **Considerations**
   - System requirements
   - Performance needs
   - Maintainability goals
   - Flexibility requirements
   - Team expertise

2. **Implementation Guidelines**
   - Clear interfaces
   - Proper abstraction
   - Error handling
   - Documentation
   - Testing strategy

### Common Pitfalls
1. **Anti-patterns**
   - Over-engineering
   - Unnecessary complexity
   - Wrong pattern choice
   - Poor abstraction
   - Performance issues

2. **Solutions**
   - Pattern simplification
   - Clear documentation
   - Performance testing
   - Code review
   - Continuous refactoring

## Conclusion
Structural patterns provide essential solutions for organizing code into larger structures while maintaining flexibility and efficiency. Choose patterns based on specific needs while considering maintainability and complexity.
