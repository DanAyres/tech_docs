https://kubernetes.io/docs/reference/kubectl/cheatsheet/

---
# Pods

- listing pods:
```
kubectl get pods
```
- listing pods with labels:
```
kubectl get pods --show-labels
```
- deleting a pod
```
kubectl delete pod [pod_name]
```
- delete a pod by label name
```
kubectl delete pod -l [label_name]
```
- connect for debugging purposes
```
kubectl port-forward [pod_name] localhost_port:container_port
```
- dump the pod's yaml
```
kubectl get pod [pod_name] -o yaml
```
Execute a command in a pod
```
kubectl exec [pod_name] -- [CMD]
```
The "--" signals the end of the commands for kubectl and signifies that everything after it should be executed within the pod. To open a shell into a pod:
```
kubectl exec -it [pod_name] -- /bin/bash
```
Delete all Pods
```
kubectl delete po --all
```


## Pod labels

- Explicit column headings for labels
```
kubectl get pods -L creation_method,env
```
-modify pod labels
```
kubectl label pod kubia-manual creation_method=manual
```
---
# Namespace

- create a namespace
```
kubectl create namespace [namespace_name]
```
- list all namespaces
```
kubectl get ns
```
- delete the namespace
```
kubectl delete ns [namespace_name]
```

---
# Help
To get the definition of the containers keyword of a pod specification:
```
kubectl explain pods.spec.containers
```
