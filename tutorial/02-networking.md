# 02 -- Networking in Go

In distributed systems, independent processes communicate over the
network.

In this project, the Queue, Workers, and Client will communicate using
TCP sockets and JSON messages.

This tutorial explains:

-   How TCP communication works
-   How to build a client
-   How to send structured messages
-   How request--response patterns work

------------------------------------------------------------------------

## 1) How TCP Communication Works

TCP is a connection-oriented protocol.

Basic flow:

1.  Server listens on a port
2.  Client connects to the server
3.  Client sends data
4.  Server reads data
5.  Server responds
6.  Connection closes

In Go, this is done using the `net` package.

------------------------------------------------------------------------

## 2) Simple Request--Response Example

We will build:

-   A server that reads a message
-   A client that sends a message
-   The server replies

------------------------------------------------------------------------

### Server Code (server.go)

``` go
package main

import (
    "bufio"
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

    reader := bufio.NewReader(conn)
    message, err := reader.ReadString('\n')
    if err != nil {
        return
    }

    fmt.Println("Received:", message)

    conn.Write([]byte("Message received\n"))
}
```

------------------------------------------------------------------------

### Client Code (client.go)

``` go
package main

import (
    "fmt"
    "net"
)

func main() {
    conn, err := net.Dial("tcp", "localhost:8080")
    if err != nil {
        panic(err)
    }
    defer conn.Close()

    message := "Hello from client\n"
    conn.Write([]byte(message))

    buffer := make([]byte, 1024)
    n, _ := conn.Read(buffer)

    fmt.Println("Server response:", string(buffer[:n]))
}
```

------------------------------------------------------------------------

### How to Run (All OS)

Terminal 1:

    go run server.go

Terminal 2:

    go run client.go

Expected output:

Server terminal:

    Received: Hello from client

Client terminal:

    Server response: Message received

------------------------------------------------------------------------

## 3) Sending Structured Data (JSON over TCP)

In real systems, we send structured data instead of raw strings.

Example JSON message:

{ "type": "submit_job", "id": "job1", "duration": 5 }

------------------------------------------------------------------------

### JSON Server Example

``` go
package main

import (
    "encoding/json"
    "fmt"
    "net"
)

type Job struct {
    ID       string `json:"id"`
    Duration int    `json:"duration"`
}

func main() {
    listener, _ := net.Listen("tcp", ":8081")
    defer listener.Close()

    fmt.Println("JSON Server listening on port 8081")

    for {
        conn, _ := listener.Accept()
        go handle(conn)
    }
}

func handle(conn net.Conn) {
    defer conn.Close()

    var job Job
    decoder := json.NewDecoder(conn)
    err := decoder.Decode(&job)
    if err != nil {
        return
    }

    fmt.Println("Received job:", job)

    response := map[string]string{"status": "accepted"}
    json.NewEncoder(conn).Encode(response)
}
```

------------------------------------------------------------------------

### JSON Client Example

``` go
package main

import (
    "encoding/json"
    "fmt"
    "net"
)

type Job struct {
    ID       string `json:"id"`
    Duration int    `json:"duration"`
}

func main() {
    conn, _ := net.Dial("tcp", "localhost:8081")
    defer conn.Close()

    job := Job{ID: "job1", Duration: 5}
    json.NewEncoder(conn).Encode(job)

    var response map[string]string
    json.NewDecoder(conn).Decode(&response)

    fmt.Println("Server response:", response)
}
```

------------------------------------------------------------------------

## 4) Important Concepts for the Project

From this tutorial, you must understand:

-   Listening on a TCP port
-   Accepting connections
-   Reading data from a connection
-   Writing data to a connection
-   Using goroutines for concurrency
-   Encoding and decoding JSON over TCP

------------------------------------------------------------------------

## 5) Common Mistakes

-   Forgetting newline when using ReadString('`\n`{=tex}')
-   Not closing connections (`defer conn.Close()`)
-   Client blocking forever if server does not respond

------------------------------------------------------------------------

## 6) How This Connects to Project 2

In Project 2:

-   The Queue is a server.
-   Workers connect to the Queue.
-   The Client submits jobs to the Queue.

All communication will use the TCP + JSON structure shown in this
tutorial.
