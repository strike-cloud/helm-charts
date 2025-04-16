# helm-chart

## Simplified usage of the charts

You can now create applications, workers, and cronjobs using a single field `type` in your values.yaml:

### Available types
- `app`: Application that receives traffic (exposed via ingress/service)
- `worker`: Process that does not receive traffic (e.g., reads from SQS)
- `cronjob`: Scheduled task (Kubernetes cronjob)

### Example: App that receives traffic
```yaml
live-app-template:
  appName: my-app
  type: app
  healthcheck:
    type: http
    path: /health
  resources:
    requests:
      cpu: 10m
      memory: 100M
```

### Example: Worker (does not receive traffic)
```yaml
live-app-template:
  appName: my-worker
  type: worker
  healthcheck:
    type: command
    command: pgrep -f "python -u app.py"
  resources:
    requests:
      cpu: 10m
      memory: 100M
```

### Example: Cronjob (runs every 1 minute)
```yaml
live-app-template:
  appName: my-cron
  type: cronjob
  cronjob:
    schedule: "* * * * *" # runs every 1 minute
  resources:
    requests:
      cpu: 10m
      memory: 100M
```

### Notes
- You do not need to define `service`, `ingress`, `hpa`, etc.: the chart handles it automatically based on the type.
- For cronjobs, the container will always use the default CMD/entrypoint from the Docker image.
- You can still use the default values in values.yaml if you need to customize something advanced.
- For stg-app-template usage is identical, just change the chart name.