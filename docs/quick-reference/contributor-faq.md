Contributor FAQ
===============

The following contains answers to typical questions from contributors about OpenMS.

## General

The following section provides general information to new contributors.

### I am new to OpenMS. What should I do first?

* Check out the development version of OpenMS (see OpenMS <a href="../downloads.html#openms-releases">releases and installers</a>).
* Build OpenMS according to the installation instructions.
* Read the [OpenMS Coding Conventions](https://abibuilder.informatik.uni-tuebingen.de/archive/openms/Documentation/nightly/html/coding_conventions.html).
* Read the [OpenMS User Tutorial](../tutorials-and-quickstart-guides/openms-user-tutorial.md).
* Create a GitHub account.
* Subscribe to the [open-ms-general](https://sourceforge.net/projects/open-ms/lists/open-ms-general) 
or [contact us](../quick-reference/contact-us.md).

### What is the difference between an OpenMS tool and util?

A tool starts its lifecycle in `UTILS` and may exist without being thoroughly tested. Tools may be promoted from `UTILS`
to `TOOLS` if they are stable enough, are fully tested, fully documented, and a test workflow exists.

### I have written a class for OpenMS. What should I do?

Follow the [OpenMS coding conventions](https://abibuilder.informatik.uni-tuebingen.de/archive/openms/Documentation/nightly/html/coding_conventions.html).

Coding style (brackets, variable names, etc.) must conform to the conventions.

* The class and all the members must be documented thoroughly.
* Check your code with the tool  `tools/checker.php`. Call `php tools/checker.php` for detailed instructions.

Please open a pull request and follow the [pull request guidelines](../contribute-to-openms/pull-request-checklist.md).

## Troubleshooting

The following section provides information about how to troubleshoot common OpenMS issues.

### OpenMS complains about boost not being found

`CMake` got confused. Set up a new build directory and try again. Build from source, deleting the `CMakeCache.txt`
and `cmake` directory might help.

## Build System

The following questions are related to the build system.

### What is CMake?

`CMake` builds BuildSystems for different platforms, e.g. VisualStudio Solutions on Windows, Makefiles on Linux etc.
This allows us to define in one central location (namely `CMakeLists.txt`) how OpenMS is build and have the platform
specific stuff handled by `CMake`. View the [cmake website](http://www.cmake.org) for more information.

### How do I use CMake?

See Installation instructions for your platform.
In general, call `CMake(.exe)` with some parameters to create the native build-system.
Afterwards, (but usually) don't have to edit the current configuration using a GUI named `ccmake`
(or `CMake-GUI` in Windows), which ships with `CMake`).

```{note}
Whenever `ccmake` is mentioned in this document, substitute this by `CMake-GUI` if your OS is Windows. Edit
the `CMakeCache.txt` file directly.
```

### How do I generate a build-system for Eclipse, KDevelop, CodeBlocks etc?

Type `cmake` into a console. This will list the available code generators available on your platform, 
pass them to `CMake` using the `-G` option.

### How do I add a new class to the build system?

1. Create the new class in the corresponding sub-folder of the sub-project. The header has to be created in
   `src/<sub-project>/include/OpenMS` and the cpp file in `src/<sub-project>/source`, e.g.,
   `src/openms/include/OpenMS/FORMAT/NewFileFormat.h` and `src/openms/source/FORMAT/NewFileFormat.cpp`.
2. Add both to the respective `sources.cmake` file in the same directory (e.g., `src/openms/source/FORMAT/` and
   `src/openms/include/OpenMS/FORMAT/`).
3. Add the corresponding class test to `src/tests/class_tests/<sub-project>/`
   (e.g., `src/tests/class_tests/openms/source/NewFileFormat_test.cpp`).
4. Add the test to the `executables.cmake` file in the test folder 
   (e.g., `src/tests/class_tests/openms/executables.cmake`).
5. Add them to git by using the command `git add`.

### How do I add a new directory to the build system?

1. Create two new `sources.cmake` files (one for `src/<sub-project>/include/OpenMS/MYDIR`, one for
   `src/<sub-project>/source/MYDIR`), using existing `sources.cmake` files as template.
2. Add the new `sources.cmake` files to `src/<sub-project>/includes.cmake`
3. If you created a new directory directly under `src/openms/source`, then have a look 
   at `src/tests/class_tests/openms/executables.cmake`.
4. Add a new section that makes the unit testing system aware of the new (upcoming) tests.
5. Look at the very bottom and augment `TEST_executables`.
6. Add a new group target to `src/tests/class_tests/openms/CMakeLists.txt`.

## Debugging

The following section provides information about how to debug your code.

### How do I run a single test?

Execute an OpenMS class test using the CTest regular expressions:

```bash

$ ctest -V -R "^<class>_test"

# To build a class test, call the respective make target in ./source/TEST:

$ make <class>_test
```
To run a TOPP test, use:

```bash

$ ctest -V -R "TOPP_<tool>"
```

To build the tool, use:

```bash
$ make <tool>
```
### How do I debug uncaught exceptions?

Dump a core if an uncaught exception occurs, by setting the environment variable `OPENMS_DUMP_CORE`.

Each time an uncaught exception occurs, the `OPENMS_DUMP_CORE` variable is checked and a segmentation fault is caused,
if it is set.

### (Linux) How can I set breakpoints in gdb to debug OpenMS?

Debug the TOPPView application to stop at line 341 of SpectrumMDIWindow.C.

1. Enter the following in your terminal:

  ```bash
  Run gdb:
 shell> gdb TOPPView
```

2. Start the application (and close it):

  ```bash
 gdb> run [arguments]
```

3. Set the breakpoint:
  ```bash
 gdb> break SpectrumMDIWindow.C:341
```

4. Start the application again (with the same arguments):

  ```bash
 gdb> run
 ```

## Doxygen Documentation

### Where can I find the definition of the main page?

Find a definition of the main page [here](https://github.com/OpenMS/OpenMS/edit/develop/doc/doxygen/public/Main.doxygen).

### Where can I add a new module?

Add a new module [here](https://github.com/OpenMS/OpenMS/edit/develop/doc/doxygen/public/Modules.doxygen).


### How is the command line documentation for TOPP/UTILS tools created?

The program `OpenMS/doc/doxygen/parameters/TOPPDocumenter.cpp` creates the command line documentation for all classes
that are included in the static `ToolHandler.cpp` tools list. It can be included in the documentation using 
the following `doxygen` command:

`@verbinclude TOPP_<tool name>.cli`

Test if everything worked by calling `make doc_param_internal`. The command line documentation is written to
`OpenMS/doc/doxygen/parameters/output/`.

### What are the important files for adding a new tutorial section?

View the following OpenMS tutorials:

* `OpenMS/doc/OpenMS_tutorial/refman_overwrite.tex.in` (for PDF tutorials)
* `OpenMS/doc/doxygen/public/OpenMS_Tutorial_html.doxygen` (for html tutorials)

View the following TOPP and TOPPView tutorials:

* `OpenMS/doc/TOPP_tutorial/refman_overwrite.tex.in` (for PDF tutorials)
* `OpenMS/doc/doxygen/public/TOPP_Tutorial_html.doxygen` (for html tutorials)

## Bug Fixes

### How do I contribute to a bug fix?

To contribute to a bug fix:

1. Submit the bug as a GitHub issue.
2. Create a feature branch (e.g. `feature/fix_missing_filename_issue_615`) from your (up-to-date) 
   develop branch in your fork of OpenMS.
3. Fix the bug and add a test.
4. Create a pull request for your branch.
5. After approval and merge make sure the issue is closed.
