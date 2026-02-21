# 10 - Project Evaluation and Submission Guidelines

This document explains:

-   What you must submit
-   What your report must include
-   What your demo video must show
-   How grading will be performed

Please read carefully. Missing required components will result in grade
deductions.

------------------------------------------------------------------------

# 1) Required Deliverables

You must submit **ONE ZIP file on eClass**.

Your ZIP file must include:

1.  All source code (Queue, Worker, Client)
2.  Your written report (PDF format)
3.  The demo video file (maximum 10 minutes)
4.  Any helper/testing scripts (if applicable)

All required components must be inside a single ZIP file.

------------------------------------------------------------------------

# 2) Demo Video Requirement 

You must include a demo video file inside your ZIP submission.

-   Maximum length: 10 minutes
-   The video does NOT need to be 10 minutes
-   Shorter and clear explanations are encouraged

Important:

Even though the video is worth 10 points, **if the video is missing from
the ZIP file, your project will not be marked**

------------------------------------------------------------------------

# 3) Written Report Requirements

There is no strict page limit, but the report must be clear, structured,
and technically detailed.

Your report must include the following sections:

------------------------------------------------------------------------

## 3.1 System Overview

-   Description of overall architecture
-   Explanation of Queue, Worker, and Client roles
-   
------------------------------------------------------------------------

## 3.2 Design Decisions

Clearly explain:

-   How pending jobs are stored
-   How running jobs are tracked
-   How completed jobs are recorded
-   How job state transitions occur
-   Why you chose your data structures

Avoid vague statements. Justify your decisions.

------------------------------------------------------------------------

## 3.3 Concurrency Handling

Explain:

-   How shared state is protected
-   Where and why mutex is used
-   How goroutines are structured
-   How you avoided race conditions

------------------------------------------------------------------------

## 3.4 Failure Handling Strategy

Explain:

-   What happens when a worker crashes
-   How timeouts are implemented
-   How often timeouts are checked
-   How jobs are reassigned
-   Why your approach works

------------------------------------------------------------------------

## 3.5 Testing Methodology

Describe:

-   How you tested integration
-   How you tested worker crashes
-   What evidence shows correctness
-   Issues encountered and how you fixed them

**Screenshots of logs are recommended.**

------------------------------------------------------------------------

## 3.6 Limitations

Briefly discuss:

-   What your system does NOT handle
-   Known edge cases
-   Possible improvements

------------------------------------------------------------------------

# 4) Demo Video Content Requirements

Your video must include:

-   Your face visible on camera
-   Screen recording of the system running
-   Explanation of your architecture and design decisions

The video must demonstrate:

1.  Starting the Queue server
2.  Starting at least two Workers
3.  Submitting multiple jobs
4.  Showing job distribution
5.  Killing one worker during execution
6.  Showing job reassignment after timeout
7.  Explaining how your design works

Do not simply run commands without explanation.

------------------------------------------------------------------------

# 5) Grading Scheme (100 Points)

## 1) System Functionality -- 40 Points

-   Jobs can be submitted and stored correctly
-   Workers receive and execute jobs
-   Multiple workers operate concurrently
-   Completed jobs are not reassigned
-   System runs without crashing

------------------------------------------------------------------------

## 2) Concurrency & Failure Handling -- 30 Points

-   Proper synchronization (no race conditions)
-   Safe handling of shared state
-   Worker crash does not lose jobs
-   Timeout and reassignment work correctly

------------------------------------------------------------------------

## 3) Code Quality & Design -- 10 Points

-   Clear structure (Queue / Worker / Client separation)
-   Logical organization
-   Basic error handling

------------------------------------------------------------------------

## 4) Written Report -- 10 Points

-   Clear explanation of architecture
-   Explanation of concurrency handling
-   Explanation of failure strategy
-   Discussion of limitations

------------------------------------------------------------------------

## 5) Demo Video -- 10 Points

-   Working demonstration
-   Worker crash shown
-   Clear explanation of design decisions
  
------------------------------------------------------------------------

# 6) Submission Instructions

-   Compress all required files into ONE ZIP file
-   Ensure the video file is included inside the ZIP
-   Upload the ZIP file to eClass before the deadline

Late submissions follow course policy.

------------------------------------------------------------------------

# 7) Questions

If you have questions:

-   Post them on the eClass forum
-   Check existing posts before asking
-   Do not wait until the last day

------------------------------------------------------------------------

# Final Reminder

This project evaluates:

-   Your understanding of distributed systems
-   Your concurrency skills in Go
-   Your ability to design reliable systems
-   Your ability to clearly explain technical decisions

Plan early and test thoroughly.
