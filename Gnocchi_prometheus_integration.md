To integrate the Gnocchi with Prometheus, enable the basic authentication because Prometheus only support the Basic authentication. 
To send the metrics from Prometheus to Gnocchi update the remote_write configuration in Prometheus.yaml file like below.

```yaml
remote_write:
- url: "http://urlofgnocchi/v1/prometheus/write"
  basic_auth:
    username: "gnocchi"
    password: "whatever" 
```

The /v1/prometheus/write endpoint handles the WriteRequest protobuf message.

Gnocchi maps Prometheus metrics to its data model.

For each metric sent by Prometheus, Gnocchi maintains a corresponding resource based on each job and instance pair. This resource is created with the Prometheus resource type and contains two attributes, job and instance. The metrics sent by Prometheus with this pair are attached to that resource and filled with the provided measures.
