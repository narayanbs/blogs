---
title: "Python Project Setup and Dev Config"
date: 2022-05-03T08:29:03+05:30
publishdate: 2022-05-03
lastmod: 2022-05-03

tags: [setup]
categories: [setup]
---
##### Project setup using pyenv and pip
```bash
$ mkdir -p pydjango/{pydjango,tests} && cd pydjango
$ touch pydjango/__init__.py tests/__init__.py pyproject.toml README.md

	├── pydjango
	│   └── __init__.py
	├── pyproject.toml
	├── README.md
	└── tests
	    └── __init__.py

# Specify a python version using pyenv
$ pyenv local 3.11.0

# create virtual environment and activate
$ python -m venv myenv
$ source myenv/bin/activate

# Install dependencies
$ pip install django~=4.0 black flake8-bugbear isort mypy pytest

# Create test files inside tests
# Run test using pytest
$ pytest -v -s

Note: To create a requirements.txt using pip, use the following command
$ pip freeze > requirements.txt
```

##### Project setup using pyenv and poetry
```bash
# specify the python interpreter
$ pyenv local 3.9.0

$ poetry new myproject
$ cd myproject
    or

# if you already have a working folder, say myproject then you can
# initialize the project using poetry (interactive initialization)
$ cd myproject && poetry init

# Add dev dependencies
$ poetry add --group=dev black mypy flake8-bugbear isort

$ Add prod dependencies
poetry add ...

# enable virtual environment
$ poetry shell 
    or
$ source .venv/bin/activate 

# to deactivate
$ exit
    or
$ deactivate

# Open the project in VSCode
$ code .
```
create or reuse the following config files in the project directory

For configuring black we use pyproject.toml
##### pyproject.toml
```
[tool.black]
line-length = 100

[tool.isort]
profile = "black"
```
##### .flake8 configuration
```
[flake8]
max-line-length = 100
ignore = E203, E266, E501, W503, F403
exclude = .git,__pycache__,build,dist,.eggs,postgres
```

##### .gitignore
```
__pycache__
*.py[cod]
*$py.class

# Distribution / packaging
.Python build/
develop-eggs/
dist/
downloads/
eggs/
.eggs/
lib/
lib64/
parts/
sdist/
var/
wheels/
*.egg-info/
.installed.cfg
*.egg
*.manifest
*.spec

# Log files
pip-log.txt
pip-delete-this-directory.txt
*.log

# Unit test / coverage reports
htmlcov/
.tox/
.coverage
.coverage.*
.cache
.pytest_cache/
nosetests.xml
coverage.xml
*.cover
.hypothesis/

# Translations
*.mo
*.pot

# PyBuilder
target/

# Jupyter Notebook
.ipynb_checkpoints

# IPython
profile_default/
ipython_config.py

# pyenv
.python-version

# pyflow
__pypackages__/

# Environment
.env
.venv
env/
venv/
ENV/

# Visual Studio Code #
.vscode/*
!.vscode/settings.json
!.vscode/tasks.json
!.vscode/launch.json
!.vscode/extensions.json
.history

```
