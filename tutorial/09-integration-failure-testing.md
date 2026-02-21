# 09 -- Integration and Failure Testing

This tutorial explains how to test your full system end-to-end:

-   Queue server
-   Multiple workers
-   Client job submissions
-   Failure cases (worker crash)
-   Timeout-based job reassignment

You will test that your system behaves like a real distributed job
queue.

------------------------------------------------------------------------

## 1) What You Must Verify

Your system must demonstrate:

### Integration (Correctness)

-   Client can submit jobs successfully.
-   Queue stores jobs reliably.
-   Workers can request and receive jobs.
-   Workers execute jobs and report completion.
-   Queue marks jobs completed correctly.

### Failure Handling (Robustness)

-   If a worker crashes during execution, the job is not lost.
-   After a timeout, the Queue reassigns the job to another worker.
-   No completed job is reassigned.

------------------------------------------------------------------------

## 2) Recommended Setup

Use three terminals (minimum):

-   Terminal 1: Queue
-   Terminal 2: Worker 1
-   Terminal 3: Worker 2
-   Terminal 4 (optional): Client submissions

You can run everything locally.

------------------------------------------------------------------------

## 3) Integration Test: Basic End-to-End Run

### Step 1 --- Start the Queue

Terminal 1:

    go run queue.go

Expected output example:

    Queue server running on port 8080

------------------------------------------------------------------------

### Step 2 --- Start Two Workers

Terminal 2:

    go run worker.go

Terminal 3:

    go run worker.go

Expected behavior: - Workers repeatedly request jobs - If no jobs exist,
they wait/retry (depends on your implementation)

------------------------------------------------------------------------

### Step 3 --- Submit Jobs

Terminal 4 (or reuse one terminal):

    go run client.go job1 5
    go run client.go job2 2
    go run client.go job3 7

Expected behavior: - Queue responds "accepted" for each submission -
Workers receive jobs and print logs for execution - Queue prints logs
for assignment/completion

------------------------------------------------------------------------

## 4) Integration Test: Concurrency Stress (Lightweight)

Submit many jobs quickly:

    go run client.go jobA 1
    go run client.go jobB 1
    go run client.go jobC 1
    go run client.go jobD 1
    go run client.go jobE 1

Expected behavior: - Jobs should not be duplicated - No job should be
lost - Queue should not crash

(Optional) Start 3--5 workers to increase concurrency.

------------------------------------------------------------------------

## 5) Failure Test: Worker Crash During Execution

This test verifies job reassignment.

### Step 1 --- Submit a Long Job

Submit one long job (10 seconds or more):

    go run client.go jobLong 15

### Step 2 --- Confirm a Worker Started Executing It

In the worker output, you should see something like:

-   Received job: jobLong
-   Executing job: jobLong

### Step 3 --- Kill the Worker Mid-Execution

While the worker is still sleeping/executing, stop that worker:

-   Press `Ctrl + C` in the worker terminal

This simulates a worker crash.

------------------------------------------------------------------------

## 6) Expected Behavior After Crash

The Queue should not mark the job completed.

Instead, after your timeout expires:

-   Queue should move jobLong back to pending
-   Another worker should eventually receive it
-   That worker should complete it
-   Queue marks it completed

This is the main failure-handling requirement.

------------------------------------------------------------------------

## 7) Failure Test: No Reassignment Should Happen After Completion

Submit a short job:

    go run client.go jobShort 1

Let it complete normally.

Expected: - JobShort should never be reassigned - It must remain
completed

------------------------------------------------------------------------

## 8) Debugging Checklist

If tests fail, check the following:

### Jobs are "lost"

-   You forgot to requeue timed-out jobs.
-   You remove jobs incorrectly from your structures.

### Jobs are duplicated

-   Two workers can fetch the same job.
-   This usually means missing mutex lock around job assignment.

### Queue crashes randomly

-   Shared map/slice modified without mutex.

-   Run:

        go run -race queue.go

### Worker blocks forever

-   Queue does not send a response.
-   Your JSON decode/encode order does not match.

------------------------------------------------------------------------

## 9) What to Include in Your Report

You should include:

-   A screenshot or log snippet of successful integration test
-   A screenshot or log snippet showing worker crash + reassignment
-   Brief explanation of your timeout strategy:
    -   timeout length
    -   how often you check
    -   what happens when timeout triggers

------------------------------------------------------------------------

## 10) Optional Advanced Tests (Not Required)

If you want to go further (bonus):

-   Submit jobs while workers are already busy.
-   Start/stop workers randomly.
-   Submit 50+ jobs in a loop.
-   Add a small script to generate jobs automatically.

------------------------------------------------------------------------

## Summary

If your system passes these tests, you have demonstrated:

-   Correct end-to-end distributed execution
-   Safe concurrency handling
-   Realistic worker failure handling
-   Job timeout and reassignment logic
