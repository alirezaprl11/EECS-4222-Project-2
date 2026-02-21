# EECS 4222 - Project 2 (Distributed Job Queue)
------------------------------------------------------------------------

## 📌 Project Overview

In this project, you will design and implement a **Distributed Job Queue
System** using the Go programming language.

Modern cloud and AI systems rely on distributed task scheduling to
execute jobs across multiple workers. In this project, you will build a
simplified version of such a system.

Your system will:

-   Accept job submissions
-   Distribute jobs to multiple workers
-   Detect worker failures
-   Reassign unfinished jobs
-   Handle concurrent requests safely

This project runs entirely **locally** and does not require Kubernetes,
Docker, or virtual machines.

All components must be written in **Go**.

------------------------------------------------------------------------

## 🎯 Learning Objectives

By completing this project, you will:

-   Gain hands-on experience with Go concurrency
-   Implement TCP-based communication between processes
-   Design a distributed coordination system
-   Handle crash failures and timeouts
-   Build a fault-tolerant task distribution system
-   Understand how distributed AI workloads are scheduled

------------------------------------------------------------------------

## 🏗 System Architecture

Your system will contain three components:

### 1️⃣ Queue Server

Responsible for: 
- Receiving job submissions
- Storing pending jobs
- Assigning jobs to workers
- Tracking job status
- Detecting worker timeouts
- Reassigning unfinished jobs

### 2️⃣ Worker Nodes

Responsible for: 
- Registering with the queue
- Requesting jobs
- Executing assigned jobs
- Returning completion results

### 3️⃣ Client

Responsible for: 
- Submitting jobs
- Querying job status

All components must communicate using TCP and JSON messages.

------------------------------------------------------------------------

## 🧱 Job Definition

Each job must contain:

-   Job ID
-   Duration (in seconds)

Example job:

``` json
{
  "id": "job1",
  "duration": 5
}
```

Workers simulate execution using:

``` go
time.Sleep(time.Duration(job.Duration) * time.Second)
```

------------------------------------------------------------------------

## 📂 Repository Structure

    .
    ├── tutorial/
    |   ├── 00-requirements.md
    │   ├── 01-go-basics.md
    │   ├── 02-networking.md
    │   ├── 03-concurrency.md
    │   ├── 04-json-messaging.md
    │   ├── 05-system-architecture.md
    │   ├── 06-queue-server.md
    │   ├── 07-worker.md
    │   ├── 08-client.md
    │   ├── 09-integration-failure-testing.md
    │   └── 10-evaluation.md
    │
    └── README.md

You must follow the tutorials in order.

------------------------------------------------------------------------

## 🔧 Technical Requirements

-   Programming Language: Go
-   Communication Protocol: TCP
-   Message Format: JSON
-   Concurrency: goroutines and mutexes
-   Storage: In-memory only
-   No external frameworks allowed

You may use only Go's standard library.

------------------------------------------------------------------------

## 📊 Evaluation

Detailed grading criteria are provided in:

    tutorials/10-evaluation.md

------------------------------------------------------------------------

## 🧠 Why This Project Matters

Distributed job scheduling is a core building block of:

-   AI training systems
-   Cloud orchestration platforms
-   Data processing pipelines
-   Large-scale backend systems

This project mirrors how real distributed platforms coordinate work
across multiple nodes.
