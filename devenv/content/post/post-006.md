---
title: "ML Dev Environment using pyenv and poetry"
date: 2022-04-03T12:51:01+05:30

---
### Install and configure poetry
```bash
# Install poetry via curl
curl -sSL https://raw.githubusercontent.com/python-poetry/poetry/master/get-poetry.py | python

# Add poetry to your shell
source $HOME/.poetry/env

# For tab completion in your shell, see the documentation
poetry help completions

# Configure poetry to create virtual environments inside the project's root directory
poetry config virtualenvs.in-project true
```
After installation logout and login.

With poetry installed, we can now create a python project. We will define a project catered for data science. poetry will install a virtual environment based on python 3.9.0, that we have already installed with pyenv. We will include data analytics libraries like pandas and scipy, along with jupyter notebook. In addition we will include the ML libraries scikit-learn and a specific pre-release version of tensorflow. Finally, we will specify black and flake8 for formatting and style guiding.

```bash
# Specify the python version for the local directory using pyenv
pyenv local 3.9.0

# Create a new project, and directory
poetry new introml

# Specify some libraries
cd introml
poetry add pandas numpy scipy tensorflow=2.1.0rc2 tensorflow-text matplotlib scikit-learn jupyter ipykernel

# Specify some dev libraries
poetry add --dev black flake8
```
With the above commands, we have created a pyproject.toml file as well as a poetry.lock file. poetry has installed the virtual environment for this project in introml/.venv. The pyproject.toml file looks as follows.

```bash
[tool.poetry]
name = "introml"
version = "0.1.0"
description = ""
authors = ["you@yourname.com"]

[tool.poetry.dependencies]
python = "^3.9"
pandas = "^0.25.3"
numpy = "^1.18.0"
scipy = "^1.4.1"
tensorflow = "2.1.0rc2"
tensorflow-text = "^2.0.1"
matplotlib = "^3.1.2"
scikit-learn = "^0.22"
jupyter = "^1.0.0"
ipykernel = "^5.1.3"

[tool.poetry.dev-dependencies]
pytest = "^5.2"
black = "^19.10b0"
flake8 = "^3.7.9"

[build-system]
requires = ["poetry>=0.12"]
build-backend = "poetry.masonry.api"
```
Sometimes we may get an error from a library(scipy) stating the imcompatibility of the python version >-3.9 <4.0.. 
For example, let’s say you have a local python version of 3.9. Poetry will initialize the python version as ^3.9, which means the environment is compatible with any python version greater than 3.9. But if you install a package say scipy which has a dependency of >=3.9,< 3.10, then poetry fails the installation. If this occurs, you may need to open up the pyproject.toml file and change the python version 
to >=3.9, <3.10. This behavior is valid as of version 1.1.6 and may change in the future.

In that case, change the python version, as shown below
```bash
[tool.poetry.dependencies]
python = ">=3.9,<3.10"
```
After installation the contents of my directory will be as follows
```bash
# The contents of my directory.
.python-version
introml/
    .venv/
    README.rst
    introml/
        example.py
    poetry.lock
    pyproject.toml
    tests/
```

Let's test the installation, by writing the following into example.py, inside introml folder
```bash
'''An example file that imports some of the installed modules'''

import pandas as pd
import numpy as np
import tensorflow as tf
import tensorflow_text as text
import matplotlib.pyplot as plt

if __name__ == "__main__":
    # If the modules can't be imported, the following print won't happen
    print("Successfully imported the modules!")
```

Now trying running the script from outside your virtual environment. This wont work
```bash
python introml/example.py
```

Now try running the script with your virtual environment.
```bash
poetry run python introml/example.py
```
It should work.

Try this
```bash
# Spawn a shell within your virtual environment.
poetry shell

# Try running the script again, after having spawned the shell within your virtual environment.
python introml/example.py
```
