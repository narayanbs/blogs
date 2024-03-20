---
title: "C/C++ Project Setup"
date: 2022-05-03T08:43:53+05:30
publishdate: 2022-05-03
lastmod: 2022-05-03

tags: [setup]
categories: [setup]
---
Create a workspace folder
```
mkdir workspace/ccplus/myproject
```
Generate a .clang-format file inside the folder
```
$ clang-format -style=google -dump-config > .clang-format

# Modify the following lines in .clang-format
ColumnLimit: 100
MaxEmptyLinesToKeep: 1
SeparateDefinitionBlocks: Always
```
Create a .gitignore file
```
.vscode

build/*
*.pbxuser
*.mode2v3
*.mode1v3
*.perspective
*.perspectivev3
*~.nib

#ignore private workspace stuff added by Xcode4
xcuserdata
project.xcworkspace

## generic files to ignore
*~
*.lock
*.DS_Store
*.swp
*.out

# Compiled Object files
*.slo
*.lo
*.o
*.obj

# Compiled Dynamic libraries
*.so
*.dylib
*.dll

# Fortran module files
*.mod

# Compiled Static libraries
*.lai
*.la
*.a
*.lib

# Executables
*.exe
*.out
*.app
```

