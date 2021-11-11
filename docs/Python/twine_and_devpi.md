# Notes

- https://stefan.sofa-rockers.org/2017/11/09/getting-started-with-devpi/

- https://packaging.python.org/specifications/pypirc/?highlight=pypirc

- https://lnx.azurewebsites.net/please-enter-password-for-encrypted-keyring-when-running-python-script-on-ubuntu/

# CONFIGURING TWINE

The root CA is available here: \\it-apps\Applications\Certificates\Telesoft_Root_CA.pem

export TWINE_REPOSITORY=telesoft
export TWINE_REPOSITORY_URL=https://172.16.2.194/Telesoft/stable
export TWINE_USERNAME=dayres
export TWINE_CERT=~/sandbox/Telesoft_Root_CA.pem


To store the password in the (twine keyring)[https://twine.readthedocs.io/en/latest/index.html?highlight=keyring#keyring-support]
we first need a backend for keyring that stores/encrypts the password:
```shell
pip install keyrings.cryptfile
```
```shell
keyring set https://172.16.2.194/Telesoft/stable dayres
```
and enter the password.

## Uploading a package

```shell
twine upload  <name>.whl
```

## pypirc file
```ini
[distutils]
index-servers =
    telesoft

[telesoft]
repository = https://172.16.2.194/Telesoft/stable
username = dayres
```

## pip.conf
```ini
[global]
index-url = https://172.16.2.194/Telesoft/stable
trusted-host = pypi.org pypi.python.org ssl.tduk.com 172.16.2.194

[search]
index = https://172.16.2.194/Telesoft/stable

```