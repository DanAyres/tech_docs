
# Internet Access when behind a firewall

Some corporate network security policies require you to authenticate before network traffic is allowed to 
and from the internet. This authentication is usually performed in a web browser which can be a problem for a headless 
machine but can be achieved by doing the following:

- Open a terminal session on the headless machine that requires internet access.
- Export the X-windows display to your workstation
    ```shell script
    export DISPLAY=<workstation ip>:0.0
    ```
    where `<workstation ip>` is the ip address of your (graphical) workstation.
- Launce firefox from the headless machine
    ```shell script
    firefox
    ```
    The firefox browser window should launch on your workstation. Enter credentials and close the browser when authenticated.

# Using pip

To upgrade a package:
```shell script
pip install --trusted-host pypi.python.org --trusted-host pypi.org --trusted-host files.pythonhosted.org --upgrade <package>
``` 
etc.

To do-away with the `--trusted-host` flag; add the trusted hosts to the pip configuration:

1) Find where pip reads its config:
```shell script
pip config list -v
```
2) Create a pip.ini file at one of those locations

3) Add the following to the .ini file:
```ini
[global]
trusted-host = pypi.python.org
			   pypi.org
	           files.pythonhosted.org
```
