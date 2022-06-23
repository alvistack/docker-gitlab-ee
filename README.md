# Docker Image Packaging for GitLab EE

<img src="/alvistack.svg" width="75" alt="AlviStack">

[![GitLab pipeline status](https://img.shields.io/gitlab/pipeline/alvistack/docker-gitlab-ee/master)](https://gitlab.com/alvistack/docker-gitlab-ee/-/pipelines)
[![GitHub tag](https://img.shields.io/github/tag/alvistack/docker-gitlab-ee.svg)](https://github.com/alvistack/docker-gitlab-ee/tags)
[![GitHub license](https://img.shields.io/github/license/alvistack/docker-gitlab-ee.svg)](https://github.com/alvistack/docker-gitlab-ee/blob/master/LICENSE)
[![Docker Pulls](https://img.shields.io/docker/pulls/alvistack/gitlab-ee-15.1.svg)](https://hub.docker.com/r/alvistack/gitlab-ee-15.1)

GitLab is a complete DevOps platform, delivered as a single application. This makes GitLab unique and makes Concurrent DevOps possible, unlocking your organization from the constraints of a pieced together toolchain. Join us for a live Q\&A to learn how GitLab can give you unmatched visibility and higher levels of efficiency in a single application across the DevOps lifecycle.

Learn more about GitLab: <https://about.gitlab.com/>

## Supported Tags and Respective Packer Template Links

  - [`alvistack/gitlab-ee-15.1`](https://hub.docker.com/r/alvistack/gitlab-ee-15.1)
      - [`packer/docker-15.1/packer.json`](https://github.com/alvistack/docker-gitlab-ee/blob/master/packer/docker-15.1/packer.json)
  - [`alvistack/gitlab-ee-15.0`](https://hub.docker.com/r/alvistack/gitlab-ee-15.0)
      - [`packer/docker-15.0/packer.json`](https://github.com/alvistack/docker-gitlab-ee/blob/master/packer/docker-15.0/packer.json)

## Overview

This Docker container makes it easy to get an instance of GitLab EE up and running.

Based on [Official Ubuntu Docker Image](https://hub.docker.com/_/ubuntu/) with some minor hack:

  - Packaging by Packer Docker builder and Ansible provisioner in single layer
  - Handle `ENTRYPOINT` with [catatonit](https://github.com/openSUSE/catatonit)

### Quick Start

For the `VOLUME` directory that is used to store the repository data (amongst other things) we recommend mounting a host directory as a [data volume](https://docs.docker.com/engine/tutorials/dockervolumes/#/data-volumes), or via a named volume if using a docker version \>= 1.9.

Config GitLab EE Server (`/etc/gitlab/config.rb`):

    # GitLab EE external URL.
    external_url "EXTERNAL_URL"
    
    # Prevent Postgres from trying to allocate 25% of total memory
    postgresql['shared_buffers'] = '1MB'
    
    # Disable Prometheus node_exporter inside Docker.
    node_exporter['enable'] = false
    
    # Manage accounts with docker.
    manage_accounts['enable'] = false
    
    # Explicitly disable init detection since we are running on a container.
    package['detect_init'] = false

Start GitLab EE Server:

    # Pull latest image
    docker pull alvistack/gitlab-ee-15.1
    
    # Run as detach
    docker run \
        -itd \
        --name gitlab-ee \
        --publish 22:22 \
        --publish 80:80 \
        --publish 443:443 \
        --volume /etc/gitlab:/etc/gitlab \
        --volume /var/log/gitlab:/var/log/gitlab \
        --volume /var/opt/gitlab:/var/opt/gitlab \
        alvistack/gitlab-ee-15.1

**Success**. GitLab EE is now available on <http://localhost:80>

## Upgrade

To upgrade to a more recent version of GitLab EE Server you can simply stop the GitLab EE container and start a new one based on a more recent image:

    docker stop gitlab-ee
    docker rm gitlab-ee
    docker run ... (see above)

As your data is stored in the data volume directory on the host, it will still be available after the upgrade.

Note: Please make sure that you don't accidentally remove the gitlab-ee container and its volumes using the -v option.

## Backup

For evaluations you can use the built-in database that will store its files in the GitLab EE Server home directory. In that case it is sufficient to create a backup archive of the directory on the host that is used as a volume (`/var/opt/gitlab` in the example above).

## Versioning

### `YYYYMMDD.Y.Z`

Release tags could be find from [GitHub Release](https://github.com/alvistack/docker-gitlab-ee/tags) of this repository. Thus using these tags will ensure you are running the most up to date stable version of this image.

### `YYYYMMDD.0.0`

Version tags ended with `.0.0` are rolling release rebuild by [GitLab pipeline](https://gitlab.com/alvistack/docker-gitlab-ee/-/pipelines) in weekly basis. Thus using these tags will ensure you are running the latest packages provided by the base image project.

## License

  - Code released under [Apache License 2.0](LICENSE)
  - Docs released under [CC BY 4.0](http://creativecommons.org/licenses/by/4.0/)

## Author Information

  - Wong Hoi Sing Edison
      - <https://twitter.com/hswong3i>
      - <https://github.com/hswong3i>
