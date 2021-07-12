# Test registry

## Box setup

The following box has been allocated for test purposes:
IP: 172.16.6.132
ILOM: 172.16.6.47

1) Install Centos 8.1 and set a static IP,
   
    - Ignore IPv6 and turn off all unused network interfaces.
    
2) Configure yum repo
   
    - To configure the yum repo, backup and remove all the existing files in `/etc/yum.repos.d` and add the telesoft repo;
   An example can be found here: root@172.16.6.132:/etc/yum.repos.d/telesoft.repo.
    -  After adding the new repo, clear the yum cache:
        ```shell
        yum clean all
        ```
    - To check the new repos, run
        ```shell
        yum repolist
        ```    

3) Install docker and docker-compose

    - Download the latest version of each installer from [here](http://docker-release-blue-prod.s3-website-us-east-1.amazonaws.com/linux/centos/8/x86_64/stable/Packages/)
    - ```shell
      yum install --allowerasing containerd.io-1.4.6-3.1.el8.x86_64.rpm
      rpm -e podman-manpages-1.4.2-5.module_el8.1.0+237+63e26edc.noarch
      yum install --allowerasing docker-ce-20.10.7-3.el8.x86_64.rpm docker-ce-cli-20.10.7-3.el8.x86_64.rpm docker-ce-rootless-extras-20.10.7-3.el8.x86_64.rpm docker-scan-plugin-0.8.0-3.el8.x86_64.rpm
      ```
    - Enable and start docker service
    ```shell
     systemctl enable docker
     systemctl start docker
    ```
   - Install docker compose. Download a binary from [here](https://github.com/docker/compose/releases) and copy to /usr/bin/docker-compose

4) Install Harbor (Harbour)

    - Generating HTTPS certificates
    
        Useful [post](https://nodeployfriday.com/posts/self-signed-cert/)
        Harbor docs: https://goharbor.io/docs/2.3.0/install-config/configure-https/
        Another useful resource on installing harbor: https://computingforgeeks.com/how-to-install-harbor-docker-image-registry-on-centos-debian-ubuntu/
      
         Create a request configuration file as follows:
        ```ini
         [req]
         default_bits = 4096
         default_md = sha256
         distinguished_name = req_distinguished_name
         x509_extensions = v3_req
         prompt = no
         [req_distinguished_name]
         C = UK
         ST = D
         L = Blandford
         O = Telesoft
         OU = Operations
         CN = 172.16.6.132
         [v3_req]
         keyUsage = keyEncipherment, dataEncipherment
         extendedKeyUsage = serverAuth
         subjectAltName = @alt_names
         [alt_names]
         IP.1 = 172.16.6.132
         ```
         The important fields are the CN and IP.1 and should be set to the IP address of the harbor server.
    
         Generate the certificate and private key using the config file you created above:
         ```shell
         mkdir ~/certs
         oppenssl req -new -nodes -x509 -days 365 -keyout 172.16.6.132.key -out ca.crt -config req.conf
         ```
         Verify the certificate has an IP SAN by running the following command
         ```shell
         openssl x509 -in ca.crt -noout -text
         ```
         We are looking for the following lines:
         ```shell
         X509v3 Subject Alternative Name:
         IP Address:172.16.6.132
         ```
         Convert yourdomain.com.crt to yourdomain.com.cert, for use by Docker. The Docker daemon interprets .crt 
         files as CA certificates and .cert files as client certificates
         ```shell
         openssl x509 -inform PEM -in ca.crt -out 172.16.6.132.cert
         ```
      
         ```shell
         mkdir -p /etc/docker/certs.d/172.16.6.132
         cp ~/certs/172.16.6.132.cert /etc/docker/certs.d/172.16.6.132
         cp ~/certs/172.16.6.132.key /etc/docker/certs.d/172.16.6.132
         cp ~/certs/ca.crt /etc/docker/certs.d/172.16.6.132
         ```
   - Configure docker clients.
    On every docker client, 
      
    ```shell
    sudo mkdir -p /etc/docker/certs.d/172.16.6.132
    scp root@172.16.6.132:etc/docker/certs.d/172.16.6.132/ca.crt /etc/docker/certs.d/172.16.6.132
    sudo systemctl restart docker
    ```
   See also [here](https://goharbor.io/docs/1.10/working-with-projects/working-with-images/pulling-pushing-images/)
   
5) Pushing an image to the registry

   Once the docker client has been configured with the certs, you can log in and push images to the repo. To login,
   ```shell
   docker login -u <user> -p <password> 172.16.6.132
   ```


