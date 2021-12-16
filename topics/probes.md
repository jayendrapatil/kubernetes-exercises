# Readiness & Liveness Probes

## Table of Contents
1. [Readiness probes](#readiness-probes)
2. [Liveness probes](#liveness-probes)

 -  Readiness probes helps kubelet to know when a container is ready to start accepting traffic. A Pod is considered ready when all of its containers are ready
 - Liveness probes helps kubelet to know when the pod is unhealthy and needs to be restarted.

## Readiness probes

<br />

### Create a `nginx-readiness` pod with a readiness probe that just runs the http request on `/` with port `80`

<details><summary>show</summary><p>

```bash
kubectl run nginx-readiness --image=nginx --restart=Never --dry-run=client -o yaml > nginx-readiness.yaml
```

Edit `nginx-readiness.yaml` file to add `readinessProbe` probe as below and apply `kubectl apply -f nginx-readiness.yaml`

```YAML
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  containers:
  - image: nginx
    imagePullPolicy: IfNotPresent
    name: nginx
    resources: {}
    readinessProbe: # declare the readiness probe
      httpGet: # add this line
        path: / #
        port: 80 #
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

</p></details> 

<br />

## Liveness probes

<br />

### Create a `nginx-liveness` pod with a liveness probe that just runs the command 'ls'.

<details><summary>show</summary><p>

```bash
kubectl run nginx-liveness --image=nginx --restart=Never --dry-run=client -o yaml > nginx-liveness.yaml
```

Edit `nginx-liveness.yaml` file to add `livenessProbe` probe as below and apply `kubectl apply -f nginx-liveness.yaml`

```YAML
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  containers:
  - image: nginx
    imagePullPolicy: IfNotPresent
    name: nginx
    resources: {}
    livenessProbe: # add liveness probe
      exec: # add this line
        command: # command definition
        - ls # ls command
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```
</p></details> 

<br />

### Modify `nginx-liveness` pod to add a delay of 30 seconds whereas the interval between probes would be 5 seconds.

<details><summary>show</summary><p>

Edit `nginx-liveness.yaml` file to update `livenessProbe` probe as below. Delete and recreate pod using `kubectl apply -f nginx-liveness.yaml`

```YAML
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  containers:
  - image: nginx
    imagePullPolicy: IfNotPresent
    name: nginx
    resources: {}
    livenessProbe: 
      initialDelaySeconds: 30 # add this line
      periodSeconds: 5 # add this line as well
      exec:
        command:
        - ls
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

</p></details> 

<br />

### Clean up 

```bash
rm nginx-liveness.yaml nginx-readiness.yaml
kubectl delete pod nginx-readiness nginx-liveness --force
```