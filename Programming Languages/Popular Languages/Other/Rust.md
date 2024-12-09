# Rust Programming Language

## Introduction
Rust is a systems programming language that combines performance with memory safety through its unique ownership system. It's designed to provide memory safety without garbage collection and concurrency without data races.

## Core Features

### Ownership System
1. **Basic Rules**
   ```rust
   fn main() {
       // Each value has exactly one owner
       let s1 = String::from("hello");
       let s2 = s1;  // s1 is moved to s2
       // println!("{}", s1);  // Would not compile - s1 was moved
       
       // References and borrowing
       let s3 = String::from("world");
       let len = calculate_length(&s3);  // Borrowing s3
   }
   
   fn calculate_length(s: &String) -> usize {
       s.len()
   }
   ```

2. **Borrowing Rules**
   ```rust
   fn main() {
       let mut s = String::from("hello");
       
       // Only one mutable reference at a time
       let r1 = &mut s;
       // let r2 = &mut s;  // Would not compile
       
       // Can't have mutable and immutable references simultaneously
       let r3 = &s;
       let r4 = &s;
       // let r5 = &mut s;  // Would not compile
   }
   ```

### Type System
1. **Enums and Pattern Matching**
   ```rust
   enum Result<T, E> {
       Ok(T),
       Err(E),
   }
   
   fn divide(x: f64, y: f64) -> Result<f64, String> {
       if y == 0.0 {
           Err(String::from("Division by zero"))
       } else {
           Ok(x / y)
       }
   }
   
   match divide(10.0, 2.0) {
       Ok(result) => println!("Result: {}", result),
       Err(e) => println!("Error: {}", e),
   }
   ```

2. **Traits**
   ```rust
   trait Summary {
       fn summarize(&self) -> String;
       
       fn default_summary(&self) -> String {
           String::from("Read more...")
       }
   }
   
   struct Article {
       title: String,
       content: String,
   }
   
   impl Summary for Article {
       fn summarize(&self) -> String {
           format!("{}: {}", self.title, &self.content[..50])
       }
   }
   ```

### Memory Safety
1. **Safe Concurrency**
   ```rust
   use std::thread;
   use std::sync::mpsc;
   
   fn main() {
       let (tx, rx) = mpsc::channel();
       
       thread::spawn(move || {
           tx.send(String::from("Hello from thread")).unwrap();
       });
       
       let received = rx.recv().unwrap();
       println!("{}", received);
   }
   ```

2. **Option Type**
   ```rust
   fn find_user(id: u32) -> Option<String> {
       if id == 1 {
           Some(String::from("Alice"))
       } else {
           None
       }
   }
   
   match find_user(1) {
       Some(name) => println!("Found user: {}", name),
       None => println!("User not found"),
   }
   ```

## Modern Features

### Async/Await
```rust
use tokio;

#[tokio::main]
async fn main() {
    let result = fetch_data().await;
    println!("Result: {:?}", result);
}

async fn fetch_data() -> Result<String, Box<dyn std::error::Error>> {
    // Simulated async operation
    tokio::time::sleep(std::time::Duration::from_secs(1)).await;
    Ok(String::from("Data fetched"))
}
```

### Smart Pointers
```rust
use std::rc::Rc;
use std::cell::RefCell;

struct Node {
    next: Option<Rc<RefCell<Node>>>,
    value: i32,
}

impl Node {
    fn new(value: i32) -> Self {
        Node {
            next: None,
            value,
        }
    }
}
```

## Package Management

### Cargo
1. **Project Structure**
   ```toml
   # Cargo.toml
   [package]
   name = "my_project"
   version = "0.1.0"
   edition = "2021"
   
   [dependencies]
   serde = { version = "1.0", features = ["derive"] }
   tokio = { version = "1.0", features = ["full"] }
   ```

2. **Build Commands**
   ```bash
   # Create new project
   cargo new my_project
   
   # Build and run
   cargo build
   cargo run
   
   # Run tests
   cargo test
   ```

## Error Handling
```rust
#[derive(Debug)]
struct CustomError(String);

fn process_data(input: i32) -> Result<i32, CustomError> {
    if input < 0 {
        return Err(CustomError(String::from("Negative input")));
    }
    Ok(input * 2)
}

// Using ? operator
fn chain_operations(input: i32) -> Result<i32, CustomError> {
    let result = process_data(input)?;
    Ok(result + 1)
}
```

## Testing
```rust
#[cfg(test)]
mod tests {
    use super::*;
    
    #[test]
    fn test_addition() {
        assert_eq!(2 + 2, 4);
    }
    
    #[test]
    #[should_panic(expected = "panic message")]
    fn test_panic() {
        panic!("panic message");
    }
}
```

## Conclusion
Rust's focus on safety, performance, and concurrency makes it an excellent choice for systems programming, while its modern features and strong ecosystem support a wide range of applications.
