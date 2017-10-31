# Container Image Template

This is a template of a git repository which is a source for building container images.

It uses [distgen](https://github.com/devexp-db/distgen/) to render the `Dockerfile` template for specific base image/software version/etc.

## Description

* `Dockerfile.template` — template of `Dockerfile` — it needs to be processed in order to be used to build a container image
* `help/` — a directory containing `help.md` file. This serves as a base for container help page. Similarly to `Dockerfile`, it's also templated and rendered prior to image build.
* `LICENSE` — pick the right license
* `Makefile` — so it's easy to build, run, test
* `README.md` — global documentation for the whole service
  * what is it, how to build, how to use
* `script.sh` — an example script that gets put inside built image
* `specs/` — a directory with configuration fils for [distgen](https://github.com/devexp-db/distgen/)
* `tests/` — these tests should verify that the image works

## Usage

* `sudo make install-dependencies` or alternatively: `sudo ./requirements.sh` - install tools to build and test the container image. You have to call it explicitly.
* `make [DISTRO=<distgen-distro>] [VERSION=<version>] doc` - build docs (help file and a generated manpage) for a certain distro and software version
* `make [DISTRO=<distgen-distro>] [VERSION=<version>] build` - build image for a certain distro and software version
* `make [DISTRO=<distgen-distro>] [VERSION=<version>] run` - build and run container with a certain distro and software version
* `make [DISTRO=<distgen-distro>] [VERSION=<version>] test` - build and test a container with certain distro and software version

Note: default distro is `fedora-26-x86_64` and default version is `2.4`

The included `Makefile` executes all commands in Docker containers (using [image-build-tools](https://github.com/container-images/image-build-tools/) image), hence no dependencies are necessary except of Docker itself. In order to use this `Makefile`, your user needs to have sufficient privileges to use docker. On most Linux distribution, this means adding yourself to the `docker` group. Note, however, that this might be problematic from security POV. For more information about this, please refer to the [Project Atomic article](https://www.projectatomic.io/blog/2015/08/why-we-dont-let-non-root-users-run-docker-in-centos-fedora-or-rhel/) that discusses this.

It is of course possible to create a `Makefile` that wouldn't be using the [image-build-tools](https://github.com/container-images/image-build-tools/) container, but local executables of distgen, meta-test-family and other necessary tools.

## Generated Files

It is highly recommended that generated files are not stored in Github repos. Even thought this may be a matter of personal preference, we feel that storing lots of generated combinations of Dockerfile and other files and regenerating them on every commit is not the best approach. The exception to this is the readme file which, if templated, should be present in the repo in at least one generated combination to provide useful help for newcomers/contributors.

## Automatic Building on Docker Hub

To setup an automated Docker Hub build for a repo that uses distgen, you need to include `hooks/pre_build`. This script makes sure that a certain configuration is rendered on Docker Hub prior to the build itself by using distgen in container in the `pre_build` stage.

As your "Dockerfile Location" parameter at automated build settings at Docker Hub, you need to provide the path to your `Dockerfile.template`. The reason is that Docker Hub makes sure the file is in the repository prior to running any hooks - so if you used the name of the rendered `Dockerfile`, the file would not be found and the build would fail. The `hooks/pre_build` hook first renders your `Dockerfile` and then it runs `mv Dockerfile Dockerfile.template` to replace the template with the rendered `Dockerfile`. This means that `Dockerfile.template` provided in "Dockerfile Location" can actually be built by Docker Hub builder.

## Dummy
