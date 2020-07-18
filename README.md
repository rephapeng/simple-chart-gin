# Simple helm chart for go application
simple helm chart for API service using golang. this chart set autoscaling and private url.

### add autoscaling config
add horizontal pod autoscaling on `deployment.yaml` for enable this feature.
```sh
---   # sparator configuration

kind: HorizontalPodAutoscaler
apiVersion: autoscaling/v2beta1
metadata:
  name: {{ include "simple-chart-gin.fullname" . }}
spec:
  maxReplicas: {{ .Values.autoMaxReplicas }}
  minReplicas: {{ .Values.autoMinReplicas }}
  scaleTargetRef:
    apiVersion: app/v1
    kind: Deployment
    name: {{ include "simple-chart-gin.fullname" . }}
  metrics: 
  - type: Resource
    resource:
      name: {{ .Values.resourceName }}
      targetAverageUtilization: {{ .Values.Treshold }}

```

### custom value 
```sh
replicaCount: 1     # set first replication count
autoMaxReplicas: 3  # set maximum autoscaling replica
autoMinReplicas: 1  # set minimum autoscaling replica
resourceName: cpu   # set matric resource for parameter autoscaling
Treshold: "70"      # set treshold usage resource


image:
  repository: <private repository>  # set your private repository
  tag: <tag-version>        # set tag version of repository
  pullPolicy: Always

spec:
  containerPort: 8080   # set container port base on go application


livenessProbe:
  httpGet:
    path: /trivia   # set healt check of container
readlinessProbe:
  httpGet:
    path: /trivia

nameOverride: ""
fullnameOverride: ""

service:
  type: ClusterIP   # set service mode ClusterIP
  port: 8080        # set port and target port of service

```

### sample command to install chart
```sh
helm install --name simpleflask ./simple-chart-flask/
```