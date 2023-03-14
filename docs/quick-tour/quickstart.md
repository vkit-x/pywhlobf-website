---
sidebar_position: 2
---

# Quick Start

## Installation

There are two ways to obtain `pywhlobf`:

- From docker registry. (recommaned)
- From PyPI.

### From Docker Registry

We've built and published a series of docker images to the follwoing registries:

- Docker Hub.
- Docker registry hosted in mainland China.

**You can find all the supported docker image tags [here](https://hub.docker.com/r/pywhlobf/pywhlobf/tags).**

Our docker image tag adheres to the following pattern: `{pywhlobf version}(-cython3)?-{platform tag}`:

- `{pywhlobf version}`: The `pywhlobf` package version. We are following the [Calendar Versioning](https://calver.org/) convention.
- `(-cython3)?`: When the `-cython3` tag is included, the docker image is built using Cython 3.0. Otherwise, it uses Cython 0.29. Despite being in the pre-release phase, Cython 3.x offers a wider range of features. It is recommanded to use Cython 0.29-based image first, that is, docker image tag without `-cython3`. If you encounter compilation errors, then you may switch to the Cython 3.0-based image.
- `{platform tag}`: [Platform tag](https://packaging.python.org/en/latest/specifications/platform-compatibility-tags/#platform-tag) specifies the platform requirements, such as the operating system and the glibc version.
  - For Linux, the following platform tags are supported (For more information, please visit [pypa/manylinux](https://github.com/pypa/manylinux)):
    - `manylinux2014_x86_64`
    - `manylinux_2_24_x86_64`
    - `manylinux_2_28_x86_64`
  - It is possible to support a Windows-based docker image in the future.
  - Supporting a macOS-based docker image is unlikely, as it requires extensive hacking.

To pull the docker image from Docker Hub, first select the docker image tag you suits your needs, then run:

```bash
docker pull pywhlobf/pywhlobf:23.1.0-manylinux2014_x86_64
```

If you are a user located in mainland China and would like to increase download speeds, please add `swr.cn-east-3.myhuaweicloud.com/` to the beginning of the docker image name:

```bash
PREFIX='swr.cn-east-3.myhuaweicloud.com/'
docker pull "${PREFIX}pywhlobf/pywhlobf:23.1.0-manylinux2014_x86_64"
docker tag "${PREFIX}pywhlobf/pywhlobf:23.1.0-manylinux2014_x86_64" "pywhlobf/pywhlobf:23.1.0-manylinux2014_x86_64"
```

### From PyPI

If you need to use `pywhlobf` on platforms other than Linux, such as macOS and Windows, you can install it from [PyPI](https://pypi.org/project/pywhlobf/) with `pip`:

```bash
pip install pywhlobf
```
