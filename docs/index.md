# Creating documentation pages from markdown using mkdocs-material

## Resources

https://www.mkdocs.org/

https://squidfunk.github.io/mkdocs-material/

Math formatting:
https://facelessuser.github.io/pymdown-extensions/extensions/arithmatex/#options


## Installation

1. Create a python virtual environment:
 
    ```
    python3 -m venv <docs_env_path> 
    ```
2. Activate the virtual environment

    ```
    cd <docs_env_path>
    source bin/activate
    ```
3. Install mkdocs-material, This will also include all necessary dependencies

    ```
    pip install mkdocs-material
    ```

# Development

1. source the virtual environment
```
source <path_to_venv>/bin/activate
```
2. serve the documentation locally
```
mkdocs serve
```
by default thit will serve the documentation on http://127.0.0.1:8000

# Deployment
TBD
```
mkdocs gh-deploy --force
```
