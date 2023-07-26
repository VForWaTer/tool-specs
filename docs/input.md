# Input: Parameter and data definitions

Input of a tool consists of optional `Parameters` and optional `Data`.

We define a `Parameter` to be a structured argument, which is passed to a 
[Tool](./tool.md) on runtime. The sum of all passed `Parameters` make up the 
*parameterization* or *parametrization* of a tool execution.

All tools define their `Parameters` and `Data` in the `tool.yml`. This is the 
blueprint about the parameter values and input data that are acceptable or 
required along with specifications, e.g. about value ranges, default values and
data types. The actual definition of input (*parameterization* and *input data*) 
when running a tool is file based and defaults to `/in/input.json`. The JSON 
format is mandatory. 

## Missing parameterization and input data

In case a [Tool](./tool.md) accepts only optional parameters and no input data,
or no parameters and no data are defined at all, the `/in/input.json` can be 
an empty file:

```json
{}
```

This is the only case, in which this file is optional and can be omitted entirely.
Libraries parsing the parameters or tools, which read the file directly need to
reflect this behavior and return an empty container. The exact data type of an
empty parameterization container depends on the implementation language.
In Python this would be an empty `dict`, in R an empty `list`.

## Parameterization vs. Data

In the semantics of [Tools](tool.md), there is a difference between *data*, which 
is processed by a tool and *parameters*, which configure a tool.
On the one hand this differentiation is important to reflect the meaning of 
arguments passed to generic tools, on the other hand there are implications for
reproducible workflows.

Changing the parameters of the tool results in a different analysis workflow, as
a change in parameters might in principle change the logic. Hence, a different 
parameterization describes a different analysis. 
Changing data does not change the tool logic. By definition, a tool is reproducible,
if the parameterization of a tool can be applied to other data. That means, the 
**same** analysis is run on **different** data.

From a practical perspective, if you build a tool around these tool specifications,
the tool name and content of the sections `parameters` and `data` of `/in/input.json` 
can be used to create checksums and therefor help to establish reproducible workflows.


## Parameters: File specification

Each `Parameter` is described in a parameter block in the `/src/tool.yml` file.
All parameters are collected as the mandatory `tools.<tool_name>.parameters` block:

```yaml
tools:
  foobar:
    parameters:
      foo_parameter:
        [...]
```

Refer to the section below to learn about mandatory and optional fields for a `Parameter`.


### Fields

The following section defines all mandatory and optional fields of a `Parameter` entity.

#### `type`

The `type` field is the only mandatory field. Each parameter needs a data-type.
Allowed data-types include:

* string
* integer
* float
* boolean
* enum
* asset

##### `enum`

The `type=enum` field has an additional mandatory `values` field, which lists all
allowed enum values. Note that enums should be validated by a parsing library
or a library calling the tools. For the tools, enums parameters are treated like 
strings as soon as read from a `input.json` file.

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

#### `asset`

The `type=asset` can be used to specify paths to files or entire folders that are copied unchanged to the `/in/` path of the tool container and thus made available to the tool for further processing. The parsing library never attempts to load and process these files, therefore assets are available as-is in the container. Assets are neither Data nor parameters, but their dynamic nature might influence the tool execution. Hence, they are added as input to the tool.

#### `description`

The `description` is a multiline comment to describe the purpose of the parameter.
For the `description`, Markdown is allowed, although tool-frameworks are not required to parse it.
Descriptions are optional and can be omitted.

A mutltiline comment in YAML can be specified like:

```yaml
description: | 
    This is the first line
    This is the second line
```

#### `array`

The `array` field takes a single boolean value and defaults to `array=false`. If set to `array=true` the `Parameter` is an array of the specified `type`. The array field **cannot** be combined with the `type=enum` field.

#### `min`

Minimum value for constraining the value range. The `min` field is only valid for `type=integer` and `type=float`. Setting a minimum value is optional and can be omitted.  
Note that if a `max` value is additionally specified for the parameter, `min` must be lower than `max`.

#### `max`

Maximum value for constraining the value range. The `max` field is only valid for `type=integer` and `type=float`. Setting a maximum value is optional and can be omitted.  
Note that if a `min` value is additionally specified for the parameter, `max` must be higher than `min`.

#### `optional`

Boolean field which defaults to `false`. If set to `optional=true`, the parameter is not required by the tool. This implies, that the tool implementation can handle a `input.json` in which the `Parameter` is entirely missing.

#### `default`

The `default` field is of the same data type as the `Parameter` itself. If a default value is set, the tool-framework is required to inject this parameter into the `input.json`, as the tool will treat the default like any other non-optional parameter.  
Note, that default parameters are only parsed if they are not set as `optional=true`.


## Data: File specification

All input `Data` is described in a data block in the `/src/tool.yml` file.
All sets of input data are collected as the **optional** `tools.<tool_name>.data` block:

```yaml
tools:
  foobar:
    parameters:
      [...]
    data:
      foo_data:
        [...]
```

Refer to the section below to learn about mandatory and optional fields for `Data`.


### Fields

The following section defines all mandatory and optional fields of a `Data` entity.

#### `load`

This is the only **mandatory** field for an entity of `Data`.  
Boolean field which defaults to `true`. If set to `load=false`, the file is not parsed by the 
library used for parsing input. In this case, file paths are passed as ordinary strings and 
the parsing library will not attempt to load the file.

There are a number of file formats, which are loaded by default (and can be set via the `format`
field):


| file extension | Python |  R  |  Matlab |  NodeJS  |
| ---------------|--------|-----|---------|----------| 
| .dat  |  `numpy.array` | `vector` | `matrix`  | `number[][]` | 
| .csv  |  `pandas.DataFrame` | `data.frame` |  `matrix` |  `number[][]` |


Note that setting `load=false` can be helpful when developing tools that require to load the
data in a different way than it is provided by the parsing libraries.

#### `format`

By default, the file format is derived from the file extension given in the path to the data
in `input.json`. Via the `format` field, it is possible to override the file format of input 
data. This way, it can be ensured that the library used for parsing the input always loads the
file in the respective datastructure to the tool.  If the file format / extension is not 
supported by the parsing library, file paths are passed just as strings, the parsing library 
will not attempt to load the file (see above for supported formats).

```yaml
tools:
  foobar:
    parameters:
      ...
    data:
      foo_data:
        load: true
        format: .csv
```

#### `description`

The `description` is a multiline comment to describe the input data.
For the `description` Markdown is allowed, although tool-frameworks are not required to parse it.
Descriptions are optional and can be omitted.

A multiline comment in YAML can be specified like:

```yaml
description: | 
    This is the first line
    This is the second line
```


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
      foo_str:
        type: string
        default: My default string
      foo_option:
        type: enum
        values:
          - option 1
          - option 2
          - option 3
      foo_array:
        type: float
        array: true
        optional: true
        description: An optional array of floats
    data:
      foo_csv_data:
        load: true
        format: .csv
        description: |
          The parsing library will try to load the data like .csv files,
          regardless of the file extension.
      foo_nc_data:
        load: false
        description: netCDF data that is not loaded by the parsing library.    
```
