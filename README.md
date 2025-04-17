# Define a TCP liveness probe 
A third type of liveness probe uses a TCP socket. With this configuration, the kubelet will attempt to open a socket to your container on the specified port. If it can establish a connection, the container is considered healthy, if it can't it is considered a failure.
```yaml
apiVersion: v1
kind: Pod
metadata:
 name: health3
 labels:
  app: v1
spec:
 containers:
  - name: health3
    image: registry.k8s.io/goproxy:0.1
    imagePullPolicy: IfNotPresent
    ports:
     - containerPort: 8080
    readinessProbe:
     tcpSocket:
      port: 8080
     initialDelaySeconds: 5
     periodSeconds: 5
    livenessProbe:
     tcpSocket:
      port: 8080
     initialDelaySeconds: 5
     periodSeconds: 5
```
As you can see, configuration for a TCP check is quite similar to an HTTP check. This example uses both readiness and liveness probes. The kubelet will run the first liveness probe 15 seconds after the container starts. This will attempt to connect to the goproxy container on port 8080. If the liveness probe fails, the container will be restarted. The kubelet will continue to run this check every 10 seconds.

In addition to the liveness probe, this configuration includes a readiness probe. The kubelet will run the first readiness probe 15 seconds after the container starts. Similar to the liveness probe, this will attempt to connect to the goproxy container on port 8080. If the probe succeeds, the Pod will be marked as ready and will receive traffic from services. If the readiness probe fails, the pod will be marked unready and will not receive traffic from any services.
```
kubectl apply -f health3.yaml
```
After 15 seconds, view Pod events to verify that liveness probes:
```
kubectl describe pod health3
```
