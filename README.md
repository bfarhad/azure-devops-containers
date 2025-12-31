# azure-devops-containers
Default repo for distroless containers build and deployment of Azure container as DevOps agent

# Introduction
This repository contains Dockerfiles and Azure DevOps pipelines for building distroless container images based on Google's distroless images. This includes:

- **Java Images**: Amazon Corretto JDK containers
- **Tomcat Images**: Apache Tomcat application servers
- **DevOps Agent**: Multi-tool container for Azure DevOps pipeline agents

All images are built using distroless Debian for minimal attack surface and maximum security.

# Recent Updates - Version Upgrades

## Overview
All Docker images and pipelines have been updated to use the latest stable versions of Amazon Corretto (21) and Apache Tomcat (10) for improved security, performance, and support.

## Dockerfile Changes

### Corretto Images
- **Files Updated**: `dockerfiles/distroless-deb11-corretto`, `dockerfiles/distroless-debian11-corretta8`
- **Change**: Updated download URL from `amazon-corretto-8-x64-linux-jdk.tar.gz` to `amazon-corretto-21-x64-linux-jdk.tar.gz`
- **Impact**: Images now use Amazon Corretto 21 (latest LTS) instead of version 8

### Tomcat Images
- **Files Updated**: `dockerfiles/distroless-debian-tomcat`, `dockerfiles/distroless-debian11-corretta8` (in other repos)
- **Changes**:
  - Updated download URL from `tomcat-9/` to `tomcat-10/`
  - Changed `CATALINA_BASE` from `/opt/apache-tomcat-9` to `/opt/apache-tomcat-10`
  - Updated library paths and environment variables accordingly
- **Impact**: Images now use Apache Tomcat 10 instead of version 9

## Pipeline Changes

### distroless-debian-image.yml (Corretto Pipeline)
- **Parameter Updates**:
  - Default Corretto version changed from 8 to 21
  - Added 21 to selectable version values
- **Dockerfile Path**: Made dynamic by setting in build script instead of hardcoded path
- **File Renaming**: Renamed `distroless-deb11-corretto8` to `distroless-deb11-corretto` for version-agnostic usage
- **Documentation**: Updated comments to reflect Corretto 21 support

### distroless-debian-tomcat.yml (Tomcat Pipeline)
- **Parameter Updates**:
  - Default Tomcat version changed from 9 to 10
- **Build Script**: Modified to dynamically fetch Tomcat versions based on selected major version
- **Compatibility**: Maintains backward compatibility with older versions

### distroless-debian-wildfly.yml (Wildfly Pipeline)
- **Repository Name**: Updated from `distroless-deb11-corretto8` to `distroless-deb11-corretto`
- **Display Name**: Updated to reflect Corretto 21 usage

## Cross-Repository Updates
Similar changes were applied to related repositories:
- `3-mySub-p0c/clorg-distroless-buid/`
- `4-CLO-azuredevops/AzureDevOps/distroless-build/`
- `code/arca (2)/`

## DevOps Agent Container

### Overview
A specialized Alpine Linux container designed for Azure DevOps pipeline agents, containing essential DevOps and development tools.

### Included Tools
- **Terraform**: Latest stable version for infrastructure as code
- **Azure CLI**: Latest version for Azure resource management
- **PowerShell Core**: Latest version for cross-platform scripting
- **Python 3.12**: Latest stable Python with pip
- **Node.js LTS**: Latest LTS version with npm
- **.NET 8.0 SDK**: Latest .NET SDK for .NET development
- **Git**: Version control system
- **jq**: JSON processor
- **curl**: HTTP client

### Files
- **Dockerfile**: `dockerfiles/distroless-devops-agent`
- **Pipeline**: `distroless-devops-agent.yml`

### Usage
This container can be used as a base image for Azure DevOps self-hosted agents or as a tool container in pipelines requiring multiple development tools.

## Compatibility Notes
- All changes maintain backward compatibility
- Older versions remain selectable in pipeline parameters
- Dockerfiles are now version-agnostic where possible
- No breaking changes to existing deployments

# Getting Started
TODO: Guide users through getting your code up and running on their own system. In this section you can talk about:
1.	Installation process
2.	Software dependencies
3.	Latest releases
4.	API references

# Getting Started
TODO: Guide users through getting your code up and running on their own system. In this section you can talk about:
1.	Installation process
2.	Software dependencies
3.	Latest releases
4.	API references

# Build and Test
TODO: Describe and show how to build your code and run the tests. 

# Contribute
TODO: Explain how other users and developers can contribute to make your code better. 

If you want to learn more about creating good readme files then refer the following [guidelines](https://docs.microsoft.com/en-us/azure/devops/repos/git/create-a-readme?view=azure-devops). You can also seek inspiration from the below readme files:
- [ASP.NET Core](https://github.com/aspnet/Home)
- [Visual Studio Code](https://github.com/Microsoft/vscode)
- [Chakra Core](https://github.com/Microsoft/ChakraCore)