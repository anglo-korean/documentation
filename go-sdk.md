---
title: Go SDK
parent: SDKs
nav_order: 0
---

# Go SDK

[![GoDoc](https://img.shields.io/badge/pkg.go.dev-doc-blue)](http://pkg.go.dev/github.com/anglo-korean/anko-go-sdk)
[![Go Report Card](https://goreportcard.com/badge/github.com/anglo-korean/anko-go-sdk)](https://goreportcard.com/report/github.com/anglo-korean/anko-go-sdk)

The Anko Go SDK provides access to the Anko Investor forecasts stream over gRPC. It handles reconnection, authentication/ authorization, and

## Getting started

Initialise, if not already initialised, a go project.

```bash
$ go mod init my-anko-app
$ go get github.com/anglo-korean/anko-go-sdk
```

Create the file `main.go` with the contents:

```go
package main

import (
    "log"
    "os"
    "time"

    "github.com/anglo-korean/anko-go-sdk"
)

var Token = os.Getenv("ANKO_TOKEN")

func main() {
    anko.ConnectionTimeout = time.Second // default: 5s

    client, err := anko.New(Token, "my-client")
    if err != nil {
        panic(err)
    }

    panic(client.Handle(handler))
}

func handler(f *anko.Forecast) error {
    log.Printf("ID: %s", f.Id)
    log.Printf("Symbol: %v", f.Symbol)
    log.Printf("Label: %s", f.Label.String())
    log.Printf("Confidence Score: %.3f", f.Score)
    log.Print()

    return nil
}
```

[Generate an Anko Token](/getting-started) and export it as per:

```bash
$ export ANKO_TOKEN=anko-token
```

Where `anko-token` is the value of the token generated in the previous step.

From there, the application can be run in the usual go manner; as either:

```bash
$ go run main.go
```

or:

```bash
$ bo build
$ ./my-anko-app
```

### Forecasts

There may be very little output here, and there be very little for a while; the SDK will fail if there's a problem, and so no output is good output.


## The SDK

This SDK accepts an Anko Token and a per-connection name (which may aid debugging where a single token is used across auto-scaled services) and returns a channel of forecasts for ready consumption.

client.Handle accepts an anko.Handler function, a convention borrowed from packages such as net/http.
This function looks like:

```go
type Handler func(*anko.Forecast) error
```

This function will be called on every Forecast received from the Anko Investor Forecasts gRPC Service.
Any returned error will halt the processing of Forecasts. By default these functions are called sequentially; this is the opposite behaviour to http.HandlerFunc, where each handler runs in a new go function.

If this behaviour is important to you, then simply write your anko.Handler to provide its own sync/ dispatch/ gofunc logic.
