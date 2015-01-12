# EECS 281 Discussion 1

## Intro to Makefiles

```bash
g++ main.cpp file1.cpp file2.cpp -o main
```

Why this sucks:
* Long, prone to errors
* Not very customizable

Solution: makefile!

```bash
make
```

#### Structure of a Makefile

```makefile
all: data.o io.o main.o
    gcc data.o io.o main.o -o myexecutable
data.o: data.c data.h
    gcc -c data.c
io.o: io.c io.h
    gcc -c io.c
main.o: main.c data.h io.h common.h
    gcc -c main.c
clean:
    rm *.o myexecutable
```

Each line has a *target*, *dependencies*, and an *action* or *command*.
* **Target**: The first word before the colon. When `make` is called, the make build `all` is ran. You can type `make data.o` instead to run the process for `data.o`.
* **Dependencies**: The filenames after the colon, which are the files the command operates on and their dependencies.
* **Command**: A command or list of bash commands that are run on the dependency files.

#### Macros in Makefile

This is what macros look like (note that there is no checking of validity until the commands are run):

```makefile
OBJECTS = hello.o classname.o
CPPFLAGS = -c -Wall -Wextra -Wvla -pedantic -O3
```

#### Object files
* Pre-compiled – much faster to complie compared to executables
* If you change one part, you only need to recompile that one part from source.

