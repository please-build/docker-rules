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

## Configuring the docker registry

You may configure the docker registry through your .plzconfig file:
```
[Plugin "docker"]
Target = //plugins:docker
DefaultDockerRepo = docker.organisation.com
```