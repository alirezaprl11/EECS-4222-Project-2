# 01 -- Go Basics

Before building a distributed system, you must understand a few Go
fundamentals. This tutorial introduces only the concepts needed for the
Distributed Job Queue project.

Each section includes: - A short explanation - A full runnable example

To run any example: 1. Copy the code into a file named `main.go` 2. Run:
`go run main.go`

------------------------------------------------------------------------

## 1) Hello World

``` go
package main

import "fmt"

func main() {
    fmt.Println("Hello, Distributed Systems!")
}
```

------------------------------------------------------------------------

## 2) Variables and Basic Types

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

## 3) Structs

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
    fmt.Println("Duration:", job.Duration)
}
```

------------------------------------------------------------------------

## 4) Functions and Error Handling

``` go
package main

import "fmt"

func divide(a int, b int) (int, error) {
    if b == 0 {
        return 0, fmt.Errorf("division by zero")
    }
    return a / b, nil
}

func main() {
    r, err := divide(10, 2)
    fmt.Println("Result:", r, "Error:", err)

    r, err = divide(10, 0)
    fmt.Println("Result:", r, "Error:", err)
}
```

------------------------------------------------------------------------

## 5) Slices

``` go
package main

import "fmt"

func main() {
    jobs := []string{}
    jobs = append(jobs, "job1")
    jobs = append(jobs, "job2")

    for i, job := range jobs {
        fmt.Println(i, job)
    }
}
```

------------------------------------------------------------------------

## 6) Maps

``` go
package main

import "fmt"

func main() {
    status := make(map[string]string)
    status["job1"] = "pending"
    status["job2"] = "running"

    fmt.Println("job1:", status["job1"])

    value, exists := status["job3"]
    fmt.Println("job3 exists?", exists, "value:", value)
}
```

------------------------------------------------------------------------

## 7) Goroutines

``` go
package main

import (
    "fmt"
    "time"
)

func main() {
    go func() {
        fmt.Println("Worker started")
        time.Sleep(1 * time.Second)
        fmt.Println("Worker finished")
    }()

    fmt.Println("Main continues...")
    time.Sleep(2 * time.Second)
}
```

------------------------------------------------------------------------

## 8) Mutex

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
    fmt.Println("Final counter:", counter)
}
```

------------------------------------------------------------------------

## 9) Basic TCP Server (Windows-Safe Testing Instructions)

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

1.  Start the server:

        go run main.go

2.  Open a new PowerShell window and run:

    ``` powershell
    Test-NetConnection -ComputerName localhost -Port 8080
    ```

If you see:

    TcpTestSucceeded : True

the server is running correctly.

(Optional) If you want to use Telnet:

Enable Telnet Client in Windows: Control Panel → Programs → Turn Windows
features on or off → Enable "Telnet Client"

Then run:

    telnet localhost 8080

------------------------------------------------------------------------

## 10) JSON Encoding and Decoding

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
    job := Job{ID: "job1", Duration: 5}

    data, _ := json.Marshal(job)
    fmt.Println("Encoded:", string(data))

    var decoded Job
    json.Unmarshal(data, &decoded)
    fmt.Println("Decoded:", decoded)
}
```

------------------------------------------------------------------------

## What You Should Understand Before Moving On

-   Running Go programs
-   Structs
-   Slices and maps
-   Goroutines
-   Mutex
-   Basic TCP networking
-   JSON encoding/decoding

You do NOT need advanced Go features for this project.
