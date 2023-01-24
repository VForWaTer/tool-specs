# Parameter definitions

We define a `Parameter` to be a structured argument, which is passed to a 
[Tool](./tool.md) on runtime. The sum of all passed `Parameters` make up the 
*parameterization* or *parametrization* of a tool execution.

All tools define their `Parameters` in the `tool.yml`. This is the blueprint about
the parameter values that are acceptable or required along with specification about
value ranges. The *parameterization* is file based and defaults to 
`/in/parameters.json`. The JSON format is mandatory. 

## Missing parameterization

In case a [Tool](./tool.md) accepts only optional parameters, or no parameters 
are defined at all, the `/in/parameter.json` can be an empty file:

```json
{}
```

This is the only case, in which this file is optional and can be omitted entirely.
Libraries parsing the parameters or tools, which read the file directly need to
reflect this behavior and return an empty container. The exact data type of an
empty parameterization container depends on the implementation language.
In Python this would be an empty `dict`, in R an empty `list`.

## Parameterization vs. Data

In the semantics of [Tool](tool.md)s, there is a difference between *data*, which 
is processed by a tool and *parameters*, which configure a tool.
On the one hand this differentiation is important to reflect the meaning of 
arguments passed to generic tools, on the other hand there are implications for
reproducible workflows.

Changing the parameters of the tool results in a different analysis workflow, as
a change in parameter might in principle change the logic. Hence, a different 
parameterization describes a different analysis. 
Changing data does not change the tool logic. By definition, a tool is reproducible,
if the parameterization of a tool can be applied to other data. That means, the 
**same** analysis is run on **different** data.

From a practical perspective, if you build a tool around these tool specifications,
the tool name and content of the `/in/parameter.json` can be used to create 
checksums.


## File specification

Each `Parameter` is described in a parameter block in the `/src/tool.yml` file.
All parameters are collected as the mandatory `tools.<tool_name>.parameters` block:

```yaml
tools:
  foobar:
    parameters:
      parameter_name: Parameter
```

Refer to the section below to learn about mandatory and optional fields for `Parameter`.


## Fields

The following section will define all mandatory and optional fields of a `Parameter` entity.

### `type`

The `type` field is the only mandatory field. Each parameter needs a data-type.
Allowed data-types include:

* string
* integer
* float
* boolean
* enum
* file

#### enum

The `type=enum` field has an additional mandatory `values` field, which lists all
allowed enum values. Note that enums should be validated by a parsing library
or a library calling the tools. For the tools, enums parameters are treated like 
strings as soon as read from a `parameter.json` file.

Example

```
tool:
  foobar:
    parameters:
      my_enum:
        type: enum
        values:
          - option 1
          - option 2
          - option 3
```

#### file

The `type=file` type indicates, that the passed parameter is a path to a file. These files contain data in most cases, but may also contain more complex configurations.
The library used for parsing parameterizations should load the file into memory and pass the respective datastructure to the tool. This way, the tool is independent of specific data files. 
If this is not a possible workflow, file paths can be passed as ordinary string parameters and the parsing library will not attempt to load the file. 

There are a number of file types, which are loaded by default:

| file extension | Python |  R  |  Matlab |  NodeJS  |
| ---------------|--------|-----|---------|----------| 
| .dat  |  `numpy.array` | `vector` | `matrix`  | `number[][]` | 
|  .csv |  `pandas.DataFrame` | `data.frame` |  `matrix` |  `number[][]` |

### `description`

The `description` is a multiline comment to describe the purpose of the parameter.
For the `description` Markdown is allowed, although tool-frameworks are not required to parse it.
Descriptions are optional and can be omitted.

A mutltiline comment in YAML can be specified like:

```yaml
description: | 
    This is the first line
    This is the second line
```

### `array`

The `array` field takes a single boolean value and defaults to `array=false`. If set to `array=true` the `Parameter` is an array of the specified `type`. The array field **cannot** be combined with the `type=file` and `type=enum` fields.

### `min`

Minimum value for constraining the value range. The `min` field is only valid for `type=integer` and `type=float`. Setting a minimum value is optional and can be omitted.

### `max`

Maximum value for constraining the value range. The `max` field is only valid for `type=integer` and `type=float`. Setting a maximum value is optional and can be omitted.


### `optional`

Boolean field which defaults to `false`. If set to `optional=true`, the parameter is not required by the tool. This implies, that the tool implementation can handle a `parameter.json` in which the `Parameter` is entirely missing.

### `default`

The `default` field is of same data type as the `Parameter` itself. If a default value is set, the tool-framework is required to inject this parameter into the `parameters.json`, as the tool will treat the default like any other non-optional parameter. 

## Example

```yaml
tools:
  foobar:
    title: Dummy Tools
    parameters:
      foo_int:
        type: integer
        min: 0
        max: 10
        description: An integer between 0 and 10
      foo_data:
        type: file
      foo_str:
        type: string
        default: My default string
      foo_option:
        type: enum
        values:
          - option 1
          - option 2
          - option 3
```
