# 01 - Go Basics

Before building a distributed system, you must understand a few Go
fundamentals. This tutorial introduces only the concepts needed for the
**Distributed Job Queue** project.

To run any example: 
1. Copy the code into a file named `main.go`
2. Run: `go run main.go`

------------------------------------------------------------------------

## 1️⃣ Hello World in Go

### What this shows

-   `package main` is required for executable programs
-   `func main()` is the entry point
-   `fmt.Println()` prints text to the terminal

### ✅ Example

``` go
package main

import "fmt"

func main() {
    fmt.Println("Hello, Distributed Systems!")
}
```

------------------------------------------------------------------------

## 2️⃣ Variables and Basic Types

Go is statically typed, but it supports concise declarations.

### What this shows

-   `var x type = value` (explicit declaration)
-   `x := value` (short declaration, commonly used)
-   Basic types like `string`, `int`, `bool`, `float64`

### ✅ Example

``` go
package main

import "fmt"

func main() {
    var name string = "Alice"
    age := 25
    isWorker := true
    pi := 3.14159

    fmt.Println("name:", name)
    fmt.Println("age:", age)
    fmt.Println("isWorker:", isWorker)
    fmt.Println("pi:", pi)
}
```

------------------------------------------------------------------------

## 3️⃣ Structs (Used for Jobs and Messages)

In this project, you will represent **jobs** and **network messages**
using structs.

### What this shows

-   Defining a struct type
-   Creating a struct instance
-   Accessing fields with dot notation (`job.ID`)

### ✅ Example

``` go
package main

import "fmt"

type Job struct {
    ID       string
    Duration int
}

func main() {
    job := Job{
        ID:       "job1",
        Duration: 5,
    }

    fmt.Println("Job:", job)
    fmt.Println("Job ID:", job.ID)
    fmt.Println("Duration:", job.Duration, "seconds")
}
```

------------------------------------------------------------------------

## 4️⃣ Functions and Error Handling

Go functions can return **multiple values**. A common pattern is
returning `(result, error)`.

### What this shows

-   Defining functions
-   Returning errors using `fmt.Errorf`
-   Checking and printing errors

### ✅ Example

``` go
package main

import "fmt"

func add(a int, b int) int {
    return a + b
}

func divide(a int, b int) (int, error) {
    if b == 0 {
        return 0, fmt.Errorf("division by zero")
    }
    return a / b, nil
}

func main() {
    fmt.Println("add(2, 3) =", add(2, 3))

    r, err := divide(10, 2)
    fmt.Println("divide(10, 2) =", r, "err =", err)

    r, err = divide(10, 0)
    fmt.Println("divide(10, 0) =", r, "err =", err)
}
```

------------------------------------------------------------------------

## 5️⃣ Slices (Dynamic Arrays)

Slices are Go's main "list" type. They grow dynamically.

### What this shows

-   Creating a slice
-   Adding items using `append`
-   Iterating using `for ... range`

### ✅ Example

``` go
package main

import "fmt"

type Job struct {
    ID       string
    Duration int
}

func main() {
    jobs := []Job{}

    jobs = append(jobs, Job{ID: "job1", Duration: 3})
    jobs = append(jobs, Job{ID: "job2", Duration: 5})
    jobs = append(jobs, Job{ID: "job3", Duration: 2})

    fmt.Println("Jobs:")
    for i, job := range jobs {
        fmt.Println(i, "->", job)
    }
}
```

------------------------------------------------------------------------

## 6️⃣ Maps (Key → Value)

Maps store key-value pairs. We will use maps to track job status, worker
state, etc.

### What this shows

-   Creating a map using `make`
-   Setting and reading values
-   Checking whether a key exists

### ✅ Example

``` go
package main

import "fmt"

func main() {
    jobStatus := make(map[string]string)

    jobStatus["job1"] = "pending"
    jobStatus["job2"] = "running"

    fmt.Println("job1 status:", jobStatus["job1"])

    // Check if a key exists
    status, ok := jobStatus["job3"]
    fmt.Println("job3 exists?", ok, "value:", status)
}
```

------------------------------------------------------------------------

## 7️⃣ Concurrency -- Goroutines

A goroutine is a lightweight thread managed by Go. We use goroutines to
handle multiple connections at once.

### What this shows

-   Starting a goroutine using `go`
-   The main function continues running while the goroutine runs in the
    background

### ✅ Example

``` go
package main

import (
    "fmt"
    "time"
)

func main() {
    go func() {
        fmt.Println("Goroutine: worker started")
        time.Sleep(1 * time.Second)
        fmt.Println("Goroutine: worker finished")
    }()

    fmt.Println("Main: continuing execution")
    time.Sleep(2 * time.Second)
    fmt.Println("Main: done")
}
```

