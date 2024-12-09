# C++ Programming Language Overview

## Introduction
C++ is a general-purpose programming language created as an extension of the C language, adding object-oriented features, templates, and extensive standard library support. It provides high-level abstractions while maintaining low-level hardware control.

## Core Features

### Object-Oriented Programming
1. **Classes and Objects**
   ```cpp
   class Person {
   private:
       std::string name;
       int age;
       
   public:
       // Constructor
       Person(std::string n, int a) : name(n), age(a) {}
       
       // Member functions
       void introduce() const {
           std::cout << "I'm " << name << ", " << age << " years old\n";
       }
       
       // Getter/setter methods
       std::string getName() const { return name; }
       void setName(const std::string& n) { name = n; }
   };
   ```

2. **Inheritance and Polymorphism**
   ```cpp
   class Animal {
   public:
       virtual void makeSound() const = 0; // Pure virtual function
       virtual ~Animal() = default;        // Virtual destructor
   };
   
   class Dog : public Animal {
   public:
       void makeSound() const override {
           std::cout << "Woof!\n";
       }
   };
   ```

### Memory Management
1. **Manual Memory Control**
   ```cpp
   class ResourceManager {
   private:
       int* data;
       
   public:
       // Constructor
       ResourceManager() : data(new int[100]) {}
       
       // Destructor
       ~ResourceManager() {
           delete[] data;
       }
       
       // Copy constructor
       ResourceManager(const ResourceManager& other) {
           data = new int[100];
           std::copy(other.data, other.data + 100, data);
       }
       
       // Move constructor
       ResourceManager(ResourceManager&& other) noexcept {
           data = other.data;
           other.data = nullptr;
       }
   };
   ```

2. **Smart Pointers**
   ```cpp
   #include <memory>
   
   // Unique pointer
   std::unique_ptr<Person> person = 
       std::make_unique<Person>("John", 30);
   
   // Shared pointer
   std::shared_ptr<Resource> resource = 
       std::make_shared<Resource>();
   
   // Weak pointer
   std::weak_ptr<Resource> weakRef = resource;
   ```

### Templates
1. **Generic Programming**
   ```cpp
   template<typename T>
   class Container {
   private:
       T data;
       
   public:
       Container(T value) : data(value) {}
       T getValue() const { return data; }
       void setValue(T value) { data = value; }
   };
   
   // Function template
   template<typename T>
   T max(T a, T b) {
       return (a > b) ? a : b;
   }
   ```

2. **Template Specialization**
   ```cpp
   template<>
   class Container<bool> {
   private:
       bool data;
       
   public:
       Container(bool value) : data(value) {}
       bool getValue() const { return data; }
       void toggle() { data = !data; }
   };
   ```

## Modern C++ Features

### Lambda Expressions
1. **Basic Lambda**
   ```cpp
   auto add = [](int a, int b) { return a + b; };
   
   std::vector<int> numbers = {1, 2, 3, 4, 5};
   std::for_each(numbers.begin(), numbers.end(),
       [](int& n) { n *= 2; });
   ```

2. **Capture Clauses**
   ```cpp
   int multiplier = 10;
   auto multiply = [multiplier](int x) { return x * multiplier; };
   
   // Mutable lambda
   auto counter = [count = 0]() mutable { return ++count; };
   ```

### Range-based For Loop
```cpp
std::vector<int> numbers = {1, 2, 3, 4, 5};

// Range-based for
for (const auto& num : numbers) {
    std::cout << num << " ";
}

// Structured binding
std::map<std::string, int> scores;
for (const auto& [name, score] : scores) {
    std::cout << name << ": " << score << "\n";
}
```

## Standard Template Library (STL)

### Containers
1. **Sequence Containers**
   ```cpp
   std::vector<int> vec = {1, 2, 3};
   std::list<std::string> list = {"a", "b", "c"};
   std::deque<double> deq;
   
   // Vector operations
   vec.push_back(4);
   vec.emplace_back(5);
   ```

2. **Associative Containers**
   ```cpp
   std::map<std::string, int> scores;
   scores["Alice"] = 100;
   scores["Bob"] = 95;
   
   std::set<int> uniqueNums;
   uniqueNums.insert(42);
   ```

### Algorithms
```cpp
std::vector<int> numbers = {3, 1, 4, 1, 5, 9};

// Sorting
std::sort(numbers.begin(), numbers.end());

// Finding
auto it = std::find(numbers.begin(), numbers.end(), 4);

// Transforming
std::transform(numbers.begin(), numbers.end(),
    numbers.begin(),
    [](int n) { return n * 2; });
```

## Exception Handling
```cpp
class CustomException : public std::exception {
public:
    const char* what() const noexcept override {
        return "Custom error occurred";
    }
};

void riskyFunction() {
    try {
        throw CustomException();
    } catch (const CustomException& e) {
        std::cerr << e.what() << std::endl;
    } catch (...) {
        std::cerr << "Unknown exception" << std::endl;
    }
}
```

## Best Practices

### Resource Management
1. **RAII Pattern**
   ```cpp
   class File {
   private:
       std::FILE* fp;
       
   public:
       File(const char* filename) {
           fp = std::fopen(filename, "r");
           if (!fp) throw std::runtime_error("File open failed");
       }
       
       ~File() {
           if (fp) std::fclose(fp);
       }
   };
   ```

2. **Move Semantics**
   ```cpp
   class Buffer {
   private:
       std::unique_ptr<char[]> data;
       size_t size;
       
   public:
       Buffer(Buffer&& other) noexcept
           : data(std::move(other.data))
           , size(other.size) {
           other.size = 0;
       }
   };
   ```

## Conclusion
C++ combines powerful abstraction capabilities with low-level control, making it suitable for system programming, game development, and performance-critical applications.
