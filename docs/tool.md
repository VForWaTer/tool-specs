# Tool definition

## Description

We define a `Tool` to be an executable script/program inside a docker container. In order to be recognized as a `Tool`, the container has to meet a set of requirements, which are described [here](./container.md)


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

### `parameters`

Parameters for tools are also an Entity [defined in the specification](parameter.md). 
The parameters field hold a struct of `Parameter` instances indexed by the parameter name.
An example can be found in the Example section below or on the [Parameters page](./input.md#parameters-file-specification).

### `data`

[Input data](./input.md#data-file-specification) for a tool is defined separately from the 
parameters in an additional section of `tool.yml`.
Just like for the parameters, the input data of a tool is indexed by their names.
In case no further configuration is needed, data may be supplied in a single list
of dataset names.
Data has to be provided as files, which may be nested in sub-folders of `/in/`.

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
      - foo_csv:
      - foo_nc:
```
