# dexec [![Build Status](https://travis-ci.org/docker-exec/dexec.svg?branch=master)](https://travis-ci.org/docker-exec/dexec)  [ ![Download](https://api.bintray.com/packages/dexec/generic/dexec/images/download.svg) ](https://bintray.com/dexec/generic/dexec/_latestVersion)

A command line utility for executing source code via the Docker Exec images. It is written in Go.

## Installation

### Bintray

Navigate to the [dexec page on Bintray](https://bintray.com/dexec/generic/dexec/_latestVersion) and download the appropriate binary for your operating system.

Unzip or untar the file and move the ```dexec``` executable to where it can be found on your PATH.

### Using Go

```sh
$ go get github.com/docker-exec/dexec
```

## Reference

These examples use a .cpp source file, but any of the supported languages can be used instead. Arguments can be passed in any order, using any style of the acceptable switch styles described.

### Pass source files to execute

Multiple source files can be passed to the compiler or interpreter as follows. The first source file's extension is used to pick the appropriate Docker Exec image, e.g. .cpp retrieves dexec/cpp from the Docker registry.

```sh
$ dexec foo.cpp
$ dexec foo.cpp bar.cpp
```

The sources are mounted individually using the default Docker mount permissions (rw) and can be specified by appending :ro or :rw to the source file.

### Pass individual arguments for build

For compiled languages, arguments can be passed to the compiler.

```sh
$ dexec foo.cpp --build-arg=-std=c++11
$ dexec foo.cpp --build-arg -std=c++11
$ dexec foo.cpp -b -std=c++11
```

### Pass arguments for execution

Arguments can be passed to the executing code. Enclose arguments with single quotes to preserve whitespace.

```sh
$ dexec foo.cpp --arg=hello --arg=world --arg='hello world'
$ dexec foo.cpp --arg hello --arg world --arg 'hello world'
$ dexec foo.cpp -a hello -a world -a 'hello world'
```

### Specify location of source files

By default, dexec assumes the sources are in the directory from which it is being invoked from. It is possible to override the working directory by passing the ```-C``` flag.

```sh
$ dexec -C /path/to/sources foo.cpp bar.cpp
```

### Include files and folders mounted in Docker container

Individual files can be mounted without being passed to the compiler, for example header files in C & C++, or input files for program execution. These can be included in the following way.

```sh
$ dexec foo.cpp --include=bar.hpp
$ dexec foo.cpp --include bar.hpp
$ dexec foo.cpp -i bar.hpp
```

In addition, a program may require read and/or write access to several files on the host system. The most efficient way to achieve this is to include a directory.

```sh
$ dexec foo.cpp --include=.
$ dexec foo.cpp --include .
$ dexec foo.cpp -i .
```

Files and directories are relative to either the current working directory, or the directory specified with the ```-C``` flag.

As with sources, included files and directories are mounted using the default Docker mount permissions (rw) and can be specified by appending :ro or :rw to the source file.

### Force dexec to pull latest version of image

Primarily for debugging purposes, the --update command triggers a ```docker pull``` of the target image before executing the code.

```sh
$ dexec foo.cpp -u
$ dexec foo.cpp --update
```

### Make executable source with shebang

```dexec``` can be used to make source code executable by adding a shebang that invokes it at the top of a source file.

The shebang is stripped out at execution time but the original source containing the shebang preserved.

```c++
#!/usr/bin/env dexec
#include <iostream>
int main() {
    std::cout << "hello world" << std::endl;
}
```

then

```sh
$ chmod +x foo.cpp
$ ./foo.cpp
```
