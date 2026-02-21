# 10 -- Project Evaluation and Submission Guidelines

This document explains:

-   What you must submit
-   What your report must include
-   What your demo video must show
-   How grading will be performed

Please read this carefully to avoid losing marks due to incomplete
submission.

------------------------------------------------------------------------

# 1) Required Deliverables

You must submit **ONE ZIP file** on eClass containing:

1.  All source code (Queue, Worker, Client)
2.  Your written report (PDF format)
3.  Any additional scripts used for testing (if applicable)

In addition, you must upload a **demo video (max 10 minutes)** as
instructed on eClass.

If you have questions, post them on the **eClass discussion forum**. Do
not email questions individually unless necessary.

------------------------------------------------------------------------

# 2) Written Report Requirements

There is **no strict page limit**.\
Write clearly and concisely. Quality matters more than length.

Your report must include the following sections:

------------------------------------------------------------------------

## 2.1 System Overview

-   Brief description of your architecture
-   Explanation of each component (Queue, Worker, Client)
-   A simple diagram of your system (can be hand-drawn or digital)

------------------------------------------------------------------------

## 2.2 Design Decisions

You must clearly explain:

-   How you store pending jobs
-   How you track running jobs
-   How you track completed jobs
-   How job state transitions are handled
-   How worker failure is detected
-   How timeout logic works
-   Why you chose your data structures

Be specific. Do not just say "we used a map."\
Explain why.

------------------------------------------------------------------------

## 2.3 Concurrency Handling

You must explain:

-   How you prevent race conditions
-   Where and why you use mutex locks
-   How goroutines are used
-   How you ensure thread safety

If applicable, mention whether you tested using:

    go run -race

------------------------------------------------------------------------

## 2.4 Failure Handling Strategy

Explain:

-   What happens when a worker crashes
-   How long your timeout is
-   How frequently you check for timeouts
-   How jobs are reassigned
-   Why your strategy works

------------------------------------------------------------------------

## 2.5 Testing Methodology

Describe:

-   How you tested integration
-   How you tested worker crashes
-   What logs or evidence show correctness
-   Any issues you encountered and how you fixed them

Screenshots of logs are encouraged.

------------------------------------------------------------------------

## 2.6 Limitations

Discuss:

-   What your system does NOT handle
-   Known edge cases
-   Possible improvements

This section shows understanding of distributed systems trade-offs.

------------------------------------------------------------------------

# 3) Demo Video Requirements (Max 10 Minutes)

You must record a video that includes:

-   Your face visible (camera on)
-   Screen recording of your system running
-   Explanation of your design

The video does NOT need to be 10 minutes.\
Keep it concise but complete.

------------------------------------------------------------------------

## Your video must demonstrate:

1.  Starting the Queue server
2.  Starting at least two Workers
3.  Submitting multiple jobs
4.  Showing job distribution
5.  Killing one worker mid-execution
6.  Showing job reassignment after timeout
7.  Explaining how your design works internally

You must explain:

-   Your data structures
-   Your timeout logic
-   Your concurrency model

Do not simply run commands silently.

------------------------------------------------------------------------

# 4) Grading Criteria

Your grade will be based on:

### Correctness

-   Jobs are assigned correctly
-   Completed jobs are not reassigned
-   No job is lost

### Concurrency Safety

-   No race conditions
-   Proper mutex usage

### Failure Handling

-   Worker crash leads to reassignment
-   Timeout logic works properly

### Code Quality

-   Clean structure
-   Logical organization
-   Proper error handling

### Report Quality

-   Clear explanations
-   Technical understanding
-   Justification of design decisions

### Video Clarity

-   Clear explanation
-   Demonstrated functionality
-   Professional presentation

------------------------------------------------------------------------

# 5) Submission Instructions

-   Compress all required files into ONE ZIP file
-   Upload ZIP to eClass before deadline
-   Upload demo video as instructed on eClass

Late submissions follow course policy.

------------------------------------------------------------------------

# 6) Questions

If you have questions:

-   Post them on the eClass forum
-   Check existing posts before asking
-   Do not wait until the last day

Clear communication is part of professional software development.

------------------------------------------------------------------------

# Final Reminder

This project is designed to test:

-   Your understanding of distributed systems
-   Your concurrency skills in Go
-   Your ability to design reliable systems
-   Your ability to explain your technical decisions clearly

Good luck.
