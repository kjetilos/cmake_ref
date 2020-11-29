CMake Programming
================
This project shows some examples of how to do cmake programming.

Building the project normally

```
mkdir build
cd build
cmake ..
```

Choosing a generator can be done using the -G command

```
cmake -G Ninja ..
ninja
```

The generator will decide which type of files are created. For instance with the Ninja generator .ninja files are created and the ninja command is used to do the build step.

We can also use the cmake command to execute the build. This will work independently of the chosen generator.

```
cmake --build .
```

CMake also generates certain targets that can be executed. Using the cmake commandline utility you can select a specific target

```
cmake --build . --target all
cmake --build . --target clean
```

The help target can be used to list all the available targets that cmake generates.

```
cmake --build . --target help
```

To see what CMake actually does underneath it's covers when it is configuring we can use the trace option.

```
cmake --trace ..
```

If you want to narrow the tracing you can use the --trace-source argument and if you want to see what values the variables expand to you can add the --trace-expand argument.

```
cmake --trace-source=CMakeLists.txt .. 
cmake --trace-source=CMakeLists.txt --trace-expand ..
```

The build step will usually produce a pretty output and hide the actual compiler steps. If you want to see all the compilation flags being used when building the binaries there are various ways of doing it. If you use the Makefile generator you can use the VERBOSE=1 argument.

```
make VERBOSE=1
```

If Ninja is used you can use the -v option

```
ninja -v
```

If you want to default to verbose output then you can use the -D option to set a special CMake configuration setting.

```
cmake -DCMAKE_VERBOSE_MAKEFILE:BOOL=ON ..
cmake --build .
```

CMake steps
------------
CMake will first run the so called "Configure step". In this step the CMakeCache.txt file will be created. The next step is the "Generate step", this is where the actual Makefile or Ninja files are being generated.

It is in the configure step where variables can be sent to CMake to enable/disable features. This is the same way as we can send flags to ./configure scripts.

Now why is this divided into two steps and not being done as a single step? This seems to be because in complex projects where you could have many configuration options you might want to edit the cache entries before generating the project files. The different CMake GUI tools lets the user separate the configuration and generation steps easily.

CMakeCache
----------
The CMake cache is a file created in the configure step. This file is called CMakeCache.txt and is placed in the build directory. The file will contain a bunch of configuration variables that are used inside the build.

We can add configuration values to the cache on the command line using the `-D<VARIABLE:TYPE>=<VALUE>` syntax. So let's say that our project has an optional demo that we can enable using the `DEMO_ENABLE` flag.

```
cmake -DDEMO_ENABLE:BOOL=1 ..
```

This cache variable could then be used in the CMakeLists.txt files to conditionally add targets for instance. To view the cache values to inspect the current state we can use the following command.

```
cmake -L -N .
```

Variables
---------
Variables are the basic unit of storage in the CMake Language. Their values are always of string type although they may be interpreted as values of other types. The set() and unset() commands are used to assign and delete variables. Variable names are case sensitive and may consist of almost any text.

Variables have a specific scope/visibility. When using set() in a command definition using function() the set() and unset() will modify variables in that function scope. The variables set() inside a function will not be visible outside the function.

Each of the directories in a source tree has its own variable bindings. Current variable bindings are copied into the new directory scope from the parent directory. A variable set() or unset() outside of a function will bind to the current directory scope.

CMake stores a set of CACHE variables or CACHE entries whose values persist in the project build tree. CACHE entries have an isolated binding scope modified only by explicit requests, such as by the CACHE option of set() and unset() commands.

Variables are search for in this order: function scope, direcotry scope, cache scope.

CMake reserves identifiers that begin with CMAKE_, _CMAKE_ or begine with _ followed by the name of any CMake command.

Environment variables are like ordinary variables in CMake. They have a global scope in a CMake process and they are never cached. Variable references have the form of $ENV{<name>}. Initial values of the CMake environment variables are those of the calling process. Values can be changed by using set() and unset() commands.

When running cmake from the command line we can assign values to variables using the `-D<VARIABLE:TYPE>=<VALUE>` syntax. In this way we can set values in the cache.

The CMake cache is sort of the place where CMake configuration values are kept. When running CMake the first time it produces a CMakeCache.txt file. After CMake has created this file you can edit the file directly.

Build Configurations
--------------------
In CMake there is a built in concept of build type. A build type will select various compiler flags and other toolchain arguments. The build type selected must be one of "Debug", "Release", "RelWithDebInfo", "MinSizeRel". You could probably make your own build types as well.

To select a build type you set the CMAKE_BUILD_TYPE cache variable. This can be done either in the CMakeCache.txt file or by using the -D option.

```
cmake -CCMAKE_BUILD_TYPE=Release ..
```

There are many per-config properties and variables that specify options depending on the CMAKE_BUILD_TYPE variable. These follow the pattern of <VARIABLE>_<CONFIG>, for instance CMAKE_C_FLAGS_RELEASE. We can use this pattern to create custom build types.

```
cmake -CCMAKE_BUILD_TYPE=Custom -DCMAKE_C_FLAGS_CUSTOM="-O1 -Wall" ..
```

Selecting Compiler and flags
----------------------------
There are at least two different ways of selecting which compiler to use. We can use environment variables when we generate the project, and the environment variables have standard names like CC and CXX.

```
CC=gcc-8 CXX=g++-8 cmake -G Ninja ..
```

Another way to set the compiler is by using the -D option to set variables like CMAKE_C_COMPILER and CMAKE_CXX_COMPILER.

```
cmake -D CMAKE_C_COMPILER=gcc-8 ..
```

Install
-------
To install a project we can use the --intall option to cmake.

```
cmake --install . --prefix "/home/me/opt"
```

References
----------
These are some good references to learn more about CMake.

https://cgold.readthedocs.io/en/latest/index.html
https://gitlab.kitware.com/cmake/community/-/wikis/FAQ
https://cmake.org/cmake/help/latest/manual/cmake-language.7.html
https://cliutils.gitlab.io/modern-cmake/
https://gist.github.com/mbinna/c61dbb39bca0e4fb7d1f73b0d66a4fd1
