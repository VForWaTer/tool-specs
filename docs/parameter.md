# Parameter and data definitions

We define a `Parameter` to be a structured argument, which is passed to a 
[Tool](./tool.md) on runtime. The sum of all passed `Parameters` make up the 
*parameterization* or *parametrization* of a tool execution.

All tools define their `Parameters` and `Data` in the `tool.yml`. This is the 
blueprint about the parameter values and input data that are acceptable or 
required along with specifications, e.g. about value ranges, default values and
data types. The *parameterization* is file based and defaults to 
`/in/parameters.json`. The JSON format is mandatory. 

## Missing parameterization

In case a [Tool](./tool.md) accepts only optional parameters and no input data,
or no parameters and data are defined at all, the `/in/parameter.json` can be 
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
Input data of a tool is also defined in *tool.yml* and *parameters.json*, but in
a separate section `data`.

From a practical perspective, if you build a tool around these tool specifications,
the tool name and content of the sections `parameters` and `data` of the 
`/in/parameter.json` can be used to create checksums and therefor help to establish
reproducible workflows.


## Parameters: File specification

Each `Parameter` is described in a parameter block in the `/src/tool.yml` file.
All parameters are collected as the mandatory `tools.<tool_name>.parameters` block:

```yaml
tools:
  foobar:
    parameters:
      foo_parameters:
        ...
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
* file
* asset

##### `enum`

The `type=enum` field has an additional mandatory `values` field, which lists all
allowed enum values. Note that enums should be validated by a parsing library
or a library calling the tools. For the tools, enums parameters are treated like 
strings as soon as read from a `parameters.json` file.

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

#### `file`

The `type=file` type indicates, that the passed parameter is a path to a file. These files should not contain input data for the tool, as input data is specified in the `data` section of `tool.yml`. The type `file` can be used for more complex configurations that are stored in any kind of file format.
The library used for parsing parameterizations should load the file into memory and pass the respective datastructure to the tool. This way, the tool is independent of specific data files. 
If this is not a possible workflow, file paths can be passed as ordinary string parameters and the parsing library will not attempt to load the file. 

There are a number of file types, which are loaded by default:

| file extension | Python |  R  |  Matlab |  NodeJS  |
| ---------------|--------|-----|---------|----------| 
| .dat  |  `numpy.array` | `vector` | `matrix`  | `number[][]` | 
| .csv  |  `pandas.DataFrame` | `data.frame` |  `matrix` |  `number[][]` |

#### `asset`

The `type=asset` can be used to specify paths to files or entire folders that are copied unchanged to the `/in/` path of the tool container and thus made available to the tool for further processing. The parsing library never attempts to load and process these files, therefore the files are available in the tool exactly as they are.  
Assets can be tool configurations, geometry files or all kinds of other files.

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

The `array` field takes a single boolean value and defaults to `array=false`. If set to `array=true` the `Parameter` is an array of the specified `type`. The array field **cannot** be combined with the `type=file` and `type=enum` fields.

#### `min`

Minimum value for constraining the value range. The `min` field is only valid for `type=integer` and `type=float`. Setting a minimum value is optional and can be omitted.  
Note that if a `max` value is additionally specified for the parameter, `min` must be lower than `max`.

#### `max`

Maximum value for constraining the value range. The `max` field is only valid for `type=integer` and `type=float`. Setting a maximum value is optional and can be omitted.  
Note that if a `min` value is additionally specified for the parameter, `max` must be higher than `min`.

#### `optional`

Boolean field which defaults to `false`. If set to `optional=true`, the parameter is not required by the tool. This implies, that the tool implementation can handle a `parameters.json` in which the `Parameter` is entirely missing.

#### `default`

The `default` field is of the same data type as the `Parameter` itself. If a default value is set, the tool-framework is required to inject this parameter into the `parameters.json`, as the tool will treat the default like any other non-optional parameter.  
Note, that default parameters are only parsed if they are not set as `optional=true`.


## Data: File specification

All input `Data` is described in a data block in the `/src/tool.yml` file.
All sets of input data are collected as the mandatory `tools.<tool_name>.data` block:

```yaml
tools:
  foobar:
    parameters:
      ...
    data:
      foo_data:
        ...
```

Refer to the section below to learn about mandatory and optional fields for `Data`.


### Fields

The following section defines all mandatory and optional fields of a `Data` entity.

#### `path`

The `path` field is the only mandatory field. Each input data needs a path, as input data
is always given to the tool in a file- or folder-based way.
Just as for paramters of `type=file`, the library used for parsing parameterizations and 
data loads the file into memory and passes the respective datastructure to the tool, which 
makes the tool independent of specific data files. 
If the file format / extension is not supported by the parsing library, file paths are 
passed just as strings, the parsing library will not attempt to load the file. 

There are a number of file types, which are loaded by default:

| file extension | Python |  R  |  Matlab |  NodeJS  |
| ---------------|--------|-----|---------|----------| 
| .dat  |  `numpy.array` | `vector` | `matrix`  | `number[][]` | 
| .csv  |  `pandas.DataFrame` | `data.frame` |  `matrix` |  `number[][]` |

#### `format`

By default, the file format is derived from the file extension given in `path`. Via the
`format` field, it is possible to specify the necessary file format of input data as a 
string.
This also allows to specify a `path` to a folder instead of a file and use all of the files 
inside the folder with the specified file `format` as input data:

```yaml
tools:
  foobar:
    parameters:
      ...
    data:
      foo_data:
        path: /path/to/folder/containing/nc/files/
        format: .nc
```

#### `parse`

Boolean field which defaults to `true`. If set to `parse=false`, the file is not parsed by the 
library used for parsing parameterizations. In this case, the file is not loaded into memory 
and passed in the respective datastructure to the tool. Instead, file paths are passed as 
ordinary strings and the parsing library will not attempt to load the file.

#### `include`

Boolean field which defaults to `true`. If set to `include=false`, the file / data is not copied 
to the `/in/` folder of the container. Instead of this, the location of the locally saved file or
folder is mounted to the docker container. Through this, hashsums of the data cannot be calculated
and workflows cannot be proven to be reproducible anymore, but the tool execution can be accelerated
and hard drive memory is saved, especially if input data is very large.

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
        path: /path/to/data.csv
        description: A .csv file containing input data
      foo_folder_data:
        path: /path/to/folder
        format: .nc
        parse: false
        include: false
        description: Folder containing data in netCDF format     
```
