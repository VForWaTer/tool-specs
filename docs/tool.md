# Tool definition

## Description

We define a `Tool` to be an executable script/program inside a docker container. In order to be recognized as a `Tool`, the container has to meet a set of requirements:

1. A description of the tool, its arguments, parameters and input data has to be present in YAML format at the container location `/src/tool.yml`
2. The executable script/program has to either store results at the location `/out/` of the container, or print them to the containers StdOut.
3. A tool execution may be parameterized. The parameterization is stored at `/in/parameters.json` of the container. Additional input data may be added.

Essentially, the following folder and file structure must exist within the container:

```
/
|- in/
|  |- parameters.json
|- out/
|  |- ...
|- src/
|  |- tool.yml
|  |- run.py/.R/.m/.js
```

## File specification

The *specification* is defined in a single YAML file, located at `/src/tool.yml`. This file holds specifications about the container image itself. 
At the current state, only one field is defined and supported: `tools`.
The `tools` field contains a named struct with `Tool` specifications indexed by the `Tool` name.

The file content of the `tool.yml` has to at least include:

```yaml
tools:
  toolname: Tool 
 
  [...] 
```


## Fields

The following section will define all mandatory and optional fields of a `Tool` entity.

### `title`

The title is mandatory and should contain a descriptive, short single line title to identify a tool. 
It is recommended to use titles with less than 64 characters.

### `description`

Multiline comment to describe the purpose of a tool and needed information to call a tool properly,
as well as specificities of implementation which might be relevant for the user.
The description can be supplied as Markdown, although tool-frameworks are not required to parse 
Markdown. Thus a Markdown description might be rendered as plain text.

A multiline descriptions in YAML can be specified like:

```yaml
description: | 
  This is the first line.
  This is the second line.
```

### `version`

The version is optional, but highly recommended.
The version number has to follow a semantic verisoning pattern with either major, minor and patch version,
or be limited to only major and minor version.
Semantic version numbers can be prefixed by a small` v`.

Examples are: `1.0`, `v1.3.2`

### `parameters`

Parameters for tools are also an Entity [defined in the specification](parameter.md). 
The parameters field hold a struct of `Parameter` instances indexed by the parameter name.
An example can be found in the Example section below or on the [Parameters page](parameter.md).

### `data`

[Input data](parameter.md#Data: File specification) for a tool is defined separately from the 
parameters in an additional section of `tool.yml`.
Just like for the parameters, the input data of a tool is indexed by their names.
Data is always given to a tool as files or folders.

## Example

The following YAML file contains a sample tool specification, similar to the dummy specifications found in the different tool template containers.

```yaml
tools:
  foobar:
    title: Foo Bar
    description: A dummy tool to exemplify the YAML file
    version: 0.1
    parameters:
      foo_int: 
        type: integer
      foo_float:
        type: float
      foo_string:
        type: string
      foo_enum:
        type: enum
        values:
          - foo
          - bar
          - baz
      foo_array:
        type: integer
        array: true
    data:
      foo_matrix:
        path: /path/to/foo.dat
      foo_csv:
        path: /path/to/foo.csv
```
