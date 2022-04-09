# Cresset: The One Template to Train Them All

[![GitHub stars](https://img.shields.io/github/stars/cresset-template/cresset?style=flat)](https://github.com/cresset-template/cresset/stargazers)
[![GitHub issues](https://img.shields.io/github/issues/cresset-template/cresset?style=flat)](https://github.com/cresset-template/cresset/issues)
[![GitHub forks](https://img.shields.io/github/forks/cresset-template/cresset?style=flat)](https://github.com/cresset-template/cresset/network)
[![GitHub license](https://img.shields.io/github/license/cresset-template/cresset?style=flat)](https://github.com/cresset-template/cresset/blob/main/LICENSE)
[![Twitter](https://img.shields.io/twitter/url?url=https%3A%2F%2Fgithub.com%2Fcresset-template%2Fcresset)](https://twitter.com/intent/tweet?text=Awesome_Project!!!:&url=https%3A%2F%2Fgithub.com%2Fcresset-template%2Fcresset)

**Translations:
[한국어](https://github.com/cresset-template/cresset/blob/main/KOREAN.README.md)**


![Cresset Logo](https://github.com/cresset-template/cresset/blob/main/assets/logo.png "Logo")

------------------------------------------------------------------------

## TL;DR

__*A new MLOps paradigm for deep learning development is proposed using Docker Compose
with the aim of providing reproducible and easy-to-use interactive 
development environments for deep learning practitioners.
Hopefully, the methods presented here will become best practice in both academia and industry.*__


## Introductory Video
[![Weights and Biases Presentation](https://res.cloudinary.com/marcomontalbano/image/upload/v1649474431/video_to_markdown/images/youtube--sW3VxlJl46o-c05b58ac6eb4c4700831b2b3070cd403.jpg)](https://youtu.be/sW3VxlJl46o?t=6865 "Weights and Biases Presentation")
------------------------------------------------------------------------

## Initial Setup
If this is your first time using this project, follow these steps:

1. Install the NVIDIA CUDA driver appropriate for the target hardware. 
The CUDA toolkit is not necessary. If the driver has already been installed, 
check that the installed version is compatible with the target CUDA version.
CUDA driver version mismatch is the single most common issue for new users. See the 
[compatibility matrix](https://docs.nvidia.com/cuda/cuda-toolkit-release-notes/index.html#cuda-major-component-versions__table-cuda-toolkit-driver-versions)
for compatible versions of the CUDA driver and CUDA Toolkit.

2. Install [Docker](https://docs.docker.com/get-docker) if not installed
and update to a recent version compatible with Docker Compose V2.
Docker incompatibility with Docker Compose V2 is a common issue as well.
Note that Windows users may use WSL (Windows Subsystem for Linux).
Cresset has been tested on Windows 11 WSL with the Windows CUDA driver and Docker Desktop.
There is no need to install a separate WSL CUDA driver or Docker for Linux inside WSL.
_N.B._ Windows Security real-time protection causes significant slowdown if enabled.
Disable any active antivirus programs on Windows for best performance.

3. Linux host users should install Docker Compose V2 for Linux as described in the
[documentation](https://docs.docker.com/compose/cli-command/#install-on-linux).
Visit the documentation for the latest installation information.
Installation does _**not**_ require `root` permissions. 
Please check the version and architecture tags in the URL before installing.
The following commands will install Docker Compose V2 (v2.3.4, Linux x86_64) 
for a single user on Linux hosts assuming that the installed Docker version is not too old.
Visit this [link](https://github.com/docker/compose/releases) to find the latest versions.

```shell
# WSL users should instead enable "Use Docker Compose V2" on Docker Desktop for Windows.
mkdir -p ~/.docker/cli-plugins/
curl -SL https://github.com/docker/compose/releases/download/v2.3.4/docker-compose-linux-x86_64 -o ~/.docker/cli-plugins/docker-compose
chmod +x ~/.docker/cli-plugins/docker-compose
```

4. Run `make env` on the terminal at project root to create a basic `.env` file. 
The `.env` file provides environment variables for `docker-compose.yaml`,
allowing different users and machines to set their own variables as required.
The `.env` file is excluded from version control via `.gitignore` by design.

5. To build from source, set `BUILD_MODE=include` and set the
CUDA Compute Capability (CCA) of the target hardware.
Visit the NVIDIA [website](https://developer.nvidia.com/cuda-gpus#compute)
to find compute capabilities of NVIDIA GPUs. Visit the
[documentation](https://docs.nvidia.com/cuda/cuda-c-programming-guide/index.html#compute-capabilities)
for an explanation of compute capability and its relevance.
Note that the Docker cache will save previously built binaries
if the given configurations are identical.

6. Read the `docker-compose.yaml` file to fill in extra variables in `.env`.
Also, feel free to edit `docker-compose.yaml` as necessary by changing 
session names, hostnames, etc. for different projects and configurations.
The `docker-compose.yaml` file provides reasonable default values but these 
can be overridden by values specified in the `.env` file.

Example `.env` file for user with username `USERNAME`, user id `1000`,  group id `1000` on service `full`.
Edit the `docker-compose.yaml` file and the `Makefile` to specify services other than `full`.
```text
# Generated automatically by `make up`.
GID=1000
UID=1000
IMAGE_NAME_FULL=full-USERNAME

# Environment configurations users must fill in manually.

# NVIDIA GPU Compute Capability (CCA) values may be found at https://developer.nvidia.com/cuda-gpus
CCA=8.6                            # Compute capability. CCA=8.6 for RTX3090 and A100.
# CCA="7.5 8.6+PTX"                # See https://pytorch.org/docs/stable/cpp_extension.html

LINUX_DISTRO=ubuntu                # Visit the NVIDIA Docker Hub repo for available base images. 
DISTRO_VERSION=20.04               # https://hub.docker.com/r/nvidia/cuda/tags
CUDA_VERSION=11.5.1                # Must be compatible with hardware and CUDA driver.
CUDNN_VERSION=8                    # Only major version specifications are available.
PYTHON_VERSION=3.9                 # Minor version specifications are not guaranteed to work.
MKL_MODE=include                   # Enable for Intel CPUs.

# Use only if building PyTorch from source (`BUILD_MODE=include`).
# The `*_TAG` variables are used only if `BUILD_MODE=include`.
BUILD_MODE=include                 # Whether to build PyTorch from source.
PYTORCH_VERSION_TAG=v1.11.0        # Any `git` branch or tag name can be used.
TORCHVISION_VERSION_TAG=v0.12.0
TORCHTEXT_VERSION_TAG=v0.12.0
TORCHAUDIO_VERSION_TAG=v0.11.0
```

7. Edit requirements in `reqs/apt-train.requirements.txt` and `reqs/pip-train.requirements.txt`.
These contain project package dependencies. The `apt` requirements are designed to resemble an
ordinary Python `requirements.txt` file.

8. Run `make up` or `make rebuild` to start the service. 
This may take some time if `BUILD_MODE=include`, especially for the first time.
The `make` commands are defined in the `Makefile` and target the `full` service by default.
Please read the `Makefile` for implementation details and usage.
If the build fails during `git clone`, try `make rebuild` again with a stable internet connection.
If the build fails during `pip install`, check the PyPI mirror and package requirements.

9. Run `make exec` to enter the interactive container environment. Then start coding.

## Makefile Instructions
1. To create a new container without rebuilding the image, use `make up`.
2. To force build a new image from the Dockerfile, use `make rebuild`.
3. To enter a running container in an interactive terminal, use `make exec`.
4. To show services on the system, use `make ls`.
5. To delete all containers and networks (but not images), use `make down`.

Read the `Makefile` for all instructions and their implementations.


### Tips

1. Configurations such as connected volumes and network ports cannot 
be changed in a running container, requiring a new container.

2. Docker automatically caches all builds up to `defaultKeepStorage`.
Builds use caches from previous builds by default, 
greatly speeding up later builds by only building modified layers.

## Project Overview
The main components of the project are as follows. The other files are utilities.
1) Dockerfile 
2) docker-compose.yaml 
3) reqs/*requirements.txt 
4) .env

When the user inputs `make up` or another `make` command, 
commands specified in the `Makefile` are executed.
The `Makefile` is used to specify shorthand commands and variables.

When a command related to Docker Compose (e.g., `make rebuild`) is executed,
The `docker-compose.yaml` file and the `.env` file are read by Docker Compose.
The `docker-compose.yaml` file specifies reasonable default values
but users may wish to change them as per their needs.
The values specified in the `.env` file take precedence over 
the defaults specified in the `docker-compose.yaml` file.
Environment variables specified in the shell 
take precedence over those in the `.env` file.
The `.env` file is deliberately excluded from source control
to allow different users and machines to use different configurations.

The `docker-compose.yaml` file manages configurations, builds, runs, etc. using the `Dockerfile`.
Visit the Docker Compose [Specification](https://github.com/compose-spec/compose-spec/blob/master/spec.md)
and [Reference](https://docs.docker.com/compose/compose-file/compose-file-v3/) for details.

The `Dockerfile` is configured to read only requirements files in the `reqs` directory.
Edit `reqs/pip-train.requirements.txt` to specify Python package requirements.
Edit `reqs/apt-train.requirements.txt` to specify Ubuntu package requirements.
Users must edit the `.dockerignore` file to `COPY` other files into the Docker build,
for example, when building from private code during the Docker build.

The `Dockerfile` uses Docker Buildkit and a multi-stage build where
control flow is specified via stage names and build-time environment variables 
given via `docker-compose.yaml`. See the Docker Buildkit 
[Syntax](https://github.com/moby/buildkit/blob/master/frontend/dockerfile/docs/syntax.md)
for more information on Docker Buildkit.
The `full` service specified in the `docker-compose.yaml` file uses 
the `train` stage specified in the `Dockerfile`, which assumes an Ubuntu image.


## _Raison d'Être_
The purpose of this section is to introduce a new paradigm for deep learning development. 
I hope that Cresset, or at least the ideas behind it, will eventually become 
best practice for small to medium-scale deep learning research and development.

Developing in local environments with `conda` or `pip` 
is commonplace in the deep learning community.
However, this risks rendering the development environment, 
and the code meant to run on it, unreproducible.
This state of affairs is a serious detriment to scientific progress
that many readers of this article will have experienced at first-hand.

Docker containers are the standard method for providing reproducible programs 
across different computing environments. 
They create isolated environments where programs 
can run without interference from the host or from one another.
See https://www.docker.com/resources/what-container for details.

But in practice, Docker containers are often misused. 
Containers are meant to be transient and best practice dictates 
that a new container be created for each run.
However, this is very inconvenient for development, 
especially for deep learning applications, 
where new libraries must constantly be installed and 
bugs are often only evident at runtime.
This leads many researchers to develop inside interactive containers.
Docker users often have `run.sh` files with commands such as
`docker run -v my_data:/mnt/data -p 8080:22 -t my_container my_image:latest /bin/bash`
(look familiar, anyone?) and use SSH to connect to running containers.
VSCode even provides a remote development mode to code inside containers.

The problem with this approach is that these interactive containers 
become just as unreproducible as local development environments.
A running container cannot connect to a new port or attach a new
[volume](https://docs.docker.com/storage/volumes).
But if the computing environment within the container was created over 
several months of installs and builds, the only way to keep it is to 
save the container as an image and create a new container from the saved image.
After a few iterations of this process, the resulting images become bloated and 
no less scrambled than the local environments that they were meant to replace.

Problems become even more evident when preparing for deployment.
MLOps, defined as a set of practices that aims to deploy and maintain 
machine learning models reliably and efficiently, has gained enormous popularity 
of late as many practitioners have come to realize the importance of 
continuously maintaining ML systems long after the initial development phase ends.

However, bad practices such as those mentioned above mean that much coffee has 
been spilled turning research code into anything resembling a production-ready product.
Often, even the original developers cannot recreate the same model after a few months.
Many firms thus have entire teams dedicated to model translation, a huge expenditure.

To alleviate these problems, I propose the use of Docker Compose as a simple MLOps solution.
Using Docker and Docker Compose, the entire training environment can be reproduced.
Compose has not yet caught on in the deep learning community,
possibly because it is usually advertised as a multi-container solution.
This is a misunderstanding as it can be used for single-container development just as well.

A `docker-compose.yaml` file is provided for easy management of containers.
**Using the provided `docker-compose.yaml` file will create an interactive environment,
providing a programming experience very similar to using a terminal on a remote server.
Integrations with popular IDEs (PyCharm, VSCode) are also available.**
Moreover, it also allows the user to specify settings for both build and run,
removing the need to manage the environment with custom shell scripts.
Connecting a new volume or port is as simple as removing the current container,
adding a line in the `docker-compose.yaml` file, then running `make up` 
to create a new container from the same image. 
Build caches allow new images to be built very quickly,
removing another barrier to Docker adoption, the long initial build time.
For more information on Compose, visit the [documentation](https://docs.docker.com/compose).

Docker Compose can also be used directly for deployment, including on the cloud,
which is useful for small-scale deployments. See https://www.compose-spec.io.
If and when large-scale deployments using Kubernetes becomes necessary,
using reproducible Docker environments from the very beginning will accelerate 
the development process and smooth the path to MLOps adoption.
Accelerating time-to-market by streamlining the development process
is a competitive edge for any firm, whether lean startup or tech titan.

With luck, the techniques I propose here will enable 
the deep learning community to "_write once, train anywhere_".
But even if I fail in persuading the majority of users of the merits of my method,
I may still spare many a hapless grad student from the 
sisyphean labor of setting up their `conda` environment,
only to have it crash and burn right before their paper submission is due.


## Compose as Best Practice

Docker Compose is superior to using custom shell scripts for each environment.
Not only does it gather all variables and commands for both build and run into a single file,
but its native integration with Docker means that it makes complicated Docker 
build/run setups simple to implement and use.

I wish to emphasize that using Docker Compose this way is a general-purpose technique 
that does not depend on anything about this project.
As an example, an image from the NVIDIA NGC PyTorch repository 
has been used as the base image in `ngc.Dockerfile`.
The NVIDIA NGC PyTorch images contain many optimizations 
for the latest GPU architectures and provide
a multitude of pre-installed machine learning libraries. 
For those starting new projects, using the latest NGC image is recommended.

To use the NGC images, use the following commands:

1. `docker compose up -d ngc`
2. `docker compose exec ngc zsh`

The only difference with the previous example is the service name.


### Using Compose with PyCharm and VSCode

The Docker Compose container environment can be used with popular Python IDEs, not just in the terminal.
PyCharm and Visual Studio Code, both very popular in the deep learning community,
are compatible with Docker Compose.

1. If you are using a remote server, first create a Docker 
[context](https://docs.docker.com/engine/context/working-with-contexts)
to connect your local Docker with the remote Docker.

2. **PyCharm** (Professional only): Both Docker and Docker Compose are natively available as Python interpreters. 
See tutorials for [Docker](https://www.jetbrains.com/help/pycharm/docker.html) and 
[Compose](https://www.jetbrains.com/help/pycharm/using-docker-compose-as-a-remote-interpreter.html#summary) for details.
JetBrains [Gateway](https://www.jetbrains.com/remote-development/gateway)
can also be used to connect to running containers.
JetBrains Fleet IDE, with much more advanced features, will become available in early 2022.
_N.B._ PyCharm Professional and other JetBrains IDEs are available 
free of charge to anyone with a valid university e-mail address.

3. **VSCode**: Install the Remote Development extension pack. 
See [tutorial](https://code.visualstudio.com/docs/remote/containers-tutorial) for details.


# Known Issues

1. Connecting to a running container by `ssh` will remove all variables set by `ENV`.
This is because `sshd` starts a new environment, wiping out all previous variables.
Using `docker`/`docker compose` to enter containers is strongly recommended.

2. WSL users using Compose should disable `ipc: host`. WSL cannot use this option.

3. PyTorch source builds require a corresponding `magma-cudaXXX` package in the PyTorch anaconda channel.
CUDA 11.4.x is not available as `magma-cuda114` is unavailable. 
Nor can new versions of CUDA be used until a `magma` package is published.

4. If the build fails during `git clone`, simply try `make rebuild` again.
Most of the build will be cached. Failure is probably due to networking issues during installation.
Updating git submodules is [not fail-safe](https://stackoverflow.com/a/8573310/9289275).

5. `torch.cuda.is_available()` will return a `... UserWarning: CUDA initialization:...` error 
or the image will simply not start if the CUDA driver on the host 
is incompatible with the CUDA version on the Docker image.
Either upgrade the host CUDA driver or downgrade the CUDA version of the image.
Check the [compatibility matrix](https://docs.nvidia.com/cuda/cuda-toolkit-release-notes/index.html#cuda-major-component-versions__table-cuda-toolkit-driver-versions)
to see if the host CUDA driver is compatible with the desired version of CUDA.
Also check if the CUDA driver has been configured correctly on the host.
The CUDA driver version can be found using the `nvidia-smi` command.

6. Docker Compose V2 will silently fail if the installed Docker version is too low.
Update Docker to the latest version (20.10+) to use Docker Compose V2.


# Desiderata

1. **MORE STARS**. _**No Contribution Without Appreciation!**_

2. A method of building `Magma` from source would be appreciated.
Currently, Cresset depends on the `magma-cudaXXX` package
provided in the PyTorch channel of Anaconda.

3. Bug reports are welcome. Only the latest versions has been tested rigorously.
Please raise an issue if there are any versions that do not build properly. 
However, please check that your host Docker, Docker Compose, 
and especially NVIDIA Driver are up-to-date before doing so.
Also, note that some combinations of PyTorch version and CUDA environment 
may simply be impossible to build because of issues in the underlying source code.

4. Translations into other languages and updates to existing translations are welcome. 
Please create a separate `LANG.README.md` file and make a Pull Request.
