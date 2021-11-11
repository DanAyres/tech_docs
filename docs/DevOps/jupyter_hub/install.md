pythhttps://canthonyscott.com/deploying-jupyterhub-on-a-mulit-user-centos-server/

https://jupyterhub.readthedocs.io/en/stable/quickstart.html

https://github.com/jupyterhub/jupyterhub/wiki/Run-jupyterhub-as-a-system-service

## Troubleshoot

kill any processes for the proxy
```shell
ps -ef | grep configurable-http-proxy
```

Cannot log in for a second time: https://github.com/jupyterhub/jupyterhub/issues/486

## Config

```python
c.JupyterHub.ip = '172.16.6.132'
c.JupyterHub.port = 4443
c.JupyterHub.ssl_key = '/root/certs/domain.key'
c.JupyterHub.ssl_cert = '/root/certs/domain.cert'
c.Authenticator.admin_users = {'dan'}
c.PAMAuthenticator.open_sessions = False  # Required for PAM to work properly
```

```python

c.Spawner.notebook_dir = '~/notebooks'
c.JupyterHub.spawner_class = 'dockerspawner.SystemUserSpawner'

# Explicitly set notebook directory because we'll be mounting a host volume to
# it.  Most jupyter/docker-stacks *-notebook images run the Notebook server as
# user `jovyan`, and set the notebook directory to `/home/jovyan/work`.
# We follow the same convention.
notebook_dir = os.environ.get('DOCKER_NOTEBOOK_DIR') or '/home/jovyan/work'
c.DockerSpawner.notebook_dir = notebook_dir

# Mount the real user's Docker volume on the host to the notebook user's
# notebook directory in the container
c.DockerSpawner.volumes = { '/home/{username}/notebooks': notebook_dir }


```

https://queirozf.com/entries/jupyter-kernels-how-to-add-change-remove

/path/to/python3 -m IPython kernel install --name "ds_basic" --prefix=/usr/local