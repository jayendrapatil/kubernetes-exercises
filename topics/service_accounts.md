# Service Account

 - A service account provides an identity for processes that run in a Pod.

<br />

### Create Service Account `sample-sa`

<details><summary>show</summary><p>

```bash
kubectl create serviceaccount sample-sa
# OR
kubectl create sa sample-sa
```

OR

```yaml
cat << EOF > sample-sa.yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: sample-sa
EOF

kubectl apply -f sample-sa.yaml
```

```bash
kubectl describe serviceaccount sample-sa # Verify, secret with token is automatically created
# Name:                sample-sa
# Namespace:           default
# Labels:              <none>
# Annotations:         <none>
# Image pull secrets:  <none>
# Mountable secrets:   sample-sa-token-p22nx
# Tokens:              sample-sa-token-p22nx
# Events:              <none>

kubectl describe secret sample-sa-token-p22nx
# Name:         sample-sa-token-p22nx
# Namespace:    default
# Labels:       <none>
# Annotations:  kubernetes.io/service-account.name: sample-sa
#               kubernetes.io/service-account.uid: 4bca7c79-7067-4114-a4c1-6a1eac7e2192

# Type:  kubernetes.io/service-account-token

# Data
# ====
# token:      eyJhbGciOiJSUzI1NiIsImtpZCI6InBEZjRWenZzWUxOdDlEVEdaRmlmX19kN0RwMF80N2JoRGxYR0lISWFvVmsifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJkZWZhdWx0Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZWNyZXQubmFtZSI6InNhbXBsZS1zYS10b2tlbi1wMjJueCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50Lm5hbWUiOiJzYW1wbGUtc2EiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC51aWQiOiI0YmNhN2M3OS03MDY3LTQxMTQtYTRjMS02YTFlYWM3ZTIxOTIiLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6ZGVmYXVsdDpzYW1wbGUtc2EifQ.NiYFRVc9M3nPGzNiE3RIsvl16ogiwP_dHXMesJmLYWV-_woLsf8aGgkO0ItuqxP6l5jncPpmLwj1xSvO8NXdrTA8PuEFh1uPr_ucdbswD2UzsOm8NB7kC7nJRqUqvLailwuRjjPjW2Ww4Ey3DTAchlDCGvTPWRPaQM1xanctAzx91-8Cdwc8-cRRjnkMhOj1zEfhLC_TQc77dkW0RMT-dA3qEF86GvxDAtUoUK9TP8JwmHWaD4hjdEyziWwij8ynIJLMrcVJcxWnak8vkVZN0QH9oENQMIkhSYdvxCCp57mcA5QXyMijbE_gSHbdTpeOz09KVpZFHkpJCAGaahej7A
# ca.crt:     1025 bytes
# namespace:  7 bytes

```

</p></details> 

<br />

### Create Service Account `sample-sa-no-auto-mount` with auto mounting disabled 

<br />

<details><summary>show</summary><p>

```yaml
cat << EOF > sample-sa-no-auto-mount.yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: sample-sa-no-auto-mount
automountServiceAccountToken: false
EOF

kubectl apply -f sample-sa-no-auto-mount.yaml
```

</p></details> 

<br />

### Create a pod with name `nginx-sa` and with image `nginx` and service account `sample-sa`

<br />

<details><summary>show</summary><p>

```bash
kubectl run nginx-sa --image=nginx --serviceaccount=sample-sa
```

OR 

```yaml
cat << EOF > nginx-sa.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-sa
spec:
  containers:
  - image: nginx
    name: nginx-sa
  serviceAccountName: sample-sa
EOF

kubectl apply -f nginx-sa.yaml
```

</p></details> 

<br />

### Clean up

```bash
rm nginx-sa.yaml sample-sa-no-auto-mount.yaml sample-sa.yaml
kubectl delete pod nginx-sa --force --grace-period=0
kubectl delete serviceaccount sample-sa-no-auto-mount sample-sa
```