# 06 - Queue Server Implementation

In this tutorial, you will begin implementing the core component of the
system:\
the **Queue server**.

This is the central coordinator responsible for:

-   Accepting job submissions
-   Assigning jobs to workers
-   Tracking job status
-   Handling concurrency safely

------------------------------------------------------------------------

## 1) Responsibilities of the Queue Server

Your Queue server must:

-   Listen on a TCP port
-   Accept multiple concurrent connections
-   Decode incoming JSON messages
-   Maintain shared in-memory state
-   Assign jobs fairly (first-come-first-serve)
-   Reassign timed-out jobs

------------------------------------------------------------------------

## 2) Basic Server Skeleton

Start by creating `queue.go`.

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

    fmt.Println("Queue server running on port 8080")

    for {
        conn, err := listener.Accept()
        if err != nil {
            continue
        }
        go handleConnection(conn)
    }
}
```

Each connection must be handled in a separate goroutine.

------------------------------------------------------------------------

## 3) Shared State Structure

Your queue must maintain internal state safely.

Example structure:

``` go
type Job struct {
    ID       string
    Duration int
}

type Queue struct {
    pendingJobs   []Job
    runningJobs   map[string]string
    completedJobs map[string]bool
    mu            sync.Mutex
}
```

You must protect all shared data using a mutex.

------------------------------------------------------------------------

## 4) Handling Messages

Each connection handler should:

1.  Decode the incoming JSON message
2.  Inspect the `"type"` field
3.  Perform the correct action
4.  Send a JSON response

Example skeleton:

``` go
func handleConnection(conn net.Conn) {
    defer conn.Close()

    var msg Message
    err := json.NewDecoder(conn).Decode(&msg)
    if err != nil {
        return
    }

    switch msg.Type {
    case "submit_job":
        handleSubmit(conn, msg)
    case "request_job":
        handleRequest(conn, msg)
    case "job_complete":
        handleCompletion(conn, msg)
    }
}
```

------------------------------------------------------------------------

## 5) Handling Job Submission

When receiving a job submission:

-   Lock the queue
-   Append the job to pending list
-   Unlock
-   Send confirmation response

------------------------------------------------------------------------

## 6) Handling Job Requests

When a worker requests a job:

-   Lock queue
-   If pending jobs exist:
    -   Remove first job
    -   Add to running jobs
    -   Unlock
    -   Send job assignment
-   If no jobs:
    -   Unlock
    -   Send `"no_job"` response

------------------------------------------------------------------------

## 7) Handling Job Completion

When worker reports completion:

-   Lock queue
-   Remove job from running list
-   Mark job as completed
-   Unlock

------------------------------------------------------------------------

## 8) Timeout Checker (Background Goroutine)

To detect worker failures:

-   When assigning a job, record timestamp
-   Start a background goroutine:

``` go
go func() {
    for {
        time.Sleep(2 * time.Second)
        checkTimeouts()
    }
}()
```

Inside `checkTimeouts()`:

-   Lock queue
-   Check running jobs
-   If timeout exceeded → move job back to pending
-   Unlock

------------------------------------------------------------------------

## 9) Concurrency Safety Rules

Always:

``` go
q.mu.Lock()
defer q.mu.Unlock()
```

Never:

-   Modify slices or maps without locking
-   Hold lock longer than necessary
-   Block on network operations while holding lock

------------------------------------------------------------------------

## 10) Testing the Queue

To test:

1.  Run queue server
2.  Start multiple workers
3.  Submit multiple jobs
4.  Kill one worker mid-execution
5.  Verify job reassignment

------------------------------------------------------------------------

## 11) Common Mistakes

-   Forgetting mutex protection
-   Blocking forever on JSON decode
-   Not removing completed jobs properly
-   Not requeuing timed-out jobs

------------------------------------------------------------------------

## 12) What Comes Next

Next tutorial:

-   Implementing the Worker
-   Connecting Worker to Queue
-   Full system integration testing

You now have the foundation for a real concurrent distributed queue.
