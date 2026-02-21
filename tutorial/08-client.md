# 08 - Client Implementation

In this tutorial, you will implement the **Client** component.

The Client is responsible for:

-   Connecting to the Queue
-   Submitting jobs
-   Receiving confirmation
-   (Optional) Querying job status

The Client does NOT execute jobs.

------------------------------------------------------------------------

## 1) Responsibilities of the Client

The Client must:

-   Create a TCP connection to the Queue
-   Send a `submit_job` JSON message
-   Read a JSON confirmation response
-   Exit cleanly

------------------------------------------------------------------------

## 2) Client Message Types

### Submit Job Request

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

### Submit Job Response

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

## 3) Basic Client Program

Create `client.go`:

``` go
package main

import (
    "encoding/json"
    "fmt"
    "net"
    "os"
    "strconv"
)

type SubmitJobRequest struct {
    Type     string `json:"type"`
    JobID    string `json:"job_id"`
    Duration int    `json:"duration"`
}

type SubmitJobResponse struct {
    Status string `json:"status"`
    JobID  string `json:"job_id"`
}

func main() {
    if len(os.Args) != 3 {
        fmt.Println("Usage: go run client.go <job_id> <duration_seconds>")
        os.Exit(1)
    }

    jobID := os.Args[1]
    duration, err := strconv.Atoi(os.Args[2])
    if err != nil || duration <= 0 {
        fmt.Println("Duration must be a positive integer")
        os.Exit(1)
    }

    conn, err := net.Dial("tcp", "localhost:8080")
    if err != nil {
        fmt.Println("Unable to connect to queue:", err)
        os.Exit(1)
    }
    defer conn.Close()

    req := SubmitJobRequest{
        Type:     "submit_job",
        JobID:    jobID,
        Duration: duration,
    }

    if err := json.NewEncoder(conn).Encode(req); err != nil {
        fmt.Println("Failed to send request:", err)
        os.Exit(1)
    }

    var resp SubmitJobResponse
    if err := json.NewDecoder(conn).Decode(&resp); err != nil {
        fmt.Println("Failed to read response:", err)
        os.Exit(1)
    }

    fmt.Println("Queue response:", resp.Status, "job_id:", resp.JobID)
}
```

------------------------------------------------------------------------

## 4) How to Run the Client

Start the queue server in Terminal 1:

    go run queue.go

Then in Terminal 2, submit a job:

    go run client.go job1 5

Expected output:

    Queue response: accepted job_id: job1

Submit multiple jobs:

    go run client.go job2 2
    go run client.go job3 7

------------------------------------------------------------------------

## 5) Testing With Workers

1.  Start Queue
2.  Start 1--2 Workers
3.  Submit several jobs via the Client
4.  Observe that jobs are distributed to workers

------------------------------------------------------------------------

## 6) Common Mistakes

-   Not validating command-line arguments
-   Using non-unique job IDs
-   Forgetting to read the queue response
-   Leaving connections open (always `defer conn.Close()`)

------------------------------------------------------------------------

## 7) Optional Extension: Batch Submission

For extra testing, you may extend the client to submit multiple jobs in
a loop (e.g., from a file). This is not required. 

------------------------------------------------------------------------

## 8) What Comes Next

Next tutorial:

-   Full integration testing
-   Failure testing (kill a worker mid-job)
-   Stress testing with many jobs and multiple workers
