# [Volumes](https://kubernetes.io/docs/concepts/storage/volumes/)

<br />

### Create a new pod `nginx-3` with `nginx` image and mount the configmap `db-config-1` as a volume named `db-config` and mount path `/config`

<details><summary>show</summary><p>

```yaml
cat << EOF > nginx-3.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-3
spec:
  containers:
  - image: nginx
    name: nginx-3
    volumeMounts:
      - name: db-config
        mountPath: "/config"
        readOnly: true
  volumes:
    - name: db-config
      configMap:
        name: db-config-1
EOF

kubectl apply -f nginx-3.yaml

kubectl exec nginx-4 -- cat /config/DB_HOST # verify env variables
# db.example.com
```

</p></details> 

<br />

### Create a new pod `nginx-4` with `nginx` image and mount the secret `db-secret-1` as a volume named `db-secret` and mount path `/secret`

<details><summary>show</summary><p>

```yaml
cat << EOF > nginx-4.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-4
spec:
  containers:
  - image: nginx
    name: nginx-4
    volumeMounts:
      - name: db-secret
        mountPath: "/secret"
        readOnly: true
  volumes:
    - name: db-secret
      secret:
        secretName: db-secret-1
EOF

kubectl apply -f nginx-4.yaml
```

```bash
kubectl exec nginx-4 -- cat /secret/DB_HOST  # verify env variables
# db.example.com
```

</p></details>

<br />

### Create the redis pod with `redis` image with volume `redis-storage` as ephemeral storage mounted at `/data/redis`.

```yaml
cat << EOF > redis.yaml
apiVersion: v1
kind: Pod
metadata:
  name: redis
spec:
  containers:
  - name: redis
    image: redis
    volumeMounts:
    - name: redis-storage
      mountPath: /data/redis
  volumes:
  - name: redis-storage
    emptyDir: {} # Ephemeral storage
EOF

kubectl apply -f redis.yaml
```


### Create the following
 - PV `task-pv-volume` with storage `10Mi`, Access Mode `ReadWriteOnce` on hostpath `/mnt/data`.   
 - PVC `task-pv-claim` to use the PV. 
 - Create a pod `task-pv-pod` with `nginx` image to use the PVC mounted on `/usr/share/nginx/html`

<details><summary>show</summary><p>

```yaml
cat << EOF > task-pv-volume.yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: task-pv-volume
spec:
  storageClassName: manual
  capacity:
    storage: 10Mi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/mnt/data"
EOF

kubectl apply -f task-pv-volume.yaml

kubectl get pv
# NAME             CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   REASON   AGE
# task-pv-volume   10Mi       RWO            Retain           Available           manual                  6s
```

```yaml
cat << EOF > task-pv-claim.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: task-pv-claim
spec:
  storageClassName: manual
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Mi
EOF

kubectl apply -f task-pv-claim.yaml

kubectl get pvc
#NAME            STATUS   VOLUME           CAPACITY   ACCESS MODES   STORAGECLASS   AGE
#task-pv-claim   Bound    task-pv-volume   10Mi       RWO            manual         12s
kubectl get pv # check status bound
#NAME             CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                   STORAGECLASS   REASON   AGE
#task-pv-volume   10Mi       RWO            Retain           Bound    default/task-pv-claim   manual                  64s

```

```yaml
cat << EOF > task-pv-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: task-pv-pod
spec:
  volumes:
    - name: task-pv-storage
      persistentVolumeClaim:
        claimName: task-pv-claim
  containers:
    - name: task-pv-pod
      image: nginx
      ports:
        - containerPort: 80
          name: "http-server"
      volumeMounts:
        - mountPath: "/usr/share/nginx/html"
          name: task-pv-storage
EOF

kubectl apply -f task-pv-pod.yaml
```

</p></details>

<br />

### Get the storage classes (Storage class does not belong to namespace)

<br />

<details><summary>show</summary><p>

```bash
kubectl get storageclass
# OR
kubectl get sc
```
</p></details> 

<br />

### Clean up

<details><summary>show</summary><p>

```bash
rm nginx-3.yaml nginx-4.yaml redis.yaml
kubectl delete pod task-pv-pod redis nginx-3 nginx-4 --force
kubectl delete pvc task-pv-claim
kubectl delete pv task-pv-volume
```

</p></details>

<br />

