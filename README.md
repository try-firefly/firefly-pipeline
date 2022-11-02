## Firefly

Project description goes here

## Prerequisites

* Running docker daemon
* Lamdba function to monitor

## Setup

### Instrumentation

Please follow the instructions [here](https://github.com/try-firefly/firefly-cli) to instrument your functions
and setup the metric-stream and firehose.

### Infrastructure

1. `git clone` this repo onto the server with your designated HTTPS address for monitoring
2. `cd` into the directory
3. Run the following command:

```
docker-compose up -d
```

Firefly's infrastructure is now setup and ready to start receiving metric and trace data.

### Server

Need to put necessary server block info here.