------------------------------------------------------------------------

## 8️⃣ Mutex (Prevent Race Conditions)

When multiple goroutines access shared data, you must protect it (for
example, with `sync.Mutex`).

### What this shows

-   Multiple goroutines increment a shared counter
-   A mutex prevents incorrect updates

### ✅ Example

``` go
package main

import (
    "fmt"
    "sync"
)

func main() {
    var mu sync.Mutex
    counter := 0

    var wg sync.WaitGroup

    for i := 0; i < 5; i++ {
        wg.Add(1)
        go func() {
            defer wg.Done()
            for j := 0; j < 1000; j++ {
                mu.Lock()
                counter++
                mu.Unlock()
            }
        }()
    }

    wg.Wait()
    fmt.Println("Final counter value:", counter)
}
```

------------------------------------------------------------------------

## 9️⃣ Basic TCP Server (With Windows-Safe Testing)

This is the basic pattern we will use later: a server listens, accepts
connections, and handles each connection in a goroutine.

### What this shows

-   Listening on a TCP port
-   Accepting connections
-   Handling each connection concurrently

### ✅ Example

``` go
package main

import (
    "fmt"
    "net"
)

func main() {
    listener, err := net.Listen("tcp", ":8080")
    if err != nil {
        panic(err)
    }
    defer listener.Close()

    fmt.Println("Server listening on port 8080")

    for {
        conn, err := listener.Accept()
        if err != nil {
            continue
        }
        go handleConnection(conn)
    }
}

func handleConnection(conn net.Conn) {
    defer conn.Close()
    conn.Write([]byte("Hello from server\n"))
}
```

### How to Test on Windows

#### Step 1 -- Start the server

In Terminal 1:
- `go run main.go`
- You should see: `Server listening on port 8080`

#### Step 2 -- Test the connection

Open a new PowerShell window and run:
- `Test-NetConnection -ComputerName localhost -Port 8080`
- If you see: `TcpTestSucceeded : True` → the server is running correctly.

#### Optional: Use Telnet to see the message

Enable Telnet Client: Control Panel → Programs → Turn Windows features
on or off → Enable "Telnet Client"

Then run:
- `telnet localhost 8080`
- You should see: `Hello from server`

### How to Test on Linux

#### Step 1 -- Start the server

- `go run main.go`

#### Step 2 -- Connect using netcat (recommended)

- `nc localhost 8080`
- If netcat is not installed → `sudo apt install netcat`
- You should see: Hello from server

### Alternative -- Using Telnet

- `telnet localhost 8080`
- If telnet is not installed: `sudo apt install telnet`


### How to Test on macOS

#### Step 1 -- Start the server

- `go run main.go`

#### Step 2 -- Connect using netcat (recommended)

- `nc localhost 8080`
- Netcat is typically preinstalled on macOS.
- You should see: `Hello from server`

#### Alternative -- Using Telnet (if installed)

- `telnet localhost 8080`
- If telnet is missing, install it using Homebrew: `brew install telnet`

### What Is Happening Internally?

-   The server listens on TCP port 8080.
-   A client (nc, telnet, or PowerShell test) opens a TCP connection.
-   The server accepts the connection.
-   The server writes "Hello from server".
-   The connection closes.

------------------------------------------------------------------------
## 🔟 JSON Encoding and Decoding

We will send jobs and responses as JSON messages.

### What this shows

-   Encoding a struct to JSON with `json.Marshal`
-   Decoding JSON back into a struct with `json.Unmarshal`

### ✅ Example

``` go
package main

import (
    "encoding/json"
    "fmt"
)

type Job struct {
    ID       string `json:"id"`
    Duration int    `json:"duration"`
}

func main() {
    // Encode
    job := Job{ID: "job1", Duration: 5}
    data, err := json.Marshal(job)
    if err != nil {
        panic(err)
    }
    fmt.Println("Encoded JSON:", string(data))

    // Decode
    var decoded Job
    err = json.Unmarshal(data, &decoded)
    if err != nil {
        panic(err)
    }
    fmt.Println("Decoded struct:", decoded)
}
```

------------------------------------------------------------------------

## ✅ What You Must Understand Before Moving On

Before moving to the next tutorial, make sure you can:

-   Run Go programs
-   Define and use structs
-   Use slices and maps
-   Start goroutines
-   Use a mutex to protect shared data
-   Understand the basic TCP server structure
-   Encode/decode JSON
