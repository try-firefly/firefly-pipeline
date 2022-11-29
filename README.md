## Prerequisites

- Running docker daemon
- Server accessible via an HTTPS endpoint

## Server setup

Firefly requires you to self-host the pipeline infrastructure. You will therefore need a server with an HTTPS enpoint in which telemetry data can be sent to. Some popular providers include:

- [AWS](https://aws.amazon.com/ec2/getting-started/)
- [Digital Ocean](https://docs.digitalocean.com/products/getting-started/)

Once you have a running server with an HTTPS endpoint, some routing configuration is required to receive telemetry data. The pipeline infrastructure exposes the following ports:

- `3000` - grafana (dashboards)
- `4433` - metrics endpoint
- `4318` - traces endpoint

If you are running a reverse proxy then please find the relevant instructions [here](https://grafana.com/tutorials/run-grafana-behind-a-proxy/) to setup grafana.
As an alternative you can also setup a bespoke subdomain for grafana. For example, if you your endpoint was `https://example.io`, your grafana subdomain could be `https://grafana.example.io`. This way you only have to handle traffic to one path. So, if you were using `nginx` as your reverse proxy, your server block could simply be:

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

Trace data is emitted to your chosen endpoint with the following path appended `/v1/traces`; you will need to route this traffic to port `4318` with the same path. Metric data will be emitted to the same endpoint with no path appended, this traffic can be routed to port `4433` with no path. If you chose to use a bespoke subdomain for your grafana setup, an example `nginx` server block setup for ports `4433` and `4318` can be seen below.

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

## Install pipeline infrastructure

1. `git clone` this repo onto your server
2. `cd` into the directory
3. Run the following command:

```
docker-compose up -d
```

You should now have several docker containers running on your machine ready to receive telemetry data. To continue with the setup please run the [firefly cli](https://github.com/try-firefly/firefly-cli).
