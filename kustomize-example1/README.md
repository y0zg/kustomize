```
cat kustomization-v1.yaml 
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
resources:
  - pod.yaml
```


```
k kustomize .
apiVersion: v1
kind: Pod
metadata:
  name: test-pod
spec:
  containers:
  - env:
    - name: DB_URL
      value: postgres://db_url:5432
    image: k8s.gcr.io/busybox
    name: test-container
```

The kustomize patch functionality works similarly as yq merge, but the setup for kustomize is more tedious.

Also, kustomize merges the two YAML only when metadata.name is the same in both files.


```
k kustomize .
apiVersion: v1
kind: Pod
metadata:
  name: test-pod
spec:
  containers:
  - image: envoyproxy/envoy:v1.12.2
    name: proxy-container
    ports:
    - containerPort: 80
  - env:
    - name: DB_URL
      value: postgres://db_url:5432
    image: k8s.gcr.io/busybox
    name: test-container
```

```
tree .
.
├── dev
│   ├── kustomization.yaml
│   └── pod-patch-env.yaml
├── prod
│   ├── kustomization-v1.yaml
│   ├── kustomization.yaml
│   ├── pod-patch.yaml
│   └── pod.yaml
```

```
k kustomize prod
apiVersion: v1
kind: Pod
metadata:
  name: test-pod
spec:
  containers:
  - image: envoyproxy/envoy:v1.12.2
    name: proxy-container
    ports:
    - containerPort: 80
  - env:
    - name: DB_URL
      value: postgres://db_url:5432
    image: k8s.gcr.io/busybox
    name: test-container
```

```
apiVersion: v1
kind: Pod
metadata:
  name: test-pod
spec:
  containers:
  - image: envoyproxy/envoy:v1.12.2
    name: proxy-container
    ports:
    - containerPort: 80
  - env:
    - name: DB_URL
      value: postgres://dev:5432
    image: k8s.gcr.io/busybox
    name: test-container
```

