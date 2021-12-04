# TYRO

```shell
kubectl create role developer-role --verb=* --resource=services --resource=persistentvolumeclaims --resource=pods --namespace development -o yaml --dry-run > developer-role.yaml

# add the namespace in the yaml

kubectl create -f eveloper-role.yaml

kubectl create rolebinding developer-rolebinding --role=developer-role --user=drogo --namespace=development
```

```

apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN5RENDQWJDZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQ
    server: https://172.17.0.16:6443
  name: kubernetes
contexts:
- context:
    cluster: kubernetes
    namespace: development
    user: kubernetes-admin
  name: kubernetes-admin@kubernetes
- context:
    cluster: kubernetes
    namespace: development
    user: drogo
  name: drogo@kubernetes
current-context: drogo@kubernetes
The environment has expired.
preferences: {}
Please refresh to get a new environment.
- name: drogo
  user: 
    client-certificate-data: /root/drogo.crt
    client-key-data: /root/drogo.key
- name: kubernetes-admin
  user:
    client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUM4akNDQWRxZ0F3SUJBZ0lJSjBhMXFiaEw4OXd3RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1
    client-key-data: LS0tLS1CRUdJTiBSU0EgUFJJVkFURSBLRVktLS0tLQpNSUlFcEFJQkFBS0NBUUVBdUE2L2FIbU9BWWYybEkxbDByVlhGeXRHYmZyTGRDQmZsWDR2cjZKWEY5N0Npc1NJCjJSdGlRS0ZQSWVIWGdI
~ 

```

```yaml
apiVersion: v1
kind: Service
metadata:
  name: jekyll
  namespace: development
spec:
  selector:
    run: jekyll
  ports:
    - protocol: TCP
      nodePort: 30097
      port: 8080
      targetPort: 4000
  type: NodePort
```


```yaml
apiVersion: v1
kind: Pod
metadata:
  name: jekyll
  namespace: development
  labels: 
    run: jekyll
spec:
  containers:
  - name: jekyll
    image: kodekloud/jekyll-serve
    volumeMounts:
    - mountPath: /site
      name: site
  initContainers:
  - name: copy-jekyll-site
    image: kodekloud/jekyll
    command: [ "jekyll", "new", "/site" ]
    volumeMounts:
    - mountPath: /site
      name: site
  volumes:
    - name: site
      persistentVolumeClaim:
        claimName: jekyll-site
```


```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: jekyll-site
  namespace: development
spec:
  accessModes:
    - ReadWriteMany
  volumeMode: Filesystem
  resources:
    requests:
      storage: 1Gi
```

