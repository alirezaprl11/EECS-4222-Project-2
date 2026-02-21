# 03 -- Concurrency in Go

Distributed systems are inherently concurrent.

In Project 2:

-   Multiple clients may submit jobs at the same time.
-   Multiple workers may request jobs simultaneously.
-   The queue must handle all of this safely.

This tutorial explains:

-   Goroutines
-   Race conditions
-   Mutex
-   WaitGroup
-   Why synchronization matters

------------------------------------------------------------------------

## 1) What Is Concurrency?

Concurrency means multiple tasks making progress at the same time.

In Go, concurrency is handled using:

-   Goroutines
-   Channels
-   Synchronization primitives (mutex, wait groups, etc.)

In this project, we will primarily use:

-   Goroutines
-   Mutex
-   WaitGroup

------------------------------------------------------------------------

## 2) Goroutines

A goroutine is a lightweight thread managed by Go.

Start one using:

``` go
go someFunction()
```

### Example: Multiple Goroutines

``` go
package main

import (
    "fmt"
    "time"
)

func worker(id int) {
    fmt.Println("Worker", id, "started")
    time.Sleep(1 * time.Second)
    fmt.Println("Worker", id, "finished")
}

func main() {
    for i := 1; i <= 3; i++ {
        go worker(i)
    }

    time.Sleep(2 * time.Second)
    fmt.Println("Main finished")
}
```

------------------------------------------------------------------------

## 3) Race Conditions

A race condition occurs when:

-   Multiple goroutines access shared data
-   At least one modifies it
-   No synchronization is used

### Example: Race Condition

``` go
package main

import (
    "fmt"
)

func main() {
    counter := 0

    for i := 0; i < 5; i++ {
        go func() {
            counter++
        }()
    }

    fmt.Println("Counter:", counter)
}
```

To detect race conditions:

    go run -race main.go

------------------------------------------------------------------------

## 4) Fixing Race Conditions with Mutex

A mutex ensures only one goroutine modifies shared data at a time.

### Example: Using Mutex

``` go
package main

import (
    "fmt"
    "sync"
)

func main() {
    counter := 0
    var mu sync.Mutex
    var wg sync.WaitGroup

    for i := 0; i < 5; i++ {
        wg.Add(1)
        go func() {
            defer wg.Done()
            mu.Lock()
            counter++
            mu.Unlock()
        }()
    }

    wg.Wait()
    fmt.Println("Counter:", counter)
}
```

------------------------------------------------------------------------

## 5) WaitGroup

Without a WaitGroup, main may exit before goroutines finish.

### Example: Proper Synchronization

``` go
package main

import (
    "fmt"
    "sync"
)

func main() {
    var wg sync.WaitGroup

    for i := 1; i <= 3; i++ {
        wg.Add(1)
        go func(id int) {
            defer wg.Done()
            fmt.Println("Worker", id, "done")
        }(i)
    }

    wg.Wait()
    fmt.Println("All workers finished")
}
```

------------------------------------------------------------------------

## 6) Why This Matters for the Queue

In Project 2, your Queue will:

-   Store jobs in a slice or map
-   Update job status
-   Assign jobs to workers
-   Remove completed jobs

Multiple goroutines will access this shared data:

-   Client handlers
-   Worker handlers
-   Timeout checkers

If you do not protect shared data:

-   Jobs may be lost
-   Status may be incorrect
-   Program may crash

You MUST protect shared state with a mutex.

------------------------------------------------------------------------

## 7) Designing Shared State Safely

Example shared state:

``` go
type Queue struct {
    jobs      []Job
    jobStatus map[string]string
    mu        sync.Mutex
}
```

When modifying:

``` go
q.mu.Lock()
q.jobs = append(q.jobs, newJob)
q.mu.Unlock()
```

Never modify shared slices or maps without locking.

------------------------------------------------------------------------

## 8) Common Concurrency Mistakes

-   Forgetting to unlock a mutex
-   Forgetting WaitGroup
-   Modifying shared slices/maps without mutex

Better locking pattern:

``` go
mu.Lock()
defer mu.Unlock()
```

------------------------------------------------------------------------

## 9) Summary

From this tutorial, you must understand:

-   What goroutines are
-   How race conditions occur
-   How to use sync.Mutex
-   How to use sync.WaitGroup
-   Why shared state must be protected

In the next tutorial, we will combine networking and concurrency to
implement the Queue server.
