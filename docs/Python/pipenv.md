# Pipenv

## Setting up on debian

1) Check the version of pip and upgrade
    ```shell
    python3 -m pip --version
    python3 -m pip install --upgrade pip
    ```
2) Install pipenv into your user directory (the default directory is the system directory which will require root privileges)
    ```shell
    python3 -m pip --install --user pipenv
    ```
3) Add your user directory for the python binaries to the system path:
    ```shell
    export PATH="$PATH:$(python -m site --user-base)/bin"
    source ~/.bashrc
    ```
