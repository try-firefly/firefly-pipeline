## Prerequisites

* Running docker daemon
* Lamdba function to monitor


## Infrastructure

1. `git clone` this repo onto the server with your designated HTTPS address for monitoring
2. `cd` into the directory
3. Run the following command:

```
docker-compose up -d
```

## Server

Once you have spun up the infrastructure there will be a number of ports exposed that require routing. These ports are as follows:

* 3000 -- grafana
* 4433 -- metrics endpoint
* 4318 -- traces endpoint

If you are running a reverse proxy then please find the relevant instructions [here](https://grafana.com/tutorials/run-grafana-behind-a-proxy/) to setup grafana.
As an alternative you can also setup a bespoke subdomain for grafana, this way you only have to handle traffic to one path. For example if you were using `nginx`
your server block could simply be:

```
location / {
  proxy_pass http://localhost:3000;
  proxy_http_version 1.1;
  proxy_set_header Upgrade $http_upgrade;
  proxy_set_header Connection 'upgrade';
  proxy_set_header Host $host;
  proxy_cache_bypass $http_upgrade;
}
```

Trace data is emitted to your chosen endpoint with the following path appended `/v1/traces`; you will need to route this traffic to port `4318` with the same path.
Metric data will be emitted to the same endpoint with no path appended, this traffic can be routed to port `4433` with no path. If you chose to use a bespoke subdomain
for your grafana setup, an example `nginx` server block setup for ports `4433` and `4318` can be seen below.

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
