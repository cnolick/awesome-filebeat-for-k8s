# awesome config for filebeat
-----
## RTFM

- [hint](https://www.elastic.co/guide/en/beats/filebeat/current/configuration-autodiscover-hints.html)
- [processors](https://www.elastic.co/guide/en/beats/filebeat/current/defining-processors.html)
- [k8s labels annotations](https://kubernetes.io/docs/reference/labels-annotations-taints/)
-----

## Filebeat config label

If you want to collect from your application multiline logs and use label in your manifests use `filebeat-label.yaml`. This example allows use label in your app and logs will be collect with multiline.

example app:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: java-app
  labels:
    app: java-app
spec:
  revisionHistoryLimit: 2
  replicas: 1
  selector:
    matchLabels:
      app: java-app
  template:
    metadata:
      labels:
        typelog: java #from this label read typelog in filebeat
        app: java-app
    spec:
      containers:
      - image: java-app
        name:  java-app
        resources:
          requests:
            cpu: "500m"
            memory: "550M"
          limits:
            cpu: "500m"
            memory: "550M"
```

## Filebeat config annotations

This better way for multiline collect. Just add and use `filebeat-annotations.yaml`. 

example app:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: java-app
  labels:
    app: java-app
spec:
  revisionHistoryLimit: 2
  replicas: 1
  selector:
    matchLabels:
      app: java-app
  template:
    metadata:
      labels:
        app: java-app
      annotations: #from this annotations read multiline params
        co.elastic.logs/multiline.pattern: '^\['
        co.elastic.logs/multiline.negate: true
        co.elastic.logs/multiline.match: after
        co.elastic.logs/multiline.max_lines: 500
    spec:
      containers:
      - image: java-app
        name:  java-app
        resources:
          requests:
            cpu: "500m"
            memory: "550M"
          limits:
            cpu: "500m"
            memory: "550M"
```