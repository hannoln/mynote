

**a build system's goal :**
- **organize the magical incantations:** autumate the compilation and linking of source files into executables
- **process only what's necessary:** recompile only the changed portion of the source code,and the portion dependent on changed code
- **make maintaining the build system easier:** the build system should be a programming language that allows you to avoid redundant code x


**Makefile allows people more easily build the system**

```Makefile
CC=gcc

INCDIRS=-I.

OPT=-O0

CFLAGS=-Wall -Wextra -g $(INCDIRS) $(OPT)

CFILES=x.c y.c

OBJECTS=x.o y.o

BINARY=bin

all: $(BINARY)

$(BINARY): $(OBJECTS)

$(CC) -o $@ $^

# regular expression where % is a wildcard

%.o:%.c

$(CC) $(CFLAGS) -c -o $@ $^

clean:

rm -rf $(BINARY) $(OBJECTS)
```


**by the way,we have to know how a C program build first**
## sth about gcc



