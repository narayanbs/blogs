---
title: "OpenGL"
date: 2022-04-06T08:18:49+05:30

---

### OpenGL compilation commands with SDL and glew
```bash
g++ <file> -SDL2 -lGL -lGLEW -o <output>

ex: g++ main.cpp display.cpp shader.cpp mesh.cpp -lSDL2 -lGL -lGLEW -o main
```

### OpenGL compilation commands with glfw and glad
```bash
g++ main.cpp -Ibuild/include glad.c -lGL -lglfw -ldl -o main
```

### glfw installation
```bash
sudo apt-get install libglfw3
sudp apt-get install libglfw3-dev
```
### GLAD installation
```bash
git clone https://github.com/Dav1dde/glad.git
cd glad
cmake ./
make
sudo cp -a include /usr/local/
```
