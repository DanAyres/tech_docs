# Minikube

Official [documentation](https://minikube.sigs.k8s.io/docs/start/)

kubectl is required to interact with the kubernetes cluster. Installation [instructions are here](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/#install-kubectl-binary-with-curl-on-linux).
Basically, you download a binary and install:
```shell
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```

Minikube can be [removed](https://minikube.sigs.k8s.io/docs/commands/delete/) from the system using 
```shell
minikube delete --all --purge
```

## Setting a private registry

- Official documentation but does not consider self-signed certs: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/
- A blog post which describes our situation (harbour with self-signed certs): https://theithollow.com/2020/03/03/use-a-private-registry-with-kubernetes/
- 

1) Login to the registry:
    ```shell
    docker login 172.16.6.132
    ```
2) Show the credentials:
    ```shell
    cat /home/dan/.docker/config.json
    ```
3) Setup a new secret in kubernetes
    ```shell
    kubectl create secret generic regcred \
        --from-file=.dockerconfigjson=/home/dan/.docker/config.json \
        --type=kubernetes.io/dockerconfigjson
    ```
4) Inspect the secret
    ```shell
    kubectl get secret regcred --output=yaml
    ```
    and running
    ```shell
    kubectl get secret regcred --output="jsonpath={.data.\.dockerconfigjson}" | base64 --decode
    ```
    should yield the contents of "/home/dan/.docker/config.json"

5) Since the registry uses a self-signed certificate, we will also need to add this to the trusted certificates of the 
docker client running within the minikube container. To do this, create a folder at the following location
   ```shell
    midir -p ~/.minikube/files/etc/docker/certs.d/172.16.6.132/
    ```
    then add the contents of the docker certs folder from the host machine (ca.crt is all that is necessary):
    ```shell
    cp /etc/docker/certs.d/172.16.6.132/*  ~/.minikube/files/etc/docker/certs.d/172.16.6.132/
    ```
6) Deploy a cheeky pod using the registry:
    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: private-reg
    spec:
      containers:
      - name: cenotos8.1
        image: 172.16.6.132/python/36:centos8.1
      imagePullSecrets:
      - name: regcred
    ```
