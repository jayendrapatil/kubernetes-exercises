# Logging

<br />

### Create a pod with below specs. Check the logs of the pod.

<br />

```yaml
cat << EOF > counter.yaml
apiVersion: v1
kind: Pod
metadata:
  name: counter
spec:
  containers:
  - name: count
    image: busybox
    args: [/bin/sh, -c, 'i=0; while true; do echo "$i: $(date)"; i=$((i+1)); sleep 1; done']
EOF

kubectl apply -f counter.yaml

```

<details><summary>show</summary><p>

`kubectl logs counter` OR `kubectl logs counter -f` for tailing the logs

</p></details> 

<br />

### Create a multi-container pod with below specs. Check the logs of the counter pod.

<br />

```yaml
cat << EOF > nginx-counter.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-counter
spec:
  containers:
  - name: nginx
    image: nginx
  - name: counter
    image: busybox
    args: [/bin/sh, -c, 'i=0; while true; do echo "$i: $(date)"; i=$((i+1)); sleep 1; done']
EOF

kubectl apply -f nginx-counter.yaml
```

<details><summary>show</summary><p>

`kubectl logs nginx-counter -c counter` OR `kubectl logs nginx-counter -c counter`

</p></details> 