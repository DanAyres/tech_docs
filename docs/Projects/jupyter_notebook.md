The aim of this note is to describe how to configure jupyter notebook on a linux server and make it accessible on the 
local network. We will then configure a remote ipython kernel on another machine (a raspberry pi) and connect to it 
from the notebook server.

## Steps

1) Installation of python 3.8.3 on Centos 7
2) Setup a new environment for jupyter
3) Install and configure the jupyter notebook
4) Setup the remote kernel

### Step 1: Python 3.8.3

We will be using a Centos 7 server to host the jupyter notebook. The default python interpreter is 3.6.8 so we shall
build a version of 3.8.3 using the instructions [here](https://computingforgeeks.com/how-to-install-python-3-on-centos/).

- Install the following packages
    ```shell
    sudo yum -y update
    sudo yum -y groupinstall "Development Tools"
    sudo yum -y install openssl-devel bzip2-devel libffi-devel sqlite-devel
    ```
    Note the addition of `sqlite-devel`.


- Grab the python tarball, extract and cd into the decompressed directory
    ```shell
    wget https://www.python.org/ftp/python/3.8.3/Python-3.8.3.tgz
    tar xvf Python-3.8.3.tgz
    cd Python-3.8.3
    ```

- Configure and install
    ```shell
    ./configure --enable-optimizations
    sudo make altinstall
    ```

- Check python has installed correctly
    ```shell
    python3.8 --version
    ```

## Step 2: Setup the environment

```shell
python3.8 -m venv /path/to/jupyter_venv
source /path/to/jupyter_venv/bin/activate
```

## Step 3: Install and configure the notebook

More [detailed instructions](https://jupyter-notebook.readthedocs.io/en/stable/public_server.html), 
including how to secure using https, can be found on the jupyter-notebook website.

**Make sure the jupyter environment from step2 has been sourced.**

- Install jupyter
    ```shell
    pip install --update pip
    pip install jupyter
    ```

- Generate a config file - noting the location
    ```shell
    jupyter notebook --generate-config
    ```
    On my system, this file was written to /home/dan/.jupyter/jupyter_notebook_config.py.


- To get a basic http service working, add the following to jupyter_notebook_config.py:
    ```
    c.NotebookApp.ip = '<server_ip>'
    c.NotebookApp.open_browser = False
    c.NotebookApp.port = 8000
    c.NotebookApp.notebook_dir = '/path/to/mynotebooks'
    ```
    Then run the following to generate a password for your notebook
    ```shell
    jupyter notebook password
    ```

    After running the following command
    ```shell
    jupyter notebook
    ```
    you will now be able to login to the notebook using a browser on a remote machine with the following: http://<server_ip>:8000

- To run the notebook as a systemd service, create the following file
    ```shell
    sudo touch /etc/systemd/system/jupyter.service
    ```
    The edit it and add the following
    ```unit file (systemd)
    [Unit]
    Description=Jupyter Notebook
    
    [Service]
    Type=simple
    PIDFile=/run/jupyter.pid
    ExecStart=/home/dan/python_envs/jupyter_notebook/bin/jupyter-notebook --config=/home/dan/.jupyter/jupyter_notebook_config.py
    User=dan
    Group=dan
    WorkingDirectory=/home/dan/Notebooks/
    Restart=always
    RestartSec=10
    #KillMode=mixed
    
    [Install]
    WantedBy=multi-user.target
    ```
    To start the notebook service:
    ```shell
    sudo systemctl daemon-reload
    sudo systemctl restart jupyter.service
    ```
  
    To check the status of the service run the following
    ```shell
    sudo journalctl -u jupyter.service
    ```
    
    To make the service start automatically on system boot
    ```shell
    sudo systemctl enable jupyter.service
```
  

## Step 4: Remote kernel

