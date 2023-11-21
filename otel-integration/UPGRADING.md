# Upgrade guidelines

These upgrade guidelines only contain instructions for version upgrades which require manual modifications on the user's side.
If the version you want to upgrade to is not listed here, then there is nothing to do for you.
Just upgrade and enjoy.

## 0.0.32 to 0.0.33

Opentelemetry Integration v0.33 has removed Kube State Metrics deployment, as the needed resource attributes and metrics are now part of OpenTelemetry Kubernetes Cluster Receiver.

We removed `metricstransform/kube-extra-metrics` processor, as it was transforming `kube-state-metrics` metrics. And added two processors `metricstransform/k8s-dashboard` and `transform/k8s-dashboard` which transforms OTEL format into the format which Coralogix Kubernetes Dashboard uses.

If you manually configured the opentelemetry-cluster-collectors metrics pipeline, you will need to add `metricstransform/k8s-dashboard` and `transform/k8s-dashboard` processors to your pipeline.

Previously, we had cluster collector's metrics pipeline that looked like this:

```
opentelemetry-cluster-collector:
   ...
        metrics:
          exporters:
            - coralogix
          processors:
            - k8sattributes
            - metricstransform/kube-extra-metrics
            - resourcedetection/env
            - resourcedetection/region
            - memory_limiter
            - batch

```

Now, the new cluster collector's metrics pipeline should look like this:

```
opentelemetry-cluster-collector:
   ...
        metrics:
          exporters:
            - coralogix
          processors:
            - k8sattributes
            - metricstransform/k8s-dashboard
            - transform/k8s-dashboard
            - resourcedetection/env
            - resourcedetection/region
            - memory_limiter
            - batch

```

If you didn't configure the metrics pipeline for opentelemetry-cluster-collector, this is not a breaking change.