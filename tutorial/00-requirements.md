# 00 -- Requirements

## 1. Install Go

This project must be implemented entirely in **Go**.

Before starting, you must install Go on your machine and verify that it
is properly configured.

------------------------------------------------------------------------

## Step 1: Check if Go is Already Installed

Open a terminal and run:

    go version

If Go is installed, you should see something similar to:

    go version go1.21.0 darwin/amd64

If you see:

    command not found: go

then Go is not installed.

------------------------------------------------------------------------

## Step 2: Install Go

### Windows

1.  Download the latest stable version from:\
    https://go.dev/dl/

2.  Choose the Windows installer (.msi file).

3.  Run the installer and follow the default installation steps.

4.  Restart your terminal.

------------------------------------------------------------------------

### macOS

**Option 1 (Recommended -- official installer):**

1.  Download the macOS package from:\
    https://go.dev/dl/
2.  Run the .pkg installer.
3.  Restart your terminal.

**Option 2 (Using Homebrew):**

    brew install go

------------------------------------------------------------------------

### Linux (Ubuntu/Debian)

**Option 1 (Using apt):**

    sudo apt update
    sudo apt install golang-go

**Option 2 (Official tarball -- recommended for latest version):**

    wget https://go.dev/dl/go1.21.0.linux-amd64.tar.gz
    sudo rm -rf /usr/local/go
    sudo tar -C /usr/local -xzf go1.21.0.linux-amd64.tar.gz

Add Go to your PATH:

    echo 'export PATH=$PATH:/usr/local/go/bin' >> ~/.bashrc
    source ~/.bashrc

------------------------------------------------------------------------

## Step 3: Verify Installation

After installation, run:

    go version

You should see the installed version.

------------------------------------------------------------------------

## Step 4: Configure Go Modules

This project uses **Go Modules**, which are enabled by default in modern
Go versions.

Verify module support:

    go env GOMOD

You do not need to manually configure GOPATH for this project.

------------------------------------------------------------------------

## Step 5: Test Go Installation

Create a test file:

    nano hello.go

Add the following code:

``` go
package main

import "fmt"

func main() {
    fmt.Println("Go is installed successfully!")
}
```

Run it:

    go run hello.go

If you see:

    Go is installed successfully!

your environment is ready.

------------------------------------------------------------------------

## Requirements Summary

You must have:

-   Go 1.20 or higher installed
-   A terminal (PowerShell, macOS Terminal, or Linux shell)
-   Basic familiarity with command-line usage

No additional software is required for this project.
