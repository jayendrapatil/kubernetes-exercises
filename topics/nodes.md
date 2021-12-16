# Nodes

<br />

### Get the nodes of the cluster

<details><summary>show</summary><p>

```bash
kubectl get nodes
# NAME           STATUS   ROLES    AGE   VERSION
# controlplane   Ready    master   62m   v1.18.0
# node01         Ready    <none>   61m   v1.18.0
```

</p></details>

<br />

### Show all labels for the node `node01`

<details><summary>show</summary><p>

```bash
kubectl get nodes node01 --show-labels
# NAME     STATUS   ROLES    AGE   VERSION   LABELS
# node01   Ready    <none>   61m   v1.18.0   accelerator=nvidia-tesla-p100,beta.kubernetes.io/arch=amd64,beta.kubernetes.io/os=linux,kubernetes.io/arch=amd64,kubernetes.io/hostname=node01,kubernetes.io/os=linux
```

</p></details>

<br />

### Label worker node `node01` with label `type=critical`

<details><summary>show</summary><p>

```bash
kubectl label node node01 type=critical
# node/node01 labeled
```

</p></details>

<br />

### Remove label `type=critical` from worker node `node01` 

<details><summary>show</summary><p>

```bash
kubectl label node node01 type-
# node/node01 labeled
```

</p></details>

### Get usage metrics such CPU and Memory of the cluster nodes

<details><summary>show</summary><p></p></details>

```bash
kubectl top nodes
```

<br />