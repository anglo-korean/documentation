---
title: Python SDK
parent: SDKs
has_children: true
nav_order: 1
---

# Python SDK

The Anko Python SDK module provides a simple Anko Investor Forecasts gRPC Service client in python.

This SDK does little more wrap grpc with retry logic and authorization wrappers.

``` bash
$ pip install anko-sdk
```

## Usage

Given a valid token from https://anko-investor.com (see: Getting Started for more information), the following example will start consuming Forecasts

```python
import os
import socket

from anko import Client

c = Client(os.environ.get('ANKO_TOKEN'), socket.gethostname())

for forecast in c:
    if forecast:
        print(forecast)
```

(Here we use the current machine's hostname as a client identifier- this can be anything, really; it's useful to set in case you need to open a support ticket to help debug connections. It can even be an empty string).
