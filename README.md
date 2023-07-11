# Docker rules
Docker rules for the Please build system

## Basic usage

To get started, run `plz init plugin docker`. You can then build a base image like so:

```python
docker_image(
    name = "base",
    dockerfile = "Dockerfile-base",
    visibility = ["PUBLIC"],
)

docker_image(
    name = "image",
    srcs = ["//hello_service"],
    dockerfile = "Dockerfile",
    base_image = "//common/docker:base",
)
```

Dockerfile-base:
```dockerfile
FROM ubuntu:22.04

RUN apt update && apt upgrade && apt install python 

```

Dockerfile: 
```dockerfile
FROM //common/docker:base

COPY /hello_service.pex /hello_service.pex

ENTRYPOINT [ "/hello_service.pex" ] 
```

There are a number of runnable scripts that you can use to build and push these images. These scripts will ensure that
the images are built in the correct order:

To `docker build` the image, including it's base image:

```
$ plz run //service/docker:image
```

There are also other hidden targets that can be useful:

- `:{name}_load` - Alias for the `docker build` script above, building the image into the host machines docker
- `:{name}_run` - Runs `docker run -it` for the image
- `:{name}_push` - Runs `docker push` for the image 
- `:{name}_save` - Runs `docker image save`, saving the image into `plz-out/gen/{package_path}`

You may also build `:{name}_fqn` which will produce a file with the fully qualified image name including the 
deterministic tag. 

## Configuring the docker registry

You may configure the docker registry through your .plzconfig file:
```
[Plugin "docker"]
Target = //plugins:docker
DefaultDockerRepo = docker.organisation.com
```