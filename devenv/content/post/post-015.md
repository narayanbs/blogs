---
title: "Django Project Setup"
date: 2022-05-03T08:33:04+05:30
publishdate: 2022-05-03
lastmod: 2022-05-03

tags: [setup]
categories: [setup]
---
### Django Dev Setup
```
# Move to workspace
$ cd workspace

# either use poetry to create a new project skeleton
$ poetry new django_projects
    or
# create project folder and intialize poetry
$ mkdir django_projects && cd django_projects
$ poetry init

# once either of the above is done then add django library
$ poetry add django

# confirm installation of package
$ poetry run python -m django --version

# Add development packages
$ poetry add -GD black mypy flake8-bugbear
    or
$ poetry add --group --dev black mypy flake8-bugbear

# start the virtual environment
$ poetry shell
```
### Django Project skeleton
```
# if the project needs to be a seperate folder
$ django-admin startproject my_django_project
$ tree
django_projects
  |_
    my_django_project
    |___ my_django_projet
    |___ manage.py

    or

# if you want the current folder to be the main project 
$ django-admin startproject my_django_project .
$ tree
django_projects
    |
     _ manage.py
    |__ my_django_project

```
### Optional (Django rest framework && cors-headers)
```
$ poetry add djangorestframework
$ poetry add django-cors-headers
```

Open the project in vscode and follow the configuration steps mentioned in [Python Project Setup](../post-014)

### To deactivate the virtual environment 
```bash
# without shell
$ deactivate

# with shell
$ exit
```
Finally the whole workspace can be removed, simply by deleting the parent directory


