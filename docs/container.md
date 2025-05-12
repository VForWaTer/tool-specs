# Container structure

## tl;dr;
In order to be recognized as a tool following this specification, a container 
image has to meet these requirements:

1. A description of the tool, its arguments, parameters and input data has to be present in YAML format at the container location `/src/tool.yml`
2. The executable script/program has to either store results at the location `/out/` of the container, or print them to the containers StdOut.
3. A tool execution may be parameterized. The parameterization is stored at `/in/input.json` of the container. Additional input data may be added to `/in/input.json`.

## Description

In order to work properly, there is a minimum required structure, that the 
conatainer has to follow. In  order to build a container image accordingly,
there are a number of template repositories on Github:

* [Python template](https://github.com/VForWaTer/tool_template_python)
* [R template](https://github.com/VForWaTer/tool_template_r)
* [Node.js template](https://github.com/VForWaTer/tool_template_node)
* [Octave template](https://github.com/VForWaTer/tool_template_octave)
* [MATLAB template](https://github.com/VForWaTer/tool_template_matlab)

The core idea is to enforce a specific structure in the container, so that 
others know where to find things:

```
/
|- in/
|  |- input.json
|- out/
|  |- ...
|- src/
|  |- tool.yml
|  |- run.[py/R/js/m]
|  |- CITATION.cff
```

Here, the two locations `/in` and `/out` are mount points to pass data and 
parameters in and out of the container.

### Input parameterization

`/in/input.json` contains the the parameterization for running the tool. 
There are client libraries for each of the supported languages, which read and 
validate the parameterization. Runtime CLI parameters are not supported yet, 
but planned for the future.

### Metadata

`/src/tool.yml` is a mandatory metadata file containing the most important 
information about the tool.

### Entrypoint

`/src/run.[py/R/js/m]` is the single entrypoint into the tool. 
Any executable file is supported. Please note, that the tool-specs only support 
transformation-like tools, that *end* (no service tools).

Please note: In order to work properly with external tools, you implement the 
execution of the script, as shown in the sample repositories, as the default
Docker command, **not** entrypoint:

```Dockerfile
CMD ["python", "run.py"]
```

This way, you can `exec` or `run` into the container and `cat /src/tool.yml`
to build frameworks that can read the metadata of each tool available on the system.

### Citation information

Each of the template repositories include a `CITATION.cff` to distribute 
citation information to users of the tool. You, as a developer, should **replace**
the citation information in the template repositories. The templates are licensed
as CC zero.

The `CITATION.cff` file format is described [here](https://citation-file-format.github.io/).
There is also an online tool to build these files: 
[https://citation-file-format.github.io/cff-initializer-javascript/](https://citation-file-format.github.io/cff-initializer-javascript/)

CFF files are well supported by Github, Zenodo or Zotero.

