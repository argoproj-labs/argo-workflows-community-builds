# Argo Workflows Community Builds

This repository provides unofficial builds of Argo Workflows containers with BoringCrypto support for FIPS 140-2 compliance.

## Available Images

All images are built with `GOEXPERIMENT=boringcrypto` using the official Argo Workflows Dockerfile and build process.

### Latest Images (BoringCrypto)

```bash
# Workflow Controller
ghcr.io/argoproj-labs/argo-workflows-community-builds/workflow-controller:latest-fips

# Argo CLI  
ghcr.io/argoproj-labs/argo-workflows-community-builds/argocli:latest-fips

# Executor
ghcr.io/argoproj-labs/argo-workflows-community-builds/argoexec:latest-fips

# Executor (non-root)
ghcr.io/argoproj-labs/argo-workflows-community-builds/argoexec:latest-fips-nonroot
```

### Versioned Images

Specific versions follow the upstream pattern with `-fips` suffix:

```bash
# Example for v3.7.2
ghcr.io/argoproj-labs/argo-workflows-community-builds/workflow-controller:v3.7.2-fips
ghcr.io/argoproj-labs/argo-workflows-community-builds/argocli:v3.7.2-fips  
ghcr.io/argoproj-labs/argo-workflows-community-builds/argoexec:v3.7.2-fips
ghcr.io/argoproj-labs/argo-workflows-community-builds/argoexec:v3.7.2-fips-nonroot
```

## Usage

Replace the standard Argo Workflows images in your deployment:

### Workflow Controller Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: workflow-controller
spec:
  template:
    spec:
      containers:
      - name: workflow-controller
        image: ghcr.io/argoproj-labs/argo-workflows-community-builds/workflow-controller:latest-fips
```

### Workflow Controller ConfigMap

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: workflow-controller-configmap
data:
  config: |
    executor:
      image: ghcr.io/argoproj-labs/argo-workflows-community-builds/argoexec:latest-fips
```

### Argo CLI

```bash
# Pull and use the BoringCrypto CLI
docker run --rm -it ghcr.io/argoproj-labs/argo-workflows-community-builds/argocli:latest-fips version
```

## Automatic Updates

Images are automatically built daily to track the latest Argo Workflows releases. The workflow:

1. Checks for new releases from `argoproj/argo-workflows`
2. Clones the source code for each new version  
3. Builds all components (controller, executor, cli) with `GOEXPERIMENT=boringcrypto`
4. Creates multi-architecture manifests for linux/amd64 and linux/arm64
5. Pushes to the configured container registry

## New Architecture

This project introduces an enhanced build architecture for Argo Workflows with several key improvements:

### Multi-Architecture Support
- **Native ARM64 Support**: Full support for linux/arm64 alongside linux/amd64
- **Cross-Platform Builds**: Unified manifests enable seamless deployment across architectures
- **Performance Optimized**: Architecture-specific optimizations for both Intel and ARM processors

### Enhanced Security Framework
- **BoringCrypto Integration**: FIPS 140-2 validated cryptographic operations
- **Secure Build Pipeline**: Hardened GitHub Actions workflow with security scanning
- **Supply Chain Security**: Reproducible builds with attestation and SBOM generation

### Automated Release Management
- **Intelligent Version Tracking**: Automated detection and building of new upstream releases
- **Multi-Registry Distribution**: Flexible deployment to GitHub Container Registry, Quay.io, and Docker Hub
- **Rolling Update Strategy**: Builds only the latest 2 releases to maintain freshness while ensuring stability

### Container Optimization
- **Minimal Attack Surface**: Non-root executor variants for enhanced security posture
- **Layered Security**: Multi-stage builds optimized for size and security
- **Compliance Ready**: Pre-configured for regulated environments requiring FIPS compliance

This architecture provides a robust foundation for enterprise deployments while maintaining compatibility with standard Argo Workflows installations.

## BoringCrypto & FIPS Compliance

These builds include BoringCrypto, Google's FIPS 140-2 validated cryptographic module. Key benefits:

- **FIPS 140-2 Compliance**: Suitable for government and regulated environments
- **Validated Cryptography**: Uses BoringSSL instead of Go's standard crypto packages
- **Same Functionality**: Drop-in replacement for standard Argo Workflows images
- **Regular Updates**: Automatically tracks upstream releases

The `GOEXPERIMENT=boringcrypto` build flag ensures all cryptographic operations use the FIPS-validated BoringSSL library.

## Build Process

The images are built using the exact same process as upstream Argo Workflows:

- Uses the official Dockerfile from `argoproj/argo-workflows`
- Uses the same build targets: `workflow-controller`, `argocli`, `argoexec`, `argoexec-nonroot`
- Follows the same multi-stage build process
- Only adds the `GOEXPERIMENT=boringcrypto` build argument
- Creates the same multi-architecture manifests (linux/amd64, linux/arm64)

## Supported Architectures

- linux/amd64
- linux/arm64

## Registry Support

Images can be pushed to multiple registries:
- GitHub Container Registry (ghcr.io) - default
- Quay.io (quay.io)  
- Docker Hub (docker.io)

## Manual Builds

You can trigger manual builds using GitHub Actions workflow dispatch:

1. Go to the Actions tab
2. Select "Build and Push Argo Workflows (BoringCrypto)"
3. Click "Run workflow"
4. Optionally specify a specific version and registry

## License

This project follows the same Apache 2.0 license as Argo Workflows.
