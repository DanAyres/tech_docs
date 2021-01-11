https://docs.docker.com/registry/deploying/
https://docs.docker.com/registry/insecure/
https://docs.docker.com/engine/reference/run/

## HTTP access

To allow docker to use an unsecure registry, modify the file /etc/docker/daemon.json

and add the following:

{
  "insecure-registries" : ["myregistrydomain.com:5000"]
}




## Using HTTPS authentication ##

https://oracle-base.com/articles/linux/docker-using-a-local-docker-registry
https://docs.docker.com/engine/security/certificates/

We assume that the docker registry host is available at the following FQDN:  docker.synology.local

1) On the machine hosting the docker registry, make a new directory called ~/certs
```
mkdir -p ~/certs
```
2) Generate a self-signed certificate using openssl:
```
openssl req \
  -newkey rsa:4096 -nodes -sha256 -keyout /volume1/docker/certs/docker.synology.local.key \
  -x509 -days 365 -out /volume1/docker/certs/docker.synology.local.crt \
  -subj "/CN=docker.synology.local/O=kw9/L=Brockenhurst/ST=Hampshire/C=GB"
```  
3) On each docker client machine create a folder in
```
/etc/docker/certs.d/docker.synology.local:1443
```
and then copy the certificates across to each client.
```
scp user@registry:~/certs/docker.synology.local.key   /etc/docker/certs.d/docker.synology.local:1443/client.key
scp user@registry:~/certs/docker.synology.local.crt   /etc/docker/certs.d/docker.synology.local:1443/client.cert
cp /etc/docker/certs.d/docker.synology.local:1443/client.cert   /etc/docker/certs.d/docker.synology.local:1443/ca.crt
```

### Debugging

curl https://synology.local:1443/v2/ --cacert /etc/docker/certs.d/synology.local:1443/ca2.crt


## Creating a new SSL certificate authority (CA)

https://deliciousbrains.com/ssl-certificate-authority-for-local-https-development/
https://unix.stackexchange.com/questions/371997/creating-a-local-ssl-certificate
https://gist.github.com/cecilemuller/9492b848eb8fe46d462abeb26656c4f8
