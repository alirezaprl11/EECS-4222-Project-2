# 07 - Worker Implementation

In this tutorial, you will implement the **Worker** component.

Workers are responsible for:

-   Connecting to the Queue
-   Requesting jobs
-   Executing jobs
-   Reporting completion

Workers do NOT store global state and do NOT communicate with each
other.

------------------------------------------------------------------------

## 1) Responsibilities of the Worker

Each Worker must:

-   Establish a TCP connection to the Queue
-   Send a `request_job` message
-   Wait for a response
-   If assigned a job:
    -   Simulate execution using `time.Sleep`
    -   Send `job_complete` message
-   Repeat continuously

------------------------------------------------------------------------

## 2) Basic Worker Skeleton

Create `worker.go`:

``` go
package main

import (
    "encoding/json"
    "fmt"
    "net"
    "time"
)

func main() {
    for {
        runWorker("worker1")
        time.Sleep(2 * time.Second)
    }
}
```

------------------------------------------------------------------------

## 3) Connecting to the Queue

Inside `runWorker`:

``` go
func runWorker(workerID string) {
    conn, err := net.Dial("tcp", "localhost:8080")
    if err != nil {
        fmt.Println("Unable to connect to queue")
        return
    }
    defer conn.Close()

    requestJob(conn, workerID)
}
```

------------------------------------------------------------------------

## 4) Requesting a Job

Define the request structure:

``` go
type RequestJob struct {
    Type     string `json:"type"`
    WorkerID string `json:"worker_id"`
}
```

Send request:

``` go
func requestJob(conn net.Conn, workerID string) {
    req := RequestJob{
        Type:     "request_job",
        WorkerID: workerID,
    }

    json.NewEncoder(conn).Encode(req)

    handleAssignment(conn, workerID)
}
```

------------------------------------------------------------------------

## 5) Handling Job Assignment

Example assignment structure:

``` go
type AssignJob struct {
    Type     string `json:"type"`
    JobID    string `json:"job_id"`
    Duration int    `json:"duration"`
}
```

Decode response:

``` go
func handleAssignment(conn net.Conn, workerID string) {
    var response AssignJob
    err := json.NewDecoder(conn).Decode(&response)
    if err != nil {
        return
    }

    if response.Type == "assign_job" {
        fmt.Println("Received job:", response.JobID)
        executeJob(response, workerID)
    }
}
```

------------------------------------------------------------------------

## 6) Executing a Job

Simulate execution:

``` go
func executeJob(job AssignJob, workerID string) {
    fmt.Println("Executing job:", job.JobID)
    time.Sleep(time.Duration(job.Duration) * time.Second)
    fmt.Println("Finished job:", job.JobID)
    reportCompletion(job.JobID, workerID)
}
```

------------------------------------------------------------------------

## 7) Reporting Completion

``` go
type JobComplete struct {
    Type     string `json:"type"`
    JobID    string `json:"job_id"`
    WorkerID string `json:"worker_id"`
}

func reportCompletion(jobID string, workerID string) {
    conn, err := net.Dial("tcp", "localhost:8080")
    if err != nil {
        return
    }
    defer conn.Close()

    msg := JobComplete{
        Type:     "job_complete",
        JobID:    jobID,
        WorkerID: workerID,
    }

    json.NewEncoder(conn).Encode(msg)
}
```

------------------------------------------------------------------------

## 8) Continuous Worker Loop

Workers should:

-   Request job
-   Execute if assigned
-   Repeat

If `"no_job"` received, wait and retry.

------------------------------------------------------------------------

## 9) Simulating Worker Failure

To test failure handling:

-   Start worker
-   Submit jobs
-   Kill worker mid-execution
-   Ensure queue reassigns job after timeout

------------------------------------------------------------------------

## 10) Concurrency Notes

Workers do not need mutex protection for internal state.

However:

-   They must handle connection errors
-   They must not block forever

------------------------------------------------------------------------

## 11) Testing the Worker

1.  Start Queue
2.  Start multiple workers
3.  Submit multiple jobs
4.  Observe job distribution
5.  Kill a worker
6.  Verify reassignment

------------------------------------------------------------------------

## 12) What Comes Next

Next tutorial:

-   Implementing the Client
-   Full system integration test
-   Stress testing with multiple workers

You now have two main components:

-   Queue
-   Worker

The final step is connecting everything together.
