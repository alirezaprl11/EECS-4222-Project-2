# 05 -- System Architecture (Student Version)

Now that you understand:

-   Go basics\
-   Networking\
-   Concurrency\
-   JSON messaging

we define the architecture of your Distributed Job Queue system.

This document describes **what your system must do**, but it does NOT
prescribe how to implement it internally.

------------------------------------------------------------------------

## 1) High-Level Architecture

Your system must consist of three components:

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
          +------+------+
          |             |
    +-----+-----+ +-----+-----+
    |   Worker 1 | |   Worker 2 |
    +-------------+ +-------------+

------------------------------------------------------------------------

## 2) Component Responsibilities

### Client

The Client must:

-   Connect to the Queue
-   Submit jobs
-   Receive confirmation

The Client does not execute jobs.

------------------------------------------------------------------------

### Queue

The Queue is the central coordinator.

It must:

-   Accept job submissions
-   Store pending jobs
-   Assign jobs to workers
-   Track job status
-   Detect worker failure
-   Reassign unfinished jobs

The Queue must handle multiple connections concurrently.

------------------------------------------------------------------------

### Worker

Each Worker must:

-   Connect to the Queue
-   Request a job
-   Execute the job (simulate using sleep)
-   Notify the Queue when finished

Workers do not communicate with each other.

------------------------------------------------------------------------

## 3) Required System Behavior

Your system must support:

### Job Submission

-   Multiple jobs can be submitted.
-   Jobs must be stored until executed.

### Job Assignment

-   Workers request jobs.
-   Jobs must be assigned fairly (first-come-first-serve).

### Job Completion

-   Workers must report completion.
-   Completed jobs must not be reassigned.

### Failure Handling

-   If a worker fails while executing a job, the job must eventually be
    reassigned.

You must design how failure is detected.

------------------------------------------------------------------------

## 4) Concurrency Requirements

The Queue must:

-   Handle multiple client connections
-   Handle multiple worker connections
-   Protect shared data structures
-   Avoid race conditions

------------------------------------------------------------------------

## 5) Architectural Constraints

You must:

-   Use TCP sockets
-   Use JSON messaging
-   Use goroutines
-   Use synchronization primitives (`sync.Mutex`, etc.)
-   Keep all data in memory

You must NOT:

-   Use databases
-   Use HTTP frameworks
-   Use external libraries
-   Use third-party message brokers

------------------------------------------------------------------------

## 6) Design Decisions (You Must Decide)

You must design:

-   How jobs are stored internally
-   How running jobs are tracked
-   How worker failure is detected
-   How timeouts are implemented
-   How job state transitions are handled

Your design must be explained in your report.

------------------------------------------------------------------------

## 7) Expected Runtime Behavior

At runtime, the system should:

-   Accept multiple job submissions
-   Allow multiple workers
-   Reassign jobs if a worker crashes
-   Maintain consistent job state

------------------------------------------------------------------------

## 8) What You Will Implement Next

In the next tutorial, you will begin implementing:

-   The Queue server
-   Its internal state
-   Connection handlers
-   Job assignment logic

This is where your design decisions become code.
