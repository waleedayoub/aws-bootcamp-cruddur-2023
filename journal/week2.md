# Homework

![honeycomb cruddur dashboard](honeycomb-board.png)
### 

# Week 2 — Distributed Tracing

- In order to get honeycomb to receive data from the backend, I had to hard code the API key in the docker-compose
- For some reason, even though it was set in my gitpod environment:
```shell
gitpod /workspace/aws-bootcamp-cruddur-2023 (main) $ env | grep HONEYCOMB
HONEYCOMB_API_KEY=WcriyNaMANM2VIOU9K2IrB
```
- It wasn't actually being picked up when docker-compose ran with this environment variable:
```docker
OTEL_EXPORTER_OTLP_HEADERS: "x-honeycomb-team=${HONEYCOMB_API_KEY}"
```

- In reading other people's comments in youtube, it looks like restarting the gitpod environment fixes that issue above for some reason