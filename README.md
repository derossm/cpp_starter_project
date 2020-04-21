# cpp_starter_project

[![codecov](https://codecov.io/gh/lefticus/cpp_starter_project/branch/master/graph/badge.svg)](https://codecov.io/gh/lefticus/cpp_starter_project)

[![Build Status](https://travis-ci.org/lefticus/cpp_starter_project.svg?branch=master)](https://travis-ci.org/lefticus/cpp_starter_project)

[![Build status](https://ci.appveyor.com/api/projects/status/ro4lbfoa7n0sy74c/branch/master?svg=true)](https://ci.appveyor.com/project/lefticus/cpp-starter-project/branch/master)


## Getting Started

### Use the Github template
First, click the green `Use this template` button near the top of this page.
This will take you to Github's ['Generate Repository'](https://github.com/lefticus/cpp_starter_project/generate) page. 
Fill in a repository name and short description, and click 'Create repository from template'. 
This will allow you to create a new repository in your Github account, 
prepopulated with the contents of this project. 
Now you can clone the project locally and get to work!

    $ git clone https://github.com/<user>/<your_new_repo>.git

### Remove frameworks you're not going to use
If you know you're not going to use one or more of the optional gui/graphics 
frameworks (fltk, gtkmm, imgui, etc.), you can remove them with `git rm`:

    $ git rm -r src/<unnecessary_framework>

## Necessary Dependencies

1. A C++ compiler that supports C++17 (gcc 7+, clang 6+, Visual Studio 2019)
2. [Conan](https://conan.io/) - it's recommended that you install using 
[pip](https://pip.pypa.io/en/stable/) 
3. [CMake](https://cmake.org/)
4. Other Linux dependencies. If you are using Linux, there may be other 
    dependencies you will need to install, depending on what frameworks you 
    are using. Please see the documentation for your particular framework for 
    details:
    
    - [FLTK](https://www.fltk.org/doc-1.4/index.html)
    - [GTKMM](https://www.gtkmm.org/en/documentation.html)
    - [IMGUI](https://github.com/ocornut/imgui/tree/master/docs)
    - [NANA](http://nanapro.org/en-us/documentation/)
    - [QT](https://doc.qt.io/)
    - [SDL](http://wiki.libsdl.org/FrontPage)
    - [SFML](https://www.sfml-dev.org/tutorials/2.5/compile-with-cmake.php)

## Build Instructions

Before we can build, we need to make a build directory. For these directions,
we will use an in-source build directory; if you prefer using an out-of-source
build directory, adjust these directions accordingly.

    $ mkdir build && cd build

To configure the project and write makefiles, you could use `cmake` with a
bunch of command line options. The easier option is to run cmake interactively,
with the Cmake Curses Dialog Command Line tool:  

    $ ccmake ..

Once ccmake has finished setting up, press 'c' to configure the project.
Once you have selected all the options you would like to use, you can build the 
project:

    $ cmake --build .   # build all targets

### Build using an alternate compiler

If you need to use a compiler other than the system default, you will need to 
do two things:

* Tell Conan which compiler to use, via the environment variables CC and CXX
* Tell CMake which compiler to use, via the CMake variables CMAKE_C_COMPILER 
  and CMAKE_CXX_COMPILER

Be aware that CMake will detect which compiler was used to build each of the 
Conan targets, and if it doesn't match CMAKE_C_COMPILER or CMAKE_CXX_COMPILER,
the project will fail to build.

To build using clang, you can use these commands:

    $ CC=clang CXX=clang++ ccmake -DCMAKE_C_COMPILER=clang -DCMAKE_CXX_COMPILER=clang++ ..
    $ CC=clang CXX=clang++ cmake --build .

## Troubleshooting

### Update Conan
Many problems that users have can be resolved by updating Conan, so if you are 
having any trouble with this project, you should start by doing that.

To update conan: 

    $ pip install --user --upgrade conan 

You may need to use `pip3` instead of `pip` in this command, depending on your 
platform.

### Clear Conan cache
If you continue to have trouble with your Conan dependencies, you can try 
clearing your Conan cache:

    $ conan remove -f '*'
    
The next time you run `cmake` or `cmake --build`, your Conan dependencies will
be rebuilt. If you aren't using your system's default compiler, don't forget to 
set the CC, CXX, CMAKE_C_COMPILER, and CMAKE_CXX_COMPILER variables, as 
described in the 'Build using an alternate compiler' section above.

### Identifying misconfiguration of Conan dependencies

If you have a dependency 'A' that requires a specific version of another 
dependency 'B', and your project is trying to use the wrong version of 
dependency 'B', Conan will produce warnings about this configuration error 
when you run CMake. These warnings can easily get lost between a couple 
hundred or thousand lines of output, depending on the size of your project. 

If your project has a Conan configuration error, you can use `conan info` to 
find it. `conan info` displays information about the dependency graph of your 
project, with colorized output in some terminals.

    $ cd build
    $ conan info .

In my terminal, the first couple lines of `conan info`'s output show all of the
project's configuration warnings in a bright yellow font. 

For example, the package `spdlog/1.5.0` depends on the package `fmt/6.1.2`.
If you were to modify the file `cmake/Conan.cmake` so that it requires an 
earlier version of `fmt`, such as `fmt/6.0.0`, and then run:

    $ conan remove -f '*'       # clear Conan cache
    $ rm -rf build              # clear previous CMake build
    $ mkdir build && cd build
    $ cmake ..                  # rebuild Conan dependencies
    $ conan info .

...the first line of output would be a warning that `spdlog` needs a more recent
version of `fmt`.
