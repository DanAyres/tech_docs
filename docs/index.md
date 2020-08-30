# Creating documentation pages from markdown using mkdocs-material

## Resources

https://www.mkdocs.org/

https://squidfunk.github.io/mkdocs-material/

Math formatting:
https://facelessuser.github.io/pymdown-extensions/extensions/arithmatex/#options


## Installation

 1. Create a python virtual environment:
```
virtualenv 
```
2. Activate the virtual envorinment
```
cd path_to_venv
source bin/activate
```
3. Install mkdocs-material, This will also include all necessary dependancies
```
pip install mkdocs-material
```

# Deveolopment

1. source the virtual envorinment
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
