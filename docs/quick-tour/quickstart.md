---
sidebar_position: 2
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

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

To pull the docker image from Docker registry, first select the docker image tag you suits your needs, then run:

<Tabs>
  <TabItem value="docker-hub" label="Docker Hub" default>


```bash
docker pull pywhlobf/pywhlobf:23.1.0-manylinux2014_x86_64
```

  </TabItem>
  <TabItem value="docker-registry-hosted-in-mainland-china" label="Docker registry hosted in mainland China" default>

If you are a user located in mainland China and would like to increase download speeds, please add `swr.cn-east-3.myhuaweicloud.com/` to the beginning of the docker image name:

```bash
PREFIX='swr.cn-east-3.myhuaweicloud.com/'
docker pull "${PREFIX}pywhlobf/pywhlobf:23.1.0-manylinux2014_x86_64"
docker tag "${PREFIX}pywhlobf/pywhlobf:23.1.0-manylinux2014_x86_64" "pywhlobf/pywhlobf:23.1.0-manylinux2014_x86_64"
```

  </TabItem>
</Tabs>

### From PyPI

If you need to use `pywhlobf` on platforms other than Linux, such as macOS and Windows, you can install it from [PyPI](https://pypi.org/project/pywhlobf/) with `pip`:

```bash
pip install pywhlobf
```

## The Command-Line Interface (CLI)

`pywhlobf` provides a diverse range of command-line interface (CLI) commands to accomplish various tasks.

<Tabs>
<TabItem value="plain-cli" label="Plain CLI" default>

```bash
pywhlobf - -- --help
```

</TabItem>
<TabItem value="docker-image-cli" label="Docker Image CLI" default>

```bash
docker run \
  --rm -it \
  pywhlobf/pywhlobf:23.1.0-manylinux2014_x86_64 \
  - -- --help
```

Explantion:

- `--rm`: Automatically removes the container when it exits.
- `-it`: Attaches a terminal to the container, allowing the user to interact with it.

</TabItem>
</Tabs>

The aforementioned command will display a list of supported commands, which are comprehensively documented in the [Commands](/commands) section:

```text
NAME
    pywhlobf

SYNOPSIS
    pywhlobf - COMMAND

COMMANDS
    COMMAND is one of the following:

     code_file
       Obfuscate a single code file.

     code_file_config
       Generate the JSON config required by `code_file` command.

     decrypt_message
       Decrypt the message.

     package_folder
       Obfuscate a package folder.

     package_folder_config
       Generate the JSON config required by `package_folder` command.

     run_extension_file
       Run an extension file.

     wheel_file
       Obfuscate a wheel file.

     wheel_file_config
       Generate the JSON config required by `wheel_file` command.
```

## Usage Examples

### Obfuscate A Single Code File

Let's create a simple Python code file and name it `example.py`:

```python
# example.py
def greetings():
    print('greetings from pywhlobf!')

if __name__ == '__main__':
    greetings()
```

To save the `example.py` file in the current working directory, execute the following command:

```bash
cat << 'EOF' > example.py
# example.py
def greetings():
    print('greetings from pywhlobf!')

if __name__ == '__main__':
    greetings()
EOF
```

After saving the file, execute `ls -lt` command to display the current working directory:

```text
total 8
-rw-r--r--  1 huntzhan  staff  112 Mar 14 14:49 example.py
```

Before obfuscating `example.py`, we must first generate a JSON configuration file, by executing the [pywhlobf code-file-config](/commands/pywhlobf_code_file_config) command:

<Tabs>
<TabItem value="plain-cli" label="Plain CLI" default>

```bash
pywhlobf code-file-config --config-file code-file.json
```

</TabItem>
<TabItem value="docker-image-cli" label="Docker Image CLI" default>

```bash
docker run \
  --rm -it \
  --user "$(id -u):$(id -g)" \
  -v "$(pwd)":/data \
  pywhlobf/pywhlobf:23.1.0-manylinux2014_x86_64 \
  code-file-config --config-file /data/code-file.json
```

Explantion:

- `--user "$(id -u):$(id -g)"`: Sets the user ID and group ID of the container to match those of the current user running the Docker command, which helps prevent permission issues with mounted volumes.
- `-v "$(pwd)":/data`: Mounts the current working directory on the host machine `$(pwd)` to the `/data` directory in the container, allowing `pywhlobf` to generate the JSON configuration file in the current directory.

</TabItem>
</Tabs>

After generating the JSON configuration file, you can view it by executing the `ls -lt` command:

```text
total 16
-rw-r--r--  1 huntzhan  staff  425 Mar 14 14:57 code-file.json
-rw-r--r--  1 huntzhan  staff  112 Mar 14 14:49 example.py
```

Now, you are ready to convert the `example.py` file into a compiled shared library, by executing the [pywhlobf code-file](/commands/pywhlobf_code_file) command:

<Tabs>
<TabItem value="plain-cli" label="Plain CLI" default>

```bash
pywhlobf code-file --config-file code-file.json --input-file example.py
```

</TabItem>
<TabItem value="docker-image-cli" label="Docker Image CLI" default>

```bash
docker run \
  --rm -it \
  --user "$(id -u):$(id -g)" \
  -v "$(pwd)":/data \
  pywhlobf/pywhlobf:23.1.0-manylinux2014_x86_64 \
  code-file --config-file /data/code-file.json --input-file /data/example.py
```

</TabItem>
</Tabs>

If everything goes right, you should be able to see a newly compiled shared library in the current working directory. This file will have a `.so` extension if you're using Linux/macOS, or a `.pyd` extension if you're using Windows. You can view the newly compiled shared library by executing the `ls -lt` command:

```text
total 2136
-rw-r--r--  1 huntzhan  staff  1081608 Mar 14 15:31 example.cpython-38-x86_64-linux-gnu.so
-rw-r--r--  1 huntzhan  staff      425 Mar 14 14:57 code-file.json
-rw-r--r--  1 huntzhan  staff      112 Mar 14 14:49 example.py
```

We can now execute the obfuscated file using the [pywhlobf run-extension-file](/commands/pywhlobf_run_extension_file) command:

<Tabs>
<TabItem value="plain-cli" label="Plain CLI" default>

```bash
pywhlobf run-extension-file example.cpython-38-x86_64-linux-gnu.so
```

</TabItem>
<TabItem value="docker-image-cli" label="Docker Image CLI" default>

```bash
docker run \
  --rm -it \
  --user "$(id -u):$(id -g)" \
  -v "$(pwd)":/data \
  pywhlobf/pywhlobf:23.1.0-manylinux2014_x86_64 \
  run-extension-file /data/example.cpython-38-x86_64-linux-gnu.so
```

</TabItem>
</Tabs>

The aforementioned command should display:

```text
greetings from pywhlobf!
```

### Obfuscate A Wheel File

Let's download a wheel file into the current working directory:

```bash
wget \
  https://files.pythonhosted.org/packages/27/d6/003e593296a85fd6ed616ed962795b2f87709c3eee2bca4f6d0fe55c6d00/wheel-0.37.1-py2.py3-none-any.whl \
  -O "./wheel-0.37.1-py2.py3-none-any.whl"
```

After downloading the wheel file, execute `ls -lt` command to display the current working directory:

```text
total 72
-rw-r--r--  1 huntzhan  staff  35301 Dec 22  2021 wheel-0.37.1-py2.py3-none-any.whl
```

Before obfuscating `wheel-0.37.1-py2.py3-none-any.whl`, we must first generate a JSON configuration file, by executing the [pywhlobf wheel-file-config](/commands/pywhlobf_wheel_file_config) command:

<Tabs>
<TabItem value="plain-cli" label="Plain CLI" default>

```bash
pywhlobf wheel-file-config --config-file wheel-file.json
```

</TabItem>
<TabItem value="docker-image-cli" label="Docker Image CLI" default>

```bash
docker run \
  --rm -it \
  --user "$(id -u):$(id -g)" \
  -v "$(pwd)":/data \
  pywhlobf/pywhlobf:23.1.0-manylinux2014_x86_64 \
  wheel-file-config --config-file /data/wheel-file.json
```

Explantion:

- `--user "$(id -u):$(id -g)"`: Sets the user ID and group ID of the container to match those of the current user running the Docker command, which helps prevent permission issues with mounted volumes.
- `-v "$(pwd)":/data`: Mounts the current working directory on the host machine `$(pwd)` to the `/data` directory in the container, allowing `pywhlobf` to generate the JSON configuration file in the current directory.

</TabItem>
</Tabs>

After generating the JSON configuration file, you can view it by executing the `ls -lt` command:

```text
total 80
-rw-r--r--  1 huntzhan  staff    754 Mar 14 16:56 wheel-file.json
-rw-r--r--  1 huntzhan  staff  35301 Dec 22  2021 wheel-0.37.1-py2.py3-none-any.whl
```

Now, you are ready to obfuscate the `wheel-0.37.1-py2.py3-none-any.whl` file by executing the [pywhlobf wheel-file](/commands/pywhlobf_wheel_file) command. This command will convert all `.py` Python source files contained within the wheel file into compiled shared libraries:

<Tabs>
<TabItem value="plain-cli" label="Plain CLI" default>

```bash
pywhlobf wheel-file --config-file wheel-file.json --input-file wheel-0.37.1-py2.py3-none-any.whl
```

</TabItem>
<TabItem value="docker-image-cli" label="Docker Image CLI" default>

```bash
docker run \
  --rm -it \
  --user "$(id -u):$(id -g)" \
  -v "$(pwd)":/data \
  pywhlobf/pywhlobf:23.1.0-manylinux2014_x86_64 \
  wheel-file --config-file /data/wheel-file.json --input-file /data/wheel-0.37.1-py2.py3-none-any.whl
```

</TabItem>
</Tabs>

If everything goes right, you should be able to see a newly generated wheel file in the current working directory. You can view the newly generated wheel filey by executing the `ls -lt` command:

```text
total 36048
-rw-r--r--  1 huntzhan  staff  17928707 Mar 14 17:28 wheel-0.37.1-cp38-cp38-cp38-manylinux2014_x86_64.whl
-rw-r--r--  1 huntzhan  staff       755 Mar 14 17:26 wheel-file.json
-rw-r--r--  1 huntzhan  staff     35301 Dec 22  2021 wheel-0.37.1-py2.py3-none-any.whl
```

Now, we can proceed to compare the original wheel file with the obfuscated version. 

The orginal wheel file contains:

```text
Archive:  wheel-0.37.1-py2.py3-none-any.whl
  Length      Date    Time    Name
---------  ---------- -----   ----
       23  12-22-2021 12:21   wheel/__init__.py
      417  12-22-2021 12:21   wheel/__main__.py
    19075  12-22-2021 12:21   wheel/bdist_wheel.py
    15930  12-22-2021 12:21   wheel/macosx_libfile.py
     4344  12-22-2021 12:21   wheel/metadata.py
     1257  12-22-2021 12:21   wheel/pkginfo.py
      938  12-22-2021 12:21   wheel/util.py
     7574  12-22-2021 12:21   wheel/wheelfile.py
     2572  12-22-2021 12:21   wheel/cli/__init__.py
     9498  12-22-2021 12:21   wheel/cli/convert.py
     3364  12-22-2021 12:21   wheel/cli/pack.py
      673  12-22-2021 12:21   wheel/cli/unpack.py
        0  12-22-2021 12:21   wheel/vendored/__init__.py
        0  12-22-2021 12:21   wheel/vendored/packaging/__init__.py
     1812  12-22-2021 12:21   wheel/vendored/packaging/_typing.py
    29560  12-22-2021 12:21   wheel/vendored/packaging/tags.py
     1125  12-22-2021 12:21   wheel-0.37.1.dist-info/LICENSE.txt
     2328  12-22-2021 12:21   wheel-0.37.1.dist-info/METADATA
      110  12-22-2021 12:21   wheel-0.37.1.dist-info/WHEEL
      108  12-22-2021 12:21   wheel-0.37.1.dist-info/entry_points.txt
        6  12-22-2021 12:21   wheel-0.37.1.dist-info/top_level.txt
     1731  12-22-2021 12:21   wheel-0.37.1.dist-info/RECORD
---------                     -------
   102445                     22 files
```

And the obfuscated wheel file contains:

```text
Archive:  wheel-0.37.1-cp38-cp38-cp38-manylinux2014_x86_64.whl
  Length      Date    Time    Name
---------  ---------- -----   ----
   891240  03-14-2023 09:28   wheel/__init__.cpython-38-x86_64-linux-gnu.so
  1390096  03-14-2023 09:28   wheel/__main__.cpython-38-x86_64-linux-gnu.so
 12083712  03-14-2023 09:28   wheel/bdist_wheel.cpython-38-x86_64-linux-gnu.so
  7280624  03-14-2023 09:28   wheel/macosx_libfile.cpython-38-x86_64-linux-gnu.so
  3965912  03-14-2023 09:28   wheel/metadata.cpython-38-x86_64-linux-gnu.so
  2278952  03-14-2023 09:28   wheel/pkginfo.cpython-38-x86_64-linux-gnu.so
  1667792  03-14-2023 09:28   wheel/util.cpython-38-x86_64-linux-gnu.so
  7432872  03-14-2023 09:28   wheel/wheelfile.cpython-38-x86_64-linux-gnu.so
  3009384  03-14-2023 09:28   wheel/cli/__init__.cpython-38-x86_64-linux-gnu.so
  6403728  03-14-2023 09:28   wheel/cli/convert.cpython-38-x86_64-linux-gnu.so
  2963936  03-14-2023 09:28   wheel/cli/pack.cpython-38-x86_64-linux-gnu.so
  1656352  03-14-2023 09:28   wheel/cli/unpack.cpython-38-x86_64-linux-gnu.so
   875880  03-14-2023 09:28   wheel/vendored/__init__.cpython-38-x86_64-linux-gnu.so
   875960  03-14-2023 09:28   wheel/vendored/packaging/__init__.cpython-38-x86_64-linux-gnu.so
  1128368  03-14-2023 09:28   wheel/vendored/packaging/_typing.cpython-38-x86_64-linux-gnu.so
 15053280  03-14-2023 09:28   wheel/vendored/packaging/tags.cpython-38-x86_64-linux-gnu.so
     1125  03-14-2023 09:27   wheel-0.37.1.dist-info/LICENSE.txt
     2328  03-14-2023 09:27   wheel-0.37.1.dist-info/METADATA
      110  03-14-2023 09:27   wheel-0.37.1.dist-info/WHEEL
      108  03-14-2023 09:27   wheel-0.37.1.dist-info/entry_points.txt
        6  03-14-2023 09:27   wheel-0.37.1.dist-info/top_level.txt
     2234  03-14-2023 09:28   wheel-0.37.1.dist-info/RECORD
```

The message above indicates all `.py` Python source files within the wheel file have been successfully compiled into shared libraries.