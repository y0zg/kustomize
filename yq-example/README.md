# Reading YAML values

```
yq r pod.yaml "spec.containers[0].env[0].value"
postgres://db_url:5432
```

# Changing YAML values

```
yq w pod.yaml "spec.containers[0].env[0].value" "postgres://prod:5432"
apiVersion: v1
kind: Pod
metadata:
  name: test-pod
spec:
  containers:
    - name: test-container
      image: k8s.gcr.io/busybox
      env:
        - name: DB_URL
          value: postgres://prod:5432
```

# Merging YAML files

```
yq m --append pod.yaml envoy-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: test-pod
spec:
  containers:
    - name: test-container
      image: k8s.gcr.io/busybox
      env:
        - name: DB_URL
          value: postgres://db_url:5432
    - name: proxy-container
      image: envoyproxy/envoy:v1.12.2
      ports:
        - containerPort: 80
```

# Limitations of `yq`?

While `yq` works great with transforming YAML, it does have a few issues of its own:

- The two YAML files are merged at the top level. For instance, you cannot add a chunk of YAML file under `.spec.containers[]`

- The order of the files matters. If you invert the order, yq keeps envoy-pod for the Pod's name in metadata.name.

- You have to tell yq explicitly when to append and overwrite values. Since those are flags that apply to the whole document, it's hard to get the granularity right.

- Even with its limitations, yq has a lot of potential for use, especially if you are working on smaller projects.
