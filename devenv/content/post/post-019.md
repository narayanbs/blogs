---
title: "Installing and Configuring Language Servers"
date: 2022-11-20T13:26:34+05:30
publishdate: 2022-11-20
lastmod: 2022-11-20
tags: [neovim, vscode]
categories: []
---
#### Installation
There are two ways to install these servers. Globally as shown below or as part of mason installation in neovim. 
##### pyright
```bash
$ npm install -g pyright
```
##### gopls
```
$ go install golang.org/x/tools/gopls@latest
```
##### clangd
It is already installed as part of clang-llvm installation.

##### typescript
```bash
$ npm install -g typescript-language-servers
```

Make sure GOPATH (~/go/bin) is added to PATH in .bashrc

#### Installation Folders
vscode extensions
```bash
$ ~/.vscode/extensions/
```
neovim lsp servers
```bash
~/.local/share/nvim/mason/bin/
```
neovim extensions
```bash
~/.local/share/nvim/
```
