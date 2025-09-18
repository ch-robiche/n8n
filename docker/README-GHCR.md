# Docker GitHub Container Registry Workflow

This document describes the GitHub Actions workflow for building and publishing n8n Docker images to GitHub Container Registry (GHCR).

## Workflow: `docker-publish-ghcr.yml`

### Triggers

1. **Manual Dispatch** (`workflow_dispatch`):
   - Can be triggered manually from the GitHub Actions tab
   - Optional inputs:
     - `tag`: Docker tag for the image (default: `latest`)
     - `push_enabled`: Whether to push the image (default: `true`)

2. **Push to main/master**:
   - Automatically triggers when changes are pushed to main or master branches
   - Only triggers if changes affect:
     - `docker/**` files
     - `packages/**` files
     - The workflow file itself

### What it does

1. **Multi-platform build**: Builds for both AMD64 and ARM64 architectures
2. **Build n8n**: Compiles the n8n application using `pnpm build:n8n`
3. **Docker build**: Creates Docker images using the existing `docker/images/n8n/Dockerfile`
4. **Push to GHCR**: Publishes images to `ghcr.io/your-username/n8n`
5. **Multi-arch manifest**: Creates a unified manifest supporting both architectures
6. **Security scan**: Runs Trivy vulnerability scanner and uploads results to GitHub Security tab

### Image Tags

The workflow automatically creates multiple tags:
- Branch name (e.g., `main`, `feature-branch`)
- Custom tag (if specified via manual dispatch)
- Commit SHA with branch prefix (e.g., `main-abc1234`)

### Usage

#### Manual Build

1. Go to the "Actions" tab in your GitHub repository
2. Select "Docker: Build and Publish to GHCR"
3. Click "Run workflow"
4. Optionally specify a custom tag
5. Click "Run workflow"

#### Automatic Build

Simply push changes to the main/master branch that affect Docker or package files.

### Permissions Required

The workflow requires the following permissions:
- `contents: read` - To checkout the repository
- `packages: write` - To push images to GHCR
- `security-events: write` - To upload security scan results

### Environment Variables

- `REGISTRY`: Set to `ghcr.io`
- `IMAGE_NAME`: Automatically set to your GitHub repository name

### Accessing Published Images

After a successful build, you can pull the image using:

```bash
docker pull ghcr.io/your-username/n8n:latest
```

Replace `your-username` with your GitHub username and `latest` with your desired tag.

### Security

- Images are automatically scanned for vulnerabilities using Trivy
- Scan results are uploaded to GitHub Security tab
- Only authenticated users can push images
- Images are signed and include SBOMs for enhanced security