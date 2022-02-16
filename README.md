
## DevOps infrastructure for lab development


This repository contains instructions for creating a basic DevOps infrastructure able to implement CI/CD pipelines.

In order to allow to create such infrastructure in lab environments docker has been used as runtime for them creating the required docker-compose files for each one.

The tools included are:

- [gitlab](gitlab), that will act as a git provider and allows the definition of worflow pipelines using a descriptive language.

- [nexus](nexus), one of the most extented tools for hosting artifact repositories supporting a wide range of languages (java, npm, Docker, etc).

- [registry](registry), the most simple docker registry available based implemented by Docker themshelves.

- [sonarQube](sonar), may be the most used tool for quality assurance allowing integration will a pletora of languages. 

For every docker-compose file an *.env* file has been included that will allow to configure the tools. This includes tool version, volumes location, etc.

Since in most cases only basic tool installation is provided, it´s recommended to reviw the tool documentation. In the reference section links for every tool are provided.

# General configuration

If the environment is instantiated using only one machine it will be required to include the DNS entries required to resolve the DNS for each tool (i.e. modifying /etc/hosts file in osx or linux distributions)

The host system for docker and the images used must be supported, *amd64* is the recommended one.

# Requirements
 
Host Machine

- Docker
- Docker Compose

Internet Access.

# Gitlab
This repository includes the required elements to creeate a CI/CD enviroment in a lab environment. It' based in docker-compose that provides a flexible and customizable way to adapt it to the environment requirements.

### Gitlab

The compose file provided allows to bring up a full gitlab instance including CI capabilities that will allow to define the required workflows.

It also brings up a *runner* that will be responsible of executing the workflows defined.

Instructions for creating external runners have been included. In this case for a Rapsberry Pi (Buster) since its architecture it's not supported in amd64 and executing or building docker images requires it.

An .env file is included for customizing:
- Tools version
- Volumes location.

### Nexus

The compose file provided bring up a full Nexus OSS instance exposing ports:
- 8081 for artifacts
- 5000 & 5001 for docker registry.  

An .env file is included for customizing:
- Tools version
- Volumes location.

*Notes*

If not ussing trusted certificates it's required to modify docker options file */etc/docker/daemon.json* to include the registries as trusted unsecured.


```
"insecure-registries" : [
     "registry.nexus",
     "http://registry.nexus:5000"
   ]
```

### SonnarQube

The compose file provided bring up a basic SonarQube instance that includes the plugin  OSS instance exposing ports:
- 8081 for artifacts
- 5000 & 5001 for docker registry.  

An .env file is included for customizing:
- Tools version
- Volumes location.

The docker-compose file includes the definition of an external postgre database that will be used for SonarQube.

### Baremetal Gitlab Runner

In some cases is required to install a baremetal gitlab runner. In most cases is related with special requirements of the tasks to executo o the architecture required to build an artifact.

#### Gitlab Runner Raspbian Buster

Gitlab runner is sopported in many platforms, using the docker image provided by Gitlab or using the binary provided by Gitlab.  
Now the installation of a gitlab runner in a Raspberry PI (Buster) are provided.

**Steps** 

1. Install git

```ssh
sudo apt-get update && sudo apt-get upgrade

sudo apt-get install git

```

Once installed the succesful installation can be verified

```ssh
git --version
```

2. If the runner requires docker

```ssh

curl -sSL https://get.docker.com | sh

sudo usermod -aG docker pi

newgroup docker
```

3. Install gitlab runner

Follow the instructions provided by GitLab in this [link](https://docs.gitlab.com/runner/install/linux-manually.html)

#For Raspberry Pi Buster

```ssh
sudo curl -L --output /usr/local/bin/gitlab-runner "https://gitlab-runner-downloads.s3.amazonaws.com/latest/binaries/gitlab-runner-linux-arm"

sudo chmod +x /usr/local/bin/gitlab-runner

sudo useradd --comment 'GitLab Runner' --create-home gitlab-runner --shell /bin/bash

sudo gitlab-runner install --user=gitlab-runner --working-directory=/home/gitlab-runner

sudo gitlab-runner start
```

#For Raspberry Pi Linux arm64

```ssh
sudo curl -L --output /usr/local/bin/gitlab-runner "https://gitlab-runner-downloads.s3.amazonaws.com/latest/binaries/gitlab-runner-linux-arm64"

sudo chmod +x /usr/local/bin/gitlab-runner

sudo useradd --comment 'GitLab Runner' --create-home gitlab-runner --shell /bin/bash

sudo gitlab-runner install --user=gitlab-runner --working-directory=/home/gitlab-runner

sudo gitlab-runner start
```

Then an executor can be registered in the runner. Since there are several options and executor types is recomended to check GitLab [documentation](https://docs.gitlab.com/runner/executors/)

Oneliner for registering an executor (docker based)

```ssh
sudo gitlab-runner register \
  --non-interactive \
  --url "https://gitlab.com/" \
  --registration-token "$REGISTRATION_TOKEN" \
  --executor "docker" \
  --docker-image alpine:latest \
  --description "docker-runner" \
  --tag-list "docker" \
  --run-untagged="true" \
  --locked="false" \
  --access-level="not_protected"
```

4. Happy ruuning

*Note*

If using docker an receiving the following message:

```
failed to find memory cgroup
```

It's required to modify boot options:

- Raspbian Buster

  Modify */boot/cmdline.txt* adding

    ```
    cgroup_memory=1 cgroup_enable=memory
    ```

- Ubuntu 21LTS

    Modify */boot/firmware/cmdline.txt* adding

    ```
    cgroup_enable=cpuset cgroup_enable=memory cgroup_memory=1
    ```


## Usage

For every tool a *docker-compose* file so starting, stoping, etc. can be done the usual way

```ssh
$ docker-compose up
$ docker-compose down
$ docker-compose rm -v #remove orphaned volumes.
```

## References

- Gitlab
    - [Reference](https://about.gitlab.com/)
    - [Docker](https://hub.docker.com/r/gitlab/gitlab-ce)
    - [Gitlab Runner](https://docs.gitlab.com/runner/)
- Nexus
    - [Reference](https://www.sonatype.com/products/repository-oss)
    - [Docker](https://hub.docker.com/r/sonatype/nexus3)
- Registry
    - [Reference](https://docs.docker.com/registry)
    - [Docker](https://hub.docker.com/_/registry)

- SonarQube
    - [Reference](https://docs.sonarqube.org/latest/)
    - [Docker](https://hub.docker.com/_/sonarqube)

## TODO  

[ ] Automatic provissioning of gitlab runners  
[ ] Omnibus configuration for GitlabCI
