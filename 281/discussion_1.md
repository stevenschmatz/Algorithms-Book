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
 
#### GDB

* Text-based debugging tool
* Useful for solving segfaults (program received signal SIGSEGV), or memory issues, e.g., index out of bounces
* When compiling, add -g to Makefile
* adds debugging symbols to binary
 
To start, type `gdb <exe_name>`.

#### Commands

* **(r)un**: start executable
* **(b)reak**: sets breakpoints
* **where**: prints function line where seg. fault occurred
* **(b)ack(t)race**: prints the full chain fo function calls
* **(s)tep**: executes current line of the program, enters function calls
* **(n)ext**: like step but does not enter functions
* **(c)ontinue**: continue to the next breakpoint
* **(p)rint *var***: prints the value of *var*
* **watch *var***: watch a certain variable
* **(l)ist *line_num*** : list source code near line_num
* **kill**: terminate the executable
* **quit**: quit GDB
 
## Unit Testing

* Used for testing a program
* Unit tests because small test in your program that make sure that your decomposable parts are working
* Include `<assert>` library
* `assert` that something is true, and if it is not true then the code triggers a breakpoint.

#### Example

```c++
class PowerRaiser {
public:
    PowerRaiser( unsigned int _base) : base(_base) {}
    unsigned int getBase() {
    return base};
}

class PowerRaiserTest{
public:
    runTestGetBase() {
        PowerRaiser p(10);
        assert(p.getBase() == 10);
    }
    
    runTestRaise() {
        PowerRaiser p(3);
        assert(1 == p.raise(0));
        assert(3 == p.raise(1));
    }
    
    testAll() {
        testGetBase();
        testGetPower();
    }
}
```

## C++ Input/Output

Class structure:

![Class Structure](http://www.cplusplus.com/img/iostream.gif)


`cin`: read from stdin
`cout`: write to stdout
`cerr`: write to stderr
`ifstream`: open files with *read* permission
`ofstream`: open files with *write* permission
`fstream`: open files with *read and write* permission

#### Pipelining

```
./path281 < input.txt > output.txt
```

Instead of reading from `cin`, path281 executable will now read from `input.txt` and instead of writing to `cout`, it will write to `output.txt`.

#### Read characters by character
```c++
int main() {
    while (cin.get() != -1) {
        cin.unget();
        char c = cin.get();
        cout << c;
    }
}
```

Or, faster:

```c++
int main() {
    char c;
    while ((c = cin.get()) != 1) {
        cout << c;
    }
}
```

#### Read line by line

```c++
int main() {
    string s;
    while(getline(cin, s)) {
        cout << s << endl;
    }
}
```

#### Printing Line by Line

```c++
int main() {
    for(int i = 0; i < 100; i++) {
        cout << i << endl;
    }
}
```

This is inefficient, because you have to flush to the buffer every time. When you use a newline instead, then it is universally faster because the system determines when to flush the buffer.

```c++
int main() {
    for(int i = 0; i < 100; i++) {
        cout << i << '\n';
    }
}
```

#### Using Internal Buffers

```c++
#include <sstream>
int main() {
    ostringstream ss;
    
    for(int i = 0; i < 100; i++) {
        ss << i << '\n';
    }
    
    cout << ss.str();
}
```

## Getopt

Motivation: simplifies command line parsing

```
./exec -a mergesort -V 1.0
```

In `argv`, all flags and values are treated the same way. 

Instead of coding a way to parse the command line arguments every time, GNU provided a way to do this in a standard way.

```c++
#include <unistd.h>
#include <stdio.h>

int main(int argc, char **argv) {
    int aflag = 0; int bflag = 0;
    char *cvalue = nullptr;
    int index, c;
    
    while ((c = getopt(argc, argv, "abc:")) != -1) {
        switch (c) {
            case 'a':
                // do something
                break;
            case 'b':
                // do something else
                break;
            case 'c':
                // do something with the value of c
                
                // optarg is the value of the argument after
                // the -c flag is used.
                string foo = optarg;
                
                break;
        }
    }
}
```

`"abc:"` means that an argument is required for `c`.

`optarg`: global external variable in `<stdio.h>`, representing the value for the argument, if the variable requires an argument.

```
./exec -a -b -c foo
```

Or, alternatively:

```
./exec -a -c foo -b
```

If you want two flags to have arguments, use `"a:b:c:"`. This means that you could call the executable like so:

```
./exec -a hello -b goodbye -c mergesort
```

The order of the arguments does not matter. This is only if the flag only has *one argument*. However, there is a way so that you can specify more than one argument for a flag.

#### Non-optional arguments

Use another external variable in `<stdio.h>`, the variable `optind`.

```c++
// First non-optional argument
string arg1 = argv[optind];

// Second non-optional argument
string arg2 = argv[optind + 1];
```

When you call an executable with non-optional arguments, the non-optional arguments are automatically moved to the end of the flag list, and then you can access them by indexing them at the `optind + i` position.

#### Verbose arguments

Some options can be verbose:

```c++
./exec -h
./exec --help
```

You have to structure things a bit differently for a verbose option:

```c++
struct option {
    const char * name;
    int has_arg;
    int *flag; // set to null all the time
    int *value; // takes the first letter of name and converts it to ascii.
}
```





