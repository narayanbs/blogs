---
title: "Deploying hugo site to github pages"
date: 2022-12-20T20:26:07+05:30
publishdate: 2022-12-20
lastmod: 2022-12-20
tags: []
categories: [hugo]
---
* Create a hugo website
```bash
$ mkdir hugosite && cd hugosite
$ hugo new site devenv
$ cd devenv
$ git init
$ git submodule add https://github.com/zwbetz-gh/minimal-bootstrap-hugo-theme.git themes/minimal-bootstrap-hugo-theme
$ echo "theme = 'minimal-bootstrap-hugo-theme'" >> config.toml
```
* Add markdown content to content folder (in this case content/post/post-xxx.md)

* Create a github repository named narayanbs.github.io
-- Go to the gihub webpage and click on "New" to create the repository
-- Don't initialize the README.md and click on "Create Repository"

* Now go to the terminal and into the devenv folder. Modify config.toml so that the baseURL is `https://narayanbs.github.io`

* Now publish the site by entering the command `hugo`. This will create a website in the public folder. 

* Now do the following steps.
```bash
$ cd public
$ git init
$ git add .
$ git remote add origin https://github.com/narayanbs/narayanbs.github.io.git
$ git commit -m "devenv"
$ git push origin main
```
* Now go back to gihub and click on the repository narayanbs.github.io
* Click on settings in the top right and click on pages section in the left.
* The pages must be active. 
* If we make some changes and update our repository then we have to go to actions in the settings section and re-run the workflow. this will
republish our site


