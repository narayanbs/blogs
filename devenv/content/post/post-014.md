---
title: "Python Project Setup"
date: 2022-05-03T08:29:03+05:30
publishdate: 2022-05-03
lastmod: 2022-05-03

tags: [setup]
categories: [setup]
---
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
##### Project Setup using venv and pip
```bash
$ mkdir myproject

# create pyproject.toml (useful for black formatter configuration)
$ touch pyproject.toml 
# create virtual environment and activate it
$ python -m venv my_env
$ source my_env/bin/activate

# install dependencies (there is no dev or prod in pip)
$ pip install black flake8-bugbear mypy isort
```
create or reuse the following config files in the project directory

##### .editorconfig
```
root = true

[*]
charset = utf-8
end_of_line = lf
indent_style = space
indent_size = 2
trim_trailing_whitespace = true
insert_final_newline = true
max_line_length = 100

[*.py]
indent_size = 4

[ext/*.{c,cpp,java,h}]
indent_style = tab
indent_size = 4

[**.min.js]
indent_style = ignore
insert_final_newline = ignore

[*.{md,Rmd,rst}]
trim_trailing_whitespace = false
```

for configuring black we can use pyproject.toml
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


# sftp configuration file
sftp-config.json

# Package control specific files Package
Control.last-run
Control.ca-list
Control.ca-bundle
Control.system-ca-bundle
GitHub.sublime-settings

# Visual Studio Code #
.vscode/*
!.vscode/settings.json
!.vscode/tasks.json
!.vscode/launch.json
!.vscode/extensions.json
.history

```
