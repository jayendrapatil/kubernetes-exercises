# [Network Policies](https://kubernetes.io/docs/concepts/services-networking/network-policies/)

<br />

### Get network policies in the default namespace

<br />

<details><summary>show</summary><p>

```bash
kubectl get networkpolicy
``` 

</p></details>

<br />

### Create three pods as per the below specs. Create a NetworkPolicy `limit-consumer` so that `consumer` pods can only be access from producer pods and not from web pods.
1. Pod named `consumer` with image `nginx`. Expose via a ClusterIP service on port 80.
2. Pod named `producer` with image `nginx`. Expose via a ClusterIP service on port 80.
3. Pod named `web` with image `nginx`. Expose via a ClusterIP service on port 80.

<br />

<details><summary>show</summary><p>

#### Create the deployments and expose as service

```bash
kubectl run consumer --image=nginx
kubectl expose pod consumer --port=80
kubectl run producer --image=nginx
kubectl expose pod producer --port=80
kubectl run web --image=nginx
kubectl expose pod web --port=80
```

#### Verify the communication

```bash
# verify if web and producer can access consumer
kubectl exec producer -- curl http://consumer:80 # success
kubectl exec web -- curl http://consumer:80 # success
```

#### Create and apply the network policy

```yaml
cat << EOF > limit-consumer.yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: limit-consumer
spec:
  podSelector:
    matchLabels:
      run: consumer # selector for the pods
  ingress: # allow ingress traffic only from producer pods
  - from:
    - podSelector: # from pods
        matchLabels: # with this label
          run: producer
EOF

kubectl apply -f limit-consumer.yaml
```

#### Verify the communication

```bash
# verify if web and producer can access consumer
kubectl exec producer -- curl http://consumer:80 # success
kubectl exec web -- curl http://consumer:80 # failure
```

</p></details> 


