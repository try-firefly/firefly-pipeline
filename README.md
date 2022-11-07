## Prerequisites

* Running docker daemon
* Lamdba function to monitor

### Instrumentation

If you have yet to do so, please follow the instructions [here](https://github.com/try-firefly/firefly-cli) to instrument your functions
and setup the metric-stream and firehose.

### Infrastructure

1. `git clone` this repo onto the server with your designated HTTPS address for monitoring
2. `cd` into the directory
3. Run the following command:

```
docker-compose up -d
```

### Server

Once you have spun up the infrastructure there will be a number of ports exposed that require routing. These ports are as follows:

* 3000 -- grafana
* 4433 -- metrics endpoint
* 4318 -- traces endpoint

If you are running a reverse proxy then please find the relevant instructions [here](https://grafana.com/tutorials/run-grafana-behind-a-proxy/) to setup grafana.

Traffic to `4433` can be directly routed to the same port on your server. Traces are emitted to your chosen HTTPS endpoint with
the following path appended `/v1/traces`; you will need to route traffic to the same port with the same path on your server.

For example, if you we were running `nginx` as a reverse proxy the server block setup for ports `4433` and `4318` could look as follows: 

```
location / {
  proxy_pass http://localhost:4433;
  proxy_http_version 1.1;
  proxy_set_header Upgrade $http_upgrade;
  proxy_set_header Connection 'upgrade';
  proxy_set_header Host $host;
  proxy_cache_bypass $http_upgrade;
}

location /v1/traces {
  proxy_pass http://localhost:4318/v1/traces;
  proxy_http_version 1.1;
  proxy_set_header Upgrade $http_upgrade;
  proxy_set_header Connection 'upgrade';
  proxy_set_header Host $host;
  proxy_cache_bypass $http_upgrade;
}
```
