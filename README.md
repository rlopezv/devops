# DevOps Infrastructure

This repository contains instructions for creating a basic DevOps infrastructure able to implement CI/CD pipelines.

In order to allow to create such infrastructure in lab environments docker has been used as runtime for them createing the required docker-compose files for each one.

The tools included are:

- Gitlab
- Gitlab Runner
- Nexus
- Sonar

It will also include instructions to create a RaspberryPi Gitlab Runner for creating arm/v7 iamges.

# Requirements
 
Host Machine

- Docker
- Docker Compose

Internet Access to the following URLS:

# General configuration

If the environment is instantiated using only one machine it will be required to include the DNS entries required to resolve the DNS for each tool (i.e. modifying /etc/hosts file in osx or linux distributions)

# Tools

# Nexus

# SonarQube

# Gitlab

