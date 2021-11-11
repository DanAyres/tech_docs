Firewalld [cheatsheet](https://www.liquidweb.com/kb/an-introduction-to-firewalld/)

List all the firewall rules:
```shell
sudo firewall-cmd --list-all
```

Saving changes to rules
```shell
sudo firewall-cmd --reload
```


Add a port for TCP/UDP
```shell
sudo firewall-cmd --zone=public --permanent --add-port=8000/tcp
```