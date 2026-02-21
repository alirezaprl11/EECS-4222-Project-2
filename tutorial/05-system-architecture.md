# 05 - System Architecture

Now that you understand:

-   Go basics
-   Networking
-   Concurrency
-   JSON messaging

we can design the full Distributed Job Queue architecture.

This document defines:

-   System components
-   Responsibilities
-   Communication flow
-   Internal state design
-   Execution lifecycle

------------------------------------------------------------------------

## 1) High-Level Architecture

The system consists of three components:

            +---------+
            |  Client |
            +----+----+
                 |
                 v
            +----+----+
            |  Queue  |
            +----+----+
                 ^
                 |
          +------+------
          |             |
    +-----+-----+ +-----+-----+
    |   Worker 1 | |   Worker 2 |
    +-------------+ +-------------+

------------------------------------------------------------------------

## 2) Component Responsibilities

### Client

The client:

-   Connects to the Queue
-   Submits jobs
-   Optionally queries job status

It does NOT:

-   Execute jobs
-   Store state

------------------------------------------------------------------------

### Queue (Central Coordinator)

The Queue is the core of the system.

It must:

-   Accept job submissions
-   Store pending jobs
-   Track job status
-   Assign jobs to workers
-   Track running jobs
-   Detect timeouts
-   Reassign failed jobs

The Queue must be concurrent-safe.

------------------------------------------------------------------------

### Worker

Each worker:

-   Connects to the Queue
-   Requests a job
-   Executes it (simulate with sleep)
-   Reports completion

Workers do NOT communicate with each other.

------------------------------------------------------------------------

## 3) Communication Flow

### Step 1: Client Submits Job

Client → Queue:

``` json
{
  "type": "submit_job",
  "job_id": "job1",
  "duration": 5
}
```

Queue:

-   Stores job in pending list
-   Responds with confirmation

------------------------------------------------------------------------

### Step 2: Worker Requests Job

Worker → Queue:

``` json
{
  "type": "request_job",
  "worker_id": "worker1"
}
```

Queue:

-   If job available → assign
-   If none → return "no_job"

------------------------------------------------------------------------

### Step 3: Worker Executes Job

Worker:

-   Sleeps for duration seconds
-   Sends completion message

------------------------------------------------------------------------

### Step 4: Worker Reports Completion

Worker → Queue:

``` json
{
  "type": "job_complete",
  "job_id": "job1",
  "worker_id": "worker1"
}
```

Queue:

-   Marks job as completed

------------------------------------------------------------------------

## 4) Internal Queue State Design

The Queue must track:

-   Pending jobs
-   Running jobs
-   Completed jobs
-   Worker assignments

Example internal structure:

``` go
type Job struct {
    ID       string
    Duration int
}

type Queue struct {
    pendingJobs   []Job
    runningJobs   map[string]string // jobID → workerID
    completedJobs map[string]bool
    mu            sync.Mutex
}
```

------------------------------------------------------------------------

## 5) Concurrency Model

The Queue must:

-   Handle multiple clients
-   Handle multiple workers
-   Protect shared state

Pattern:

Accept connection\
→ Start goroutine\
→ Decode message\
→ Lock queue state\
→ Modify state\
→ Unlock\
→ Send response

Every connection handler must run in its own goroutine.

------------------------------------------------------------------------

## 6) Failure Handling (Simplified Model)

In this project, we assume:

-   Workers may crash
-   Connections may drop

We do NOT implement:

-   Network partitions
-   Consensus protocols
-   Persistent storage

### Timeout Model

Queue must detect if:

-   A job is assigned
-   Worker never reports completion

Solution:

-   Store timestamp when job assigned
-   Periodically check running jobs
-   If timeout exceeded → requeue job

Example:

``` go
type RunningJob struct {
    WorkerID  string
    StartTime time.Time
}
```

------------------------------------------------------------------------

## 7) Execution Lifecycle Example

Let's walk through a complete lifecycle:

1.  Client submits 3 jobs.
2.  Queue stores them in pending list.
3.  Worker 1 requests job → gets Job A.
4.  Worker 2 requests job → gets Job B.
5.  Worker 1 completes Job A.
6.  Queue marks Job A completed.
7.  Worker 1 requests next job → gets Job C.
8.  Worker 2 crashes before finishing Job B.
9.  Timeout triggers → Job B returned to pending list.
10. Worker 1 eventually picks Job B.

This is real distributed behavior.

------------------------------------------------------------------------

## 8) Thread-Safety Requirements

You must protect:

-   pendingJobs
-   runningJobs
-   completedJobs

Never modify shared maps or slices without locking:

``` go
q.mu.Lock()
...
q.mu.Unlock()
```

Better pattern:

``` go
q.mu.Lock()
defer q.mu.Unlock()
```

------------------------------------------------------------------------

## 9) What We Are NOT Building

To keep project scope reasonable, we are NOT building:

-   Distributed consensus (Raft)
-   Multi-leader system
-   Persistent storage
-   Database-backed queue
-   Worker-to-worker communication
-   Load balancing strategies

We are building:

A single central queue with multiple concurrent workers.

------------------------------------------------------------------------

## 10) Architectural Constraints

You must:

-   Use TCP sockets
-   Use JSON messaging
-   Use goroutines for concurrency
-   Protect shared state with mutex
-   Reassign timed-out jobs

You must NOT:

-   Use external frameworks
-   Use databases
-   Use third-party message brokers
-   Use HTTP libraries

------------------------------------------------------------------------

## 11) Final System Behavior

At runtime, the system should:

-   Accept unlimited job submissions
-   Support multiple workers
-   Assign jobs fairly (first-come-first-serve)
-   Handle worker failure
-   Maintain consistent job state

------------------------------------------------------------------------

## 12) What Comes Next

In the next tutorial, we will implement:

-   The Queue server
-   Its internal state
-   Connection handling
-   Job assignment logic
