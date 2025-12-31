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
- `clorg-distroless-buid/`
- `AzureDevOps/distroless-build/`
- `arca/`

## DevOps Agent Container

### Overview
A specialized Alpine Linux container designed for Azure DevOps pipeline agents, containing essential DevOps and development tools.

### Included Tools
- **Terraform 1.9.8**: Latest stable version for infrastructure as code (updated for CVE-2024-45337 fix)
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

## Latest Security Updates (2025-12-31)

### CVE-2024-45337 Fix
- **Issue**: Authorization bypass vulnerability in golang.org/x/crypto/ssh
- **Fix**: Updated Terraform from 1.9.0 to 1.9.8, which includes patched crypto libraries
- **Impact**: Resolves critical security vulnerability in SSH authentication

### Tomcat Dockerfile Fixes
- **Issue**: Build failures due to undefined variables and incorrect download URLs
- **Fixes**:
  - Changed Tomcat download URL to `https://archive.apache.org/dist/tomcat/tomcat-10/v10.1.24/bin/apache-tomcat-10.1.24.tar.gz`
  - Corrected `LD_LIBRARY_PATH` ENV syntax to use `key=value` format
  - Removed undefined `$CATALINA_OPTS` and `$JAVA_OPTS` variables from `_JAVA_OPTIONS`
- **Impact**: Tomcat container now builds successfully

### Pipeline Alignment
- **Issue**: Inconsistent Trivy scanning configurations between pipelines
- **Fix**: Standardized Trivy scan scripts, pool configurations, and artifact publishing
- **Impact**: Consistent security reporting across all container builds

## Compatibility Notes
- All changes maintain backward compatibility
- Older versions remain selectable in pipeline parameters
- Dockerfiles are now version-agnostic where possible
- No breaking changes to existing deployments

# Getting Started

## Prerequisites
- Docker 20.10 or later
- Git
- (Optional) Azure CLI for container registry access

## Manual Build Instructions

### Building Tomcat Container

1. Clone the repository and navigate to the dockerfiles directory:
   ```bash
   git clone <repository-url>
   cd azure-devops-containers/dockerfiles
   ```

2. Build the Tomcat image:
   ```bash
   docker build -t distroless-debian-tomcat -f distroless-debian-tomcat .
   ```

3. Verify the build:
   ```bash
   docker images | grep distroless-debian-tomcat
   ```

4. Run the container (optional):
   ```bash
   docker run -p 8080:8080 distroless-debian-tomcat
   ```

### Building DevOps Agent Container

1. Navigate to the dockerfiles directory:
   ```bash
   cd azure-devops-containers/dockerfiles
   ```

2. Build the devops agent image:
   ```bash
   docker build -t distroless-devops-agent -f distroless-devops-agent .
   ```

### Building Base Corretto Images

For base Amazon Corretto images, use the respective Dockerfiles:

```bash
# Build Corretto 21 base image
docker build -t distroless-base-debian11-corretto21 -f distroless-deb11-corretto .
```

## CI/CD Pipeline Usage

The repository includes Azure DevOps pipelines for automated building:

- `distroless-debian-image.yml`: Builds base Corretto images with security scanning
- `distroless-debian-tomcat.yml`: Builds Tomcat images with security scanning

Both pipelines include Trivy vulnerability scanning and publish results to Azure DevOps.

# Build and Test

## Local Testing

### Running Containers Locally

1. **Tomcat Container**:
   ```bash
   docker run -d -p 8080:8080 --name tomcat-test distroless-debian-tomcat
   curl http://localhost:8080
   ```

2. **DevOps Agent Container**:
   ```bash
   docker run -it --rm distroless-devops-agent /bin/sh
   # Inside container, test tools:
   terraform --version
   pwsh --version
   dotnet --version
   ```

### Security Scanning

Install Trivy locally and scan built images:

```bash
# Install Trivy
wget https://github.com/aquasecurity/trivy/releases/download/v0.50.1/trivy_0.50.1_Linux-64bit.tar.gz
tar -xzf trivy_0.50.1_Linux-64bit.tar.gz
sudo mv trivy /usr/local/bin/

# Scan image
trivy image --format table --severity HIGH,CRITICAL distroless-debian-tomcat
```

## Pipeline Testing

- Push changes to trigger Azure DevOps pipelines
- Monitor build logs and security scan results
- Review published test results for vulnerabilities

# Contribute
TODO: Explain how other users and developers can contribute to make your code better. 

If you want to learn more about creating good readme files then refer the following [guidelines](https://docs.microsoft.com/en-us/azure/devops/repos/git/create-a-readme?view=azure-devops). You can also seek inspiration from the below readme files:
- [ASP.NET Core](https://github.com/aspnet/Home)
- [Visual Studio Code](https://github.com/Microsoft/vscode)
- [Chakra Core](https://github.com/Microsoft/ChakraCore)