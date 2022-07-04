## C2V

C => V source code translator.

Demo Video: Translating DOOM from C to V, building and running it.

https://www.youtube.com/watch?v=6oXrz3oRoEg


### Building

```bash
v .
```
... or if you want to debug c2v behaviour:
```bash
v -d trace_verbose .
```

No dependencies other than a `clang` binary.


### Usage

```
c2v file.c
```

This will generate `file.v`.

```
c2v project
```

This will translate each C file in the `project` directory.

```
project/   ==>  project/
  a.c              a.c
  foo.c            a.v
                   foo.c
		   foo.v
```

### Wrapper generation

C2V can also generate V wrappers on top of C libraries.

```
c2v wrapper file.c
```

###  Notes

C2V is using Clang's AST to generate V. This allowed us to avoid writing a C parser.

In order to avoid LLVM dependencies/C++ complexity, C2V parses AST generated by the `clang` binary.


### Configuration
C2V supports reading from a project configuration file named `c2v.toml`, located in one of these places, in this order:
1) if C2V_CONFIG is set, it should contain the c2v.toml path.
2) the project folder (the last folder passed to c2v) + /c2v.toml
3) the project folder (the folder of the file that is last passed to c2v) + /c2v.toml

That file has the following format:
```toml
[project]
uses_sdl = true
output_dirname = "c2v_out.dir"
additional_flags = "-I. -I.. -I../.."

["info.c"]
additional_flags = "-I/some/folder/used/only/for/that/specific/file"
```

In the above:
    `uses_sdl` is a boolean, that defaults to false.
    When it is true, c2v will append the result of `sdl2-config --cflags` to `additional_flags` .
    `output_dirname` is a string, that defaults to `c2v_output`. c2v will create that folder, if it does not exist, and it will put all the translated .v files there.
    `additional_flags` is a string, that will be passed verbatim to the clang parser for each .c file. It can be used to pass additional -I flags, that are specific to your project, so that clang can find all the headers needed by that project.

Note: all these are global to the project.

The c2v.toml configuration file also supports file specific overrides, for the `additional_flags` option, just put them in their own sections,
titled as the file name.
