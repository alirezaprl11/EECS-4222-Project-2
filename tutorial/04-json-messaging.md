# 04 -- JSON Messaging Protocol

In distributed systems, components must agree on a **message format**.

In Project 2, all communication between:

-   Client ↔ Queue\
-   Worker ↔ Queue

will use **JSON over TCP**.

This document defines:

-   Why we use JSON\
-   Message structure\
-   Required message types\
-   Encoding and decoding in Go

------------------------------------------------------------------------

## 1️⃣ Why JSON?

We use JSON because:

-   It is human-readable\
-   It is language-independent\
-   It is easy to debug\
-   It is supported natively in Go (`encoding/json`)

Example JSON message:

``` json
{
  "type": "submit_job",
  "job_id": "job1",
  "duration": 5
}
```

------------------------------------------------------------------------

## 2️⃣ Designing a Message Structure

All messages in this project must follow a structured format.

We define a general message wrapper:

``` go
type Message struct {
    Type string      `json:"type"`
    Data interface{} `json:"data"`
}
```

However, in practice, it is often cleaner to define specific message
structs.

------------------------------------------------------------------------

## 3️⃣ Client → Queue: Submit Job

When a client submits a job, it must send:

``` json
{
  "type": "submit_job",
  "job_id": "job1",
  "duration": 5
}
```

Go struct:

``` go
type SubmitJobRequest struct {
    Type     string `json:"type"`
    JobID    string `json:"job_id"`
    Duration int    `json:"duration"`
}
```

------------------------------------------------------------------------

## 4️⃣ Queue → Client: Job Accepted

After receiving a job, the queue should respond:

``` json
{
  "status": "accepted",
  "job_id": "job1"
}
```

Go struct:

``` go
type SubmitJobResponse struct {
    Status string `json:"status"`
    JobID  string `json:"job_id"`
}
```

------------------------------------------------------------------------

## 5️⃣ Worker → Queue: Request Job

Workers must request jobs from the queue.

Example message:

``` json
{
  "type": "request_job",
  "worker_id": "worker1"
}
```

Go struct:

``` go
type RequestJob struct {
    Type     string `json:"type"`
    WorkerID string `json:"worker_id"`
}
```

------------------------------------------------------------------------

## 6️⃣ Queue → Worker: Assign Job

If a job is available:

``` json
{
  "type": "assign_job",
  "job_id": "job1",
  "duration": 5
}
```

Go struct:

``` go
type AssignJob struct {
    Type     string `json:"type"`
    JobID    string `json:"job_id"`
    Duration int    `json:"duration"`
}
```

If no job is available:

``` json
{
  "type": "no_job"
}
```

------------------------------------------------------------------------

## 7️⃣ Worker → Queue: Job Completed

After finishing execution, worker must send:

``` json
{
  "type": "job_complete",
  "job_id": "job1",
  "worker_id": "worker1"
}
```

Go struct:

``` go
type JobComplete struct {
    Type     string `json:"type"`
    JobID    string `json:"job_id"`
    WorkerID string `json:"worker_id"`
}
```

------------------------------------------------------------------------

## 8️⃣ Encoding JSON in Go

To send a message:

``` go
encoder := json.NewEncoder(conn)
err := encoder.Encode(message)
if err != nil {
    panic(err)
}
```

Important: - `Encode()` automatically adds a newline. - This helps with
streaming multiple messages.

------------------------------------------------------------------------

## 9️⃣ Decoding JSON in Go

To read a message:

``` go
decoder := json.NewDecoder(conn)

var msg SubmitJobRequest
err := decoder.Decode(&msg)
if err != nil {
    return
}
```

Important: - `Decode()` blocks until it receives a complete JSON
message. - Always handle errors.

------------------------------------------------------------------------

## 🔟 Full Runnable Example (JSON Echo Server)

Below is a minimal runnable example that:

-   Accepts a JSON message\
-   Prints it\
-   Sends a response

------------------------------------------------------------------------

### Server (`server.go`)

``` go
package main

import (
    "encoding/json"
    "fmt"
    "net"
)

type Message struct {
    Type  string `json:"type"`
    Value string `json:"value"`
}

func main() {
    listener, _ := net.Listen("tcp", ":9090")
    defer listener.Close()

    fmt.Println("JSON server running on port 9090")

    for {
        conn, _ := listener.Accept()
        go handle(conn)
    }
}

func handle(conn net.Conn) {
    defer conn.Close()

    var msg Message
    json.NewDecoder(conn).Decode(&msg)

    fmt.Println("Received:", msg)

    response := Message{
        Type:  "response",
        Value: "Message received",
    }

    json.NewEncoder(conn).Encode(response)
}
```

------------------------------------------------------------------------

### Client (`client.go`)

``` go
package main

import (
    "encoding/json"
    "fmt"
    "net"
)

type Message struct {
    Type  string `json:"type"`
    Value string `json:"value"`
}

func main() {
    conn, _ := net.Dial("tcp", "localhost:9090")
    defer conn.Close()

    msg := Message{
        Type:  "greeting",
        Value: "Hello from client",
    }

    json.NewEncoder(conn).Encode(msg)

    var response Message
    json.NewDecoder(conn).Decode(&response)

    fmt.Println("Server response:", response)
}
```

------------------------------------------------------------------------

## 11️⃣ Important Rules for This Project

All messages must:

-   Be valid JSON\
-   Use clearly defined fields\
-   Include a `"type"` field\
-   Be encoded using `json.NewEncoder`\
-   Be decoded using `json.NewDecoder`

Do NOT:

-   Send raw strings\
-   Mix string-based protocol with JSON\
-   Use inconsistent field names

------------------------------------------------------------------------

## 12️⃣ Why This Matters

The JSON protocol defines the **contract** between:

-   Queue\
-   Workers\
-   Clients

If all components follow the same message format, the system will work.

If message formats are inconsistent, the system will fail.

In the next tutorial, we will design the full system architecture and
define how these messages flow through the system.
