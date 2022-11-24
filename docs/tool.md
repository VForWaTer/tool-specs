# Tool definition

## Description

We define a `Tool` to be a executable script/program inside a docker container. In order to be recognized as a `Tool`, the container has to meet a set of requirements:

1. A description of the tool, its arguments and parameters has to be present in YAML format at the container location `/src/tool.yml`
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
|  |- run.py/.R/.m
```


## Fields

The following section will define all mandatory and optional fields of a `Tool` entity.

### `title`
