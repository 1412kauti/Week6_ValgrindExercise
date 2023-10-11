# Week6_ValgrindExercise
ENPM808X Week6 Valgrind Exercise

# Getting Started

## Clone Repository
```sh
git clone https://github.com/1412kauti/Week6_ValgrindExercise.git
cd Week6_ValgrindExercise
```
## Compiling
```sh
# Must re-run this command whenever any CMakeLists.txt file has been changed.
cmake -S ./ -B build/
```

## Building Program
```sh
# rebuild only files that are modified since the last build
cmake --build build/
# or rebuild everything from scracth
cmake --build build/ --clean-first
# to see verbose output, do:
cmake --build build/ --verbose
```
## Run program:
```sh
./build/app/shell-app
```

## Valgrind Test
```sh
valgrind --leak-check=full -s ./build/app/shell-app
```

## Output Valgrind to a .txt file
```sh
valgrind --log-file=errors.txt --leak-check=full -s ./build/app/shell-app
```
# Code changes

In file `AnalogSensor.cpp`

There is a memory leak issue because a dynamically allocated `std::vector<int>` is created using the `new` operator but is not properly de-allocated.

This can be fixed using smart pointers.

The fixed snippet is as follows:
```cpp
 std::unique_ptr<std::vector<int>> readings = std::make_unique<std::vector<int>>(mSamples, 10);
```

In file `main.cpp`

The boolean variable `terminator` is unused, this can be fixed by either assigning a value to the variable during the initialization or getting rid of the variable entirely. In this case `terminator` has been removed.

# What happens when the executable is linked statically ? Does Valgrind still detect those same bugs ?

When an executable is linked statically, the linker copies all the code of its dependencies into the executable file. This means that the executable can run independently of any shared libraries.

Valgrind can still detect the same bugs in statically linked executables as in dynamically linked executables.

# Why or Why Not ?
Valgrind uses LD_PRELOAD to inject its own code into the process being analyzed. This allows Valgrind to intercept and monitor all memory and system calls made by the program. LD_PRELOAD does not work with statically linked executables.

Valgrind also has a number of built-in suppressions for common errors in popular libraries (glibc). These suppressions prevent Valgrind from reporting false positives but these suppressions may not apply to statically linked executables as the library code is now part of the executable itself.