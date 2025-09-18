# Argo Workflows Community Builds

This repository provides unofficial builds of Argo Workflows containers with BoringCrypto support for FIPS 140-2 compliance.

## Available Images

All images are built with `GOEXPERIMENT=boringcrypto` using the official Argo Workflows Dockerfile and build process.

### Latest Images (BoringCrypto)

```bash
# Workflow Controller
ghcr.io/argoproj-labs/argo-workflows-community-builds/workflow-controller:latest-boringcrypto

# Argo CLI
ghcr.io/argoproj-labs/argo-workflows-community-builds/argocli:latest-boringcrypto

# Executor
ghcr.io/argoproj-labs/argo-workflows-community-builds/argoexec:latest-boringcrypto

# Executor (non-root)
ghcr.io/argoproj-labs/argo-workflows-community-builds/argoexec:latest-boringcrypto-nonroot
```

### Versioned Images

Specific versions follow the upstream pattern with `-boringcrypto` suffix:

```bash
# Example for v3.7.2
ghcr.io/argoproj-labs/argo-workflows-community-builds/workflow-controller:v3.7.2-boringcrypto
ghcr.io/argoproj-labs/argo-workflows-community-builds/argocli:v3.7.2-boringcrypto
ghcr.io/argoproj-labs/argo-workflows-community-builds/argoexec:v3.7.2-boringcrypto
ghcr.io/argoproj-labs/argo-workflows-community-builds/argoexec:v3.7.2-boringcrypto-nonroot
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
        image: ghcr.io/argoproj-labs/argo-workflows-community-builds/workflow-controller:latest-boringcrypto
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
      image: ghcr.io/argoproj-labs/argo-workflows-community-builds/argoexec:latest-boringcrypto
```

### Argo CLI

```bash
# Pull and use the BoringCrypto CLI
docker run --rm -it ghcr.io/argoproj-labs/argo-workflows-community-builds/argocli:latest-boringcrypto version
```

## Automatic Updates

Images are automatically built daily to track the latest Argo Workflows releases. The workflow:

1. Checks for new releases from `argoproj/argo-workflows`
2. Clones the source code for each new version
3. Builds all components (controller, executor, cli) with `GOEXPERIMENT=boringcrypto`
4. Creates multi-architecture manifests for linux/amd64 and linux/arm64
5. Pushes to the configured container registry

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
