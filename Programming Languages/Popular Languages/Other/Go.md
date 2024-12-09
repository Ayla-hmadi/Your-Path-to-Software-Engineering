# Go Programming Language

## Introduction
Go (or Golang) is a statically typed, compiled programming language designed by Google. It combines simplicity, efficiency, and built-in support for concurrent programming through goroutines and channels.

## Core Features

### Simplicity and Readability
1. **Basic Syntax**
   ```go
   package main

   import "fmt"

   func main() {
       message := "Hello, Go!"
       fmt.Println(message)
       
       // Multiple variable declaration
       var (
           name    string = "Alice"
           age     int    = 30
           isValid bool   = true
       )
   }
   ```

2. **Built-in Types**
   ```go
   // Basic types
   var number int = 42
   var pi float64 = 3.14159
   var text string = "Go"
   var isTrue bool = true

   // Arrays and slices
   var arr [5]int = [5]int{1, 2, 3, 4, 5}
   slice := []int{1, 2, 3}
   slice = append(slice, 4)
   ```

### Concurrency
1. **Goroutines**
   ```go
   func processData(data string, c chan string) {
       // Simulating work
       time.Sleep(100 * time.Millisecond)
       c <- "Processed: " + data
   }

   func main() {
       c := make(chan string)
       go processData("Hello", c)
       result := <-c
       fmt.Println(result)
   }
   ```

2. **Channels**
   ```go
   func worker(id int, jobs <-chan int, results chan<- int) {
       for job := range jobs {
           results <- job * 2
       }
   }

   func main() {
       jobs := make(chan int, 100)
       results := make(chan int, 100)

       // Start workers
       for w := 1; w <= 3; w++ {
           go worker(w, jobs, results)
       }

       // Send jobs
       for j := 1; j <= 5; j++ {
           jobs <- j
       }
       close(jobs)

       // Collect results
       for i := 1; i <= 5; i++ {
           <-results
       }
   }
   ```

### Error Handling
1. **Error Interface**
   ```go
   type error interface {
       Error() string
   }

   // Custom error
   type CustomError struct {
       Code    int
       Message string
   }

   func (e *CustomError) Error() string {
       return fmt.Sprintf("Error %d: %s", e.Code, e.Message)
   }

   func divide(a, b float64) (float64, error) {
       if b == 0 {
           return 0, &CustomError{
               Code:    1,
               Message: "division by zero",
           }
       }
       return a / b, nil
   }
   ```

2. **Error Checking**
   ```go
   func processFile(filename string) error {
       file, err := os.Open(filename)
       if err != nil {
           return fmt.Errorf("failed to open file: %w", err)
       }
       defer file.Close()

       // Process file...
       return nil
   }
   ```

### Interfaces
1. **Interface Definition**
   ```go
   type Reader interface {
       Read(p []byte) (n int, err error)
   }

   type Writer interface {
       Write(p []byte) (n int, err error)
   }

   // Composition
   type ReadWriter interface {
       Reader
       Writer
   }
   ```

2. **Implementation**
   ```go
   type File struct {
       // ...
   }

   func (f *File) Read(p []byte) (n int, err error) {
       // Implementation
       return len(p), nil
   }

   func (f *File) Write(p []byte) (n int, err error) {
       // Implementation
       return len(p), nil
   }
   ```

## Standard Library

### HTTP Server
```go
func handler(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintf(w, "Hello, %s!", r.URL.Path[1:])
}

func main() {
    http.HandleFunc("/", handler)
    log.Fatal(http.ListenAndServe(":8080", nil))
}
```

### JSON Handling
```go
type Person struct {
    Name    string `json:"name"`
    Age     int    `json:"age"`
    Address string `json:"address,omitempty"`
}

func main() {
    person := Person{
        Name: "John",
        Age:  30,
    }

    // Marshal
    jsonData, err := json.Marshal(person)
    if err != nil {
        log.Fatal(err)
    }

    // Unmarshal
    var decoded Person
    err = json.Unmarshal(jsonData, &decoded)
    if err != nil {
        log.Fatal(err)
    }
}
```

## Testing

### Unit Tests
```go
func TestAdd(t *testing.T) {
    tests := []struct {
        name     string
        a, b     int
        expected int
    }{
        {"positive", 2, 3, 5},
        {"negative", -2, -3, -5},
        {"zero", 0, 0, 0},
    }

    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            result := Add(tt.a, tt.b)
            if result != tt.expected {
                t.Errorf("Add(%d, %d) = %d; want %d", 
                    tt.a, tt.b, result, tt.expected)
            }
        })
    }
}
```

### Benchmarks
```go
func BenchmarkFibonacci(b *testing.B) {
    for i := 0; i < b.N; i++ {
        Fibonacci(20)
    }
}
```

## Package Management
```go
// go.mod
module example.com/project

go 1.16

require (
    github.com/gin-gonic/gin v1.7.0
    github.com/go-sql-driver/mysql v1.6.0
)
```

## Best Practices

### Error Handling
```go
func process() error {
    // Wrap errors with context
    if err := doSomething(); err != nil {
        return fmt.Errorf("process failed: %w", err)
    }
    return nil
}

// Use defer for cleanup
func processFile(filename string) error {
    file, err := os.Open(filename)
    if err != nil {
        return err
    }
    defer file.Close()
    
    // Process file...
    return nil
}
```

## Conclusion
Go's simplicity, efficient concurrency model, and strong standard library make it an excellent choice for building scalable network services and cloud applications.
