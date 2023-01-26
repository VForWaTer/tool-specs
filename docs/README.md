# Tool specification

## Overview

This document describes specifications for generic [`Tool`](./tool.md) entities. A `Tool` is:
*  any executable software
*  contained in a docker (compatible) container 
*  transforms optional [`Parameters`](./parameter.md) and optional data into output

A very simplified workflow of a tool execution looks like the flowchart below:

```mermaid
flowchart LR
    input --> container --> output
    data --> container

```

The main objective is to create a communitiy-driven tool interface specification, 
that is language-agnostic and can be implemented a layer *below* more tool-specific 
frameworks, like modeling interfaces.
This helps to increase interoperability and reproducibility, especially in a 
scientific context.

At the same time, the tool specification does not rely upon a metadata-schema, 
which is either tool-specific or very generic. But the tool specification can be
extended by any metadata schema.

## Contributing

To contribute to this specification, you can create a [Fork](https://github.com/VForWaTer/tool-specs/fork) 
of the repository and adapt as you suggest. Then open a [Pull Request](https://github.com/VForWaTer/tool-specs/comparehttps://github.com/VForWaTer/tool-specs/compare) and your changes will be reviewed.

If you like to review upcoming changes, you can mail [@mmaelicke](https://github.com/mmaelicke)
or [@AlexDo1](https://github.com/AlexDo1) to make you an outside collaborator 
of this specification.

## Implementations

This section lists the implementations, which we are aware of. By *implementation*, 
we are referring to software packages for different programming languages used
in either of the tools, that help to parse the *parametrization* of a tool into
a language specific data structure. You can read more about [`Parameters` here](./parameter.md).

The available implementations as of now are:
  
|  library          | Language          |  source repository                          | install                       |  template repo                                    |
|:------------------|:-----------------:|:-------------------------------------------:|:-----------------------------:|:-------------------------------------------------:|
| `json2args`       | Python 3.X        | https://github.com/hydrocode-de/json2args | `pip install json2args`         | https://github.com/VForWaTer/tool_template_python | 
| `json2aRgs`       | R > 3.4           | https://github.com/VForWaTer/json2aRgs    | `install.packages("json2aRgs")` | https://github.com/VForWaTer/tool_template_r      | 
| `js2args`         | NodeJS > 14       | https://github.com/VForWaTer/js2args      | `npm install js2args`           | https://github.com/vforwater/tool_template_node   | 
| `getParameters.m` | Octave / MATLAB   | https://github.com/VForWaTer/tool_template_octave | :x:                     | https://github.com/VForWaTer/tool_template_octave | 


The table below lists which implementation exists and what parts of the
tool specification are already covered:


|  specification     |  json2args (Python 3.X)  | json2aRgs (R)      |  getParameters.m (Octave / MATLAB)  |  js2args (Node.js). |
|:-------------------|:------------------------:|:------------------:|:-----------------------------------:|:-------------------:|
|    **Parameter Types**                                                                                                        ||
| string             | :heavy_check_mark:       | :heavy_check_mark: | :heavy_check_mark:                  | :heavy_check_mark:  |
| integer            | :heavy_check_mark:       | :heavy_check_mark: | :heavy_check_mark:                  | :heavy_check_mark:  |
| float              | :heavy_check_mark:       | :heavy_check_mark: | :heavy_check_mark:                  | :heavy_check_mark:  |
| enum               | :heavy_check_mark:       | :heavy_check_mark: | :heavy_check_mark:                  | :heavy_check_mark:  |
| enum -check values | :heavy_check_mark:       | :heavy_check_mark: | :x:                                 | :x:                 |
| boolean            | :grey_question:          | :grey_question:    | :grey_question:                     | :grey_question:     |
| datetime           | :heavy_check_mark:       | :heavy_check_mark: | :x:                                 | :heavy_check_mark:  |
| file - `.dat`      | :heavy_check_mark:       | :heavy_check_mark: | :heavy_check_mark:                  | :heavy_check_mark:  |
| file - `.csv`      | :heavy_check_mark:       | :heavy_check_mark: | :heavy_check_mark:                  | :heavy_check_mark:  |
| file - `.nc`       | :x:                      | :x:                | :x:                                 | :x:                 |
| file - `.sqlite`   | :x:                      | :x:                | :x:                                 | :x:                 |
|    **Parameter fields**                                                                                                       ||
| array              | :heavy_check_mark:       | :grey_question:    | :grey_question:                     | :grey_question:     |
| default            | :x:                      | :x:                | :x:                                 | :x:                 |
| empty parameters*  | :x:                      | :x:                | :x:                                 | :x:                 |


\* `empty parameters` refers to the parameter specification requiring implementations to be able to handle empty or missing `/in/parameter.json` by returing an appropriate empty data structure

## Frameworks

Frameworks refer to software implementations, that run tools for you. Running tool containers
directly by operating the docker/podman CLI is the most low-level option and always possible.
The listed solutions will take some of the management boilerplate from you and
might turn out useful.

### Python

* [`toolbox-runner`](https://github.com/hydrocode-de/tool-runner)


## Contents

* [`Tool`](./tool.md) specification
* [`Parameter`](./parameter.md) specification
