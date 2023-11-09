# GHA to build and publish a 🛢️ container

## Usage

```yaml
on:
  - push

jobs:
  build_job:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      packages: write
    steps:
      - name: Build and publish a container
        uses: voxpupuli/gha-build-and-publish-a-container@v2
        with:
          registry: docker.io                 # Default: ghcr.io
          registry_username: foobar           # Default: github.repository_owner
          registry_password: "P4SSw0rd!"      # No default, for github set it to ${{ secrets.GITHUB_TOKEN }}
          build_arch: linux/amd64,linux/arm64 # Default: linux/amd64
          build_args: |                       # No Default, can be empty
            PUPPET_VERSION=8.2.0
            PUPPET_RELEASE=8
          build_context: 'puppetdb'           # Default: .
          buildfile: Dockerfile.something     # Default: Dockerfile
          publish: 'false'                    # Default: 'true'
          docker_username: 'someone'          # No default, can be empty
          docker_password: 'docker hub pass'  # No default, can be empty
          tags: |                             # No default, can be empty
            docker.io/${{ env.REPOSITORY }}:3.2
            docker.io/${{ env.REPOSITORY }}:latest
            ghcr.io/${{ env.REPOSITORY }}:3.2
            ghcr.io/${{ env.REPOSITORY }}:latest
```

Test container build in ci:

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      packages: read
    steps:
    - name: Test container build
      uses: voxpupuli/gha-build-and-publish-a-container@v2
      with:
        github_token: ${{ secrets.GITHUB_TOKEN }}
        publish: 'false'
        tags: ci/test:dummy
```

# Behavior

## Tagging

The `main`-branch will always be tagged with the `development` container-tag.
If one does a git-tag like `v1.0.0` this will translate into `1.0.0` container-tag.
The last git-tag also will be tagged with the `latest` container-tag.

## Push to hub.docker.com

If you specify a docker_username and a docker_password, the action will do a second registry login.
It will then automatically also log in to docker.io and on push the image gets published on your
primary registry and additionally to hub.docker.com.
