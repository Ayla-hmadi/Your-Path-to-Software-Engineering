# Behavioral Design Patterns

## Introduction
Behavioral patterns are concerned with communication between objects, how objects interact, and distribute responsibilities. These patterns focus on object collaboration and the delegation of responsibilities.

## Observer Pattern

### Intent
Define a one-to-many dependency between objects so that when one object changes state, all its dependents are notified and updated automatically.

### Structure
```java
// Subject interface
interface Subject {
    void attach(Observer observer);
    void detach(Observer observer);
    void notifyObservers();
}

// Observer interface
interface Observer {
    void update(String message);
}

// Concrete Subject
class NewsAgency implements Subject {
    private List<Observer> observers = new ArrayList<>();
    private String news;
    
    @Override
    public void attach(Observer observer) {
        observers.add(observer);
    }
    
    @Override
    public void detach(Observer observer) {
        observers.remove(observer);
    }
    
    @Override
    public void notifyObservers() {
        for (Observer observer : observers) {
            observer.update(news);
        }
    }
    
    public void setNews(String news) {
        this.news = news;
        notifyObservers();
    }
}

// Concrete Observer
class NewsChannel implements Observer {
    private String name;
    
    public NewsChannel(String name) {
        this.name = name;
    }
    
    @Override
    public void update(String news) {
        System.out.println(name + " received news: " + news);
    }
}
```

### Use Cases
- Event handling systems
- User interface updates
- Real-time data monitoring
- Publish-subscribe systems
- Distributed systems

## Strategy Pattern

### Intent
Define a family of algorithms, encapsulate each one, and make them interchangeable. Strategy lets the algorithm vary independently from clients that use it.

### Structure
```java
// Strategy interface
interface PaymentStrategy {
    void pay(int amount);
}

// Concrete strategies
class CreditCardPayment implements PaymentStrategy {
    private String cardNumber;
    
    public CreditCardPayment(String cardNumber) {
        this.cardNumber = cardNumber;
    }
    
    @Override
    public void pay(int amount) {
        System.out.println("Paid " + amount + " using credit card " + cardNumber);
    }
}

class PayPalPayment implements PaymentStrategy {
    private String email;
    
    public PayPalPayment(String email) {
        this.email = email;
    }
    
    @Override
    public void pay(int amount) {
        System.out.println("Paid " + amount + " using PayPal account " + email);
    }
}

// Context
class ShoppingCart {
    private PaymentStrategy paymentStrategy;
    
    public void setPaymentStrategy(PaymentStrategy strategy) {
        this.paymentStrategy = strategy;
    }
    
    public void checkout(int amount) {
        paymentStrategy.pay(amount);
    }
}
```

### Use Cases
- Payment processing
- Sorting algorithms
- File compression
- Authentication methods
- Validation strategies

## Command Pattern

### Intent
Encapsulate a request as an object, thereby letting you parameterize clients with different requests, queue or log requests, and support undoable operations.

### Structure
```java
// Command interface
interface Command {
    void execute();
    void undo();
}

// Receiver
class Light {
    public void turnOn() {
        System.out.println("Light is on");
    }
    
    public void turnOff() {
        System.out.println("Light is off");
    }
}

// Concrete commands
class LightOnCommand implements Command {
    private Light light;
    
    public LightOnCommand(Light light) {
        this.light = light;
    }
    
    @Override
    public void execute() {
        light.turnOn();
    }
    
    @Override
    public void undo() {
        light.turnOff();
    }
}

// Invoker
class RemoteControl {
    private Command command;
    
    public void setCommand(Command command) {
        this.command = command;
    }
    
    public void pressButton() {
        command.execute();
    }
    
    public void pressUndo() {
        command.undo();
    }
}
```

### Use Cases
- GUI actions
- Transaction management
- Task scheduling
- Macro recording
- Undo/redo functionality

## State Pattern

### Intent
Allow an object to alter its behavior when its internal state changes. The object will appear to change its class.

### Structure
```java
// State interface
interface State {
    void handle(Context context);
}

// Context
class Context {
    private State state;
    
    public void setState(State state) {
        this.state = state;
    }
    
    public void request() {
        state.handle(this);
    }
}

// Concrete states
class ConcreteStateA implements State {
    @Override
    public void handle(Context context) {
        System.out.println("Handling state A");
        context.setState(new ConcreteStateB());
    }
}

class ConcreteStateB implements State {
    @Override
    public void handle(Context context) {
        System.out.println("Handling state B");
        context.setState(new ConcreteStateA());
    }
}
```

### Use Cases
- Vending machines
- Order processing
- Game states
- Connection handling
- Document workflow

## Template Method Pattern

### Intent
Define the skeleton of an algorithm in a method, deferring some steps to subclasses. Template Method lets subclasses redefine certain steps of an algorithm without changing the algorithm's structure.

### Structure
```java
// Abstract class with template method
abstract class DataMiner {
    public final void mine() {
        openFile();
        extractData();
        parseData();
        analyzeData();
        sendReport();
        closeFile();
    }
    
    abstract void openFile();
    abstract void extractData();
    abstract void parseData();
    
    void analyzeData() {
        System.out.println("Analyzing data...");
    }
    
    void sendReport() {
        System.out.println("Sending report...");
    }
    
    void closeFile() {
        System.out.println("Closing file...");
    }
}

// Concrete implementation
class PDFDataMiner extends DataMiner {
    @Override
    void openFile() {
        System.out.println("Opening PDF file...");
    }
    
    @Override
    void extractData() {
        System.out.println("Extracting PDF data...");
    }
    
    @Override
    void parseData() {
        System.out.println("Parsing PDF data...");
    }
}
```

### Use Cases
- Data processing workflows
- Framework operations
- Build processes
- Report generation
- Document conversion

## Best Practices

### Pattern Selection
1. **Decision Factors**
   - Problem complexity
   - System requirements
   - Team expertise
   - Maintenance needs
   - Performance considerations

2. **Implementation Guidelines**
   - Clear interfaces
   - Proper abstraction
   - Error handling
   - Documentation
   - Testing strategy

### Common Pitfalls
1. **Anti-patterns**
   - Over-complication
   - Wrong pattern choice
   - Poor abstraction
   - Tight coupling
   - Unclear responsibilities

2. **Solutions**
   - Pattern simplification
   - Clear documentation
   - Code review
   - Refactoring
   - Performance testing

## Conclusion
Behavioral patterns provide solutions for better interaction between objects and improve the assignment of responsibilities between objects. Choose patterns based on specific needs while considering maintainability and complexity.
