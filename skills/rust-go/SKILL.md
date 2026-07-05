# Rust & Go Skill

## Purpose
Expert systems programming with Rust and Go. High-performance, concurrent, and memory-safe applications for cloud-native, systems, and infrastructure.

## Rust

### Ownership & Borrowing
```rust
fn main() {
    let s1 = String::from("hello");
    let s2 = s1.clone(); // Deep copy
    
    let s3 = &s2; // Borrow
    println!("{}", s3);
    
    // Mutable reference
    let mut s4 = String::from("hello");
    s4.push_str(" world");
    println!("{}", s4);
}
```

### Structs & Enums
```rust
#[derive(Debug, Clone)]
struct User {
    id: u64,
    name: String,
    email: String,
    role: Role,
}

#[derive(Debug, Clone, PartialEq)]
enum Role {
    Admin,
    User,
    Guest,
}

impl User {
    fn new(id: u64, name: &str, email: &str) -> Self {
        Self {
            id,
            name: name.to_string(),
            email: email.to_string(),
            role: Role::User,
        }
    }
    
    fn is_admin(&self) -> bool {
        self.role == Role::Admin
    }
}
```

### Error Handling
```rust
use std::fs;
use std::io;

#[derive(Debug)]
enum AppError {
    Io(io::Error),
    Parse(std::num::ParseIntError),
    Custom(String),
}

impl From<io::Error> for AppError {
    fn from(err: io::Error) -> Self {
        AppError::Io(err)
    }
}

fn read_config(path: &str) -> Result<i32, AppError> {
    let content = fs::read_to_string(path)?;
    let value: i32 = content.trim().parse()?;
    Ok(value)
}
```

### Async Rust (Tokio)
```rust
use tokio;
use reqwest;

#[tokio::main]
async fn main() -> Result<(), reqwest::Error> {
    let response = reqwest::get("https://api.example.com/data")
        .await?
        .json::<MyData>()
        .await?;
    
    println!("{:?}", response);
    Ok(())
}

async fn fetch_all(urls: Vec<String>) -> Vec<Result<String, reqwest::Error>> {
    let mut handles = vec![];
    for url in urls {
        handles.push(tokio::spawn(async move {
            reqwest::get(&url).await?.text().await
        }));
    }
    
    let mut results = vec![];
    for handle in handles {
        results.push(handle.await.unwrap());
    }
    results
}
```

## Go

### Structs & Interfaces
```go
package main

import "fmt"

type User struct {
    ID    int64
    Name  string
    Email string
    Role  string
}

type UserRepository interface {
    FindByID(id int64) (*User, error)
    Save(user *User) error
}

type PostgresUserRepo struct {
    db *sql.DB
}

func (r *PostgresUserRepo) FindByID(id int64) (*User, error) {
    user := &User{}
    err := r.db.QueryRow(
        "SELECT id, name, email, role FROM users WHERE id = $1",
        id,
    ).Scan(&user.ID, &user.Name, &user.Email, &user.Role)
    return user, err
}
```

### Concurrency (Goroutines & Channels)
```go
package main

import (
    "fmt"
    "sync"
)

func worker(id int, jobs <-chan int, results chan<- int, wg *sync.WaitGroup) {
    defer wg.Done()
    for job := range jobs {
        fmt.Printf("Worker %d processing job %d\n", id, job)
        results <- job * 2
    }
}

func main() {
    jobs := make(chan int, 100)
    results := make(chan int, 100)
    var wg sync.WaitGroup

    // Start workers
    for w := 1; w <= 3; w++ {
        wg.Add(1)
        go worker(w, jobs, results, &wg)
    }

    // Send jobs
    for j := 0; j < 9; j++ {
        jobs <- j
    }
    close(jobs)

    // Wait and collect results
    go func() {
        wg.Wait()
        close(results)
    }()

    for result := range results {
        fmt.Println("Result:", result)
    }
}
```

### HTTP Server
```go
package main

import (
    "encoding/json"
    "log"
    "net/http"
    "github.com/gorilla/mux"
)

type Response struct {
    Message string `json:"message"`
    Status  int    `json:"status"`
}

func healthHandler(w http.ResponseWriter, r *http.Request) {
    response := Response{Message: "OK", Status: 200}
    w.Header().Set("Content-Type", "application/json")
    json.NewEncoder(w).Encode(response)
}

func main() {
    router := mux.NewRouter()
    router.HandleFunc("/health", healthHandler).Methods("GET")
    
    log.Println("Server starting on :8080")
    log.Fatal(http.ListenAndServe(":8080", router))
}
```

## When to Use What

| Use Case | Rust | Go |
|----------|------|-----|
| Systems programming | ✓ | ✗ |
| Web servers | ✓ | ✓ |
| CLI tools | ✓ | ✓ |
| Microservices | ✓ | ✓ |
| Embedded/IoT | ✓ | ✗ |
| Cloud-native | ✓ | ✓ |
| Performance-critical | ✓ | ✗ |
| Quick prototyping | ✗ | ✓ |

## Best Practices
- Rust: Use `clippy` for linting, `rustfmt` for formatting
- Go: Use `golint`, `go vet`, `gofmt`
- Both: Write tests, use benchmarks
- Both: Prefer composition over inheritance
