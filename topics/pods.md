# [Pod](https://kubernetes.io/docs/concepts/workloads/pods/)

 - A Kubernetes pod is a group of containers, and is the smallest unit that Kubernetes administers. 
 - Pods have a single IP address that is applied to every container within the pod.
 - Pods can have single or multiple containers.
 - Containers in a pod share the same resources such as memory and storage.
 - Remember, you CANNOT edit specifications of an existing POD other than the below.
   - spec.containers[*].image
   - spec.initContainers[*].image
   - spec.activeDeadlineSeconds
   - spec.tolerations
 -  Edit the pod for changes and a tmp file is created. Delete and Recreate the pod using the tmp file.

<br />

 - [Basics](#basics)
 - [Multi-container Pods](#multi-container-pods)
 - [Labels](#labels)
 - [Node Selector](#node-selector)
 - [Resources - Requests and limits](#resources)
 - [Annotations](#annotations)

## Basics

<br />

### Check number of pods in the default namespace

<details><summary>show</summary><p>

```bash
kubectl get pods
# OR
kubectl get po
```
</p></details>

<br />

### Create a new pod with name `nginx` and using the `nginx` image

<details><summary>show</summary><p>

```bash
kubectl run nginx --image=nginx
```

</p></details>

<br />

### Create a new pod with name `nginx-labels` and using the nginx image and labels `tier=frontend`

<details><summary>show</summary><p>

```bash
kubectl run nginx-labels --image=nginx --labels=tier=frontend
```

```bash
# verification
kubectl get pod nginx-labels --show-labels
# NAME           READY   STATUS    RESTARTS   AGE   LABELS
# nginx-labels   1/1     Running   0          16s   tier=frontend
```

</p></details>

<br />

### Create a new pod with name nginx and using the nginx image in the `alpha` namespace

<details><summary>show</summary><p>

```bash
kubectl create namespace alpha
kubectl run nginx --image=nginx --namespace=alpha
```

</p></details>

<br />

### Create a new pod `custom-nginx` using the `nginx` image and expose it on container port 8080.

<details><summary>show</summary><p>

```bash
kubectl run custom-nginx --image=nginx --port=8080
```

</p></details>

<br />

### Check which node the pod is hosted on 

<details><summary>show</summary><p>

```bash
kubectl get pods -o wide
``` 

</p></details>

<br />

### Get only the pods name

<details><summary>show</summary><p>

```bash
kubectl get pods -o name
```

</p></details>

<br />

### Delete the pod with the name nginx

<details><summary>show</summary><p>

```bash
kubectl delete pod nginx
```

</p></details>

<br />

### Delete the pod with the name nginx in the `alpha` namespace

<details><summary>show</summary><p>

```bash
kubectl delete pod nginx --namespace=alpha
```

</p></details>

<br />

### Delete the pod with name `nginx-labels` with force and no grace period

<details><summary>show</summary><p>

```bash
kubectl delete pod nginx-labels --force --grace-period=0
```

</p></details>

<br />

### Create a pod with name `nginx-file` and image nginx using pod defination file

<details><summary>show</summary><p>

```bash
kubectl run nginx-file --image=nginx --dry-run=client -o yaml > nginx-file.yaml
kubectl apply -f nginx-file.yaml
```
</p></details>

<br />

### Create a nginx pod with name nginx and copy the pod definition file to a nginx_definition.yaml file

<details><summary>show</summary><p>

```bash
kubectl run nginx --image=nginx
kubectl get nginx -o yaml > nginx_definition.yaml
```

</p></details>

<br />

### Create a `ubuntu-1` pod with image `ubuntu` with command `sleep 4800`

<details><summary>show</summary><p>

```bash
kubectl run ubuntu-1 --image=ubuntu --command sleep 4800
```

</p></details>

<br />

## Multi-container Pods

<br />

### Create a multi-container pod `multi-container-pod` with 2 containers
 - first container name `nginx` with image `nginx`
 - second container name `redis` with image `redis`

<details><summary>show</summary><p>

```yaml
cat << EOF > multi-container-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: multi-container-pod
spec:
  containers:
  - image: nginx
    name: nginx
  - image: redis
    name: redis
EOF

kubectl apply -f multi-container-pod.yaml
```

</p></details>

<br />

## [Labels](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels)

<br />

### Create pod `nginx-labels` with `nginx` image and label `name=nginx`, `tier=frontend`, `env=dev`

<details><summary>show</summary><p>

`kubectl run nginx-labels --image=nginx --labels=name=nginx,tier=frontend,env=dev,version=v1`

OR

```yaml
cat << EOF > nginx-labels.yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    env: dev
    name: nginx
    tier: frontend
    version: v1
  name: nginx-labels
spec:
  containers:
  - image: nginx
    name: nginx
EOF

kubectl apply -f nginx-labels.yaml
```

</p></details>

<br />

### Show all labels of the pod `nginx-labels`

<details><summary>show</summary><p>

```bash
kubectl get pod nginx-labels --show-labels
# NAME           READY   STATUS    RESTARTS   AGE   LABELS
# nginx-labels   1/1     Running   0          26s   env=dev,name=nginx,tier=frontend,version=v1
```

</p></details> 

<br />

### Change the labels of pod 'nginx-labels' to be `version=v2`

<details><summary>show</summary><p>

```bash
kubectl label pod nginx-labels version=v2 --overwrite

kubectl get pod nginx-labels --show-labels
# NAME           READY   STATUS    RESTARTS   AGE    LABELS
# nginx-labels   1/1     Running   0          110s   env=dev,name=nginx,tier=frontend,version=v2
```

</p></details> 

<br />

### Get the label `env` for the pods (show a column with env labels)

<details><summary>show</summary><p>

```bash
kubectl get pod -L env
# OR  
kubectl get pod --label-columns=env
```

</p></details> 

<br />

### Get only the `version=v2` pods

<details><summary>show</summary><p>

```bash
kubectl get pod -l version=v2
# OR  
kubectl get pod -l 'version in (v2)'
OR  
kubectl get pod --selector=version=v2
```

</p></details> 

<br />

### Remove the `name` label from the `nginx-labels` pod

<details><summary>show</summary><p>

```bash
kubectl label pod nginx-labels name-

kubectl get pod nginx-labels --show-labels
NAME           READY   STATUS    RESTARTS   AGE     LABELS
nginx-labels   1/1     Running   0          4m49s   env=dev,tier=frontend,version=v2
```

</p></details> 

## [Node Selector](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/)

<br />

### Create a pod `nginx-node-selector` that will be deployed to a Node that has the label 'accelerator=nvidia-tesla-p100'

<details><summary>show</summary><p>

Add the label to a node:

```bash
kubectl label nodes node01 accelerator=nvidia-tesla-p100
```

We can use the 'nodeSelector' property on the Pod YAML:

```yaml
cat << EOF > nginx-node-selector.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-node-selector
spec:
  containers:
    - name: nginx-node-selector
      image: nginx
  nodeSelector: # add this
    accelerator: nvidia-tesla-p100 # the selection label
EOF

kubectl apply -f nginx-node-selector.yaml
```

OR

Use node affinity (https://kubernetes.io/docs/tasks/configure-pod-container/assign-pods-nodes-using-node-affinity/#schedule-a-pod-using-required-node-affinity)

```yaml
cat << EOF > nginx-node-selector.yaml
apiVersion: v1
kind: Pod
metadata:
  name: affinity-pod
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: accelerator
            operator: In
            values:
            - nvidia-tesla-p100
  containers:
    - name: nginx-node-selector
      image: nginx
EOF

kubectl apply -f nginx-node-selector.yaml

```

</p></details> 

<br />

## [Annotations](https://kubernetes.io/docs/concepts/overview/working-with-objects/annotations/)

<br />

### Create pod `nginx-annotations` and Annotate it with `description='my description'` value

<br />

<details><summary>show</summary><p>

```bash
kubectl run nginx-annotations --image nginx
kubectl annotate pod nginx-annotations description='my description'
```

</p></details> 

<br />

<!-- 
### Check the annotations for pod nginx-annotations

<details><summary>show</summary><p>

```bash
kubectl annotate pod nginx-annotations --list
```
  
</p></details> 

<br />
-->

### Remove the `description` annotations for pod `nginx-annotations` 

<details><summary>show</summary><p>

```bash
kubectl annotate pod nginx-annotations description-
```

</p></details> 

<br /> 

### Remove these `nginx-annotations` pod to have a clean state in your cluster

<details><summary>show</summary><p>

```bash
kubectl delete pod nginx-annotations --force
```

</p></details> 

<br />

## [Resources](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/)

<br />

### Create an nginx pod name `nginx-resources` with `requests` `cpu=100m,memory=256Mi` and `limits` `cpu=200m,memory=512Mi`

<br />

<details><summary>show</summary><p>

```bash
kubectl run nginx-resources --image=nginx --restart=Never --requests='cpu=100m,memory=256Mi' --limits='cpu=200m,memory=512Mi'
```

OR 

```yaml
cat << EOF > nginx-resources.yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx-resources
  name: nginx-resources
spec:
  containers:
  - image: nginx
    name: nginx-resources
    resources:
      limits:
        cpu: 200m
        memory: 512Mi
      requests:
        cpu: 100m
        memory: 256Mi
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
EOF

kubectl apply -f nginx-resources.yaml
```

</p>
</details>

<br />

### Clean up 

<br />

```bash
rm nginx-labels.yaml nginx-file.yaml nginx_definition.yaml nginx-resources.yaml
kubeclt delete pod nginx nginx-labels custom-nginx nginx-file ubuntu-1 nginx-node-selector nginx-annotations nginx-resources --force --grace-period=0
kubeclt delete pod nginx -n alpha --force --grace-period=0
kubectl delete namespace alpha
```