# Deploying to DigitalOcean App Platform via GitHub Actions

Here are some examples of GitHub Actions workflows for deploying to DigitalOcean App Platform:

## Basic Deployment

This example deploys your app on every push to the main branch:

```yaml
name: Deploy to DigitalOcean App Platform

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Deploy to DigitalOcean App Platform
        uses: digitalocean/app_action/deploy@v2
        with:
          token: ${{ secrets.DIGITALOCEAN_ACCESS_TOKEN }}
```

### Build and Deploy Docker Image

This workflow builds a Docker image, pushes it to GitHub Container Registry, and then deploys it to App Platform:

```yaml
name: Build and Deploy to App Platform

on:
  push:
    branches:
      - main

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Build and push Docker image
        uses: docker/build-push-action@v5
        with:
          push: true
          tags: ghcr.io/${{ github.repository }}:${{ github.sha }}

      - name: Deploy to DigitalOcean App Platform
        uses: digitalocean/app_action/deploy@v2
        env:
          IMAGE_TAG: ${{ github.sha }}
        with:
          token: ${{ secrets.DIGITALOCEAN_ACCESS_TOKEN }}
```

### Pull Request Preview Deployment

This workflow creates a preview deployment for pull requests:

```yaml
name: PR Preview Deployment

on:
  pull_request:
    branches:
      - main

jobs:
  preview:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Deploy Preview to DigitalOcean App Platform
        id: deploy
        uses: digitalocean/app_action/deploy@v2
        with:
          token: ${{ secrets.DIGITALOCEAN_ACCESS_TOKEN }}
          deploy_pr_preview: "true"

      - name: Comment PR
        uses: actions/github-script@v7
        if: success()
        with:
          github-token: ${{secrets.GITHUB_TOKEN}}
          script: |
            github.rest.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.name,
              body: 'Preview deployed to: ${{ steps.deploy.outputs.app_url }}'
            })
```

## Specification for digitalocean/app_action@v2

The `digitalocean/app_action@v2` action provides several input parameters and outputs. Here's a detailed specification:

### Inputs

- `token` (required): Your DigitalOcean Personal Access Token[1][2].
- `app_spec_location` (optional): Location of the app spec file. Defaults to `.do/app.yaml`[3].
- `project_id` (optional): ID of the project to deploy the app to[3].
- `app_name` (optional): Name of the existing app to update[3].
- `print_build_logs` (optional): Print build logs. Defaults to `false`[3].
- `print_deploy_logs` (optional): Print deploy logs. Defaults to `false`[3].
- `deploy_pr_preview` (optional): Deploy a preview for pull requests. Defaults to `false`[5].

### Outputs

- `app`: JSON object containing the app's metadata[3].
- `build_logs`: Build logs from the deployment[3].
- `deploy_logs`: Deploy logs from the deployment[3].
- `app_url`: URL of the deployed app (for preview deployments)[5].

### Usage Tips

1. Store your DigitalOcean Access Token as a GitHub secret and reference it in your workflow[1][2].

2. You can use environment variables in your app spec file by referencing them with `${VARIABLE_NAME}` syntax[6].

3. For Docker-based deployments, you can update the image tag in your app spec using environment variables[6].

4. Use the `deploy_pr_preview` option to create temporary preview deployments for pull requests[5].

5. Utilize the action's outputs to create custom integrations or notifications in your workflow[6].

6. If you're using an in-repository app spec, make sure to turn off `deploy_on_push` in your app spec file to avoid conflicts with the GitHub Actions deployment[6].

By following these examples and specifications, you can effectively use GitHub Actions to deploy your applications to DigitalOcean App Platform, streamlining your CI/CD process and enabling automated deployments with each push or pull request.

## DigitalOcean doctl GitHub Action Cheat Sheet (v2.5.1)

### Basic Usage

```yaml
- name: Install doctl
  uses: digitalocean/action-doctl@v2.5.1
  with:
    token: ${{ secrets.DIGITALOCEAN_ACCESS_TOKEN }}
```

### Key Features

- Installs the latest version of `doctl` CLI
- Authenticates with DigitalOcean using the provided token
- Makes `doctl` available for use in subsequent steps

### Configuration Options

| Option    | Required | Description                                     | Default |
|-----------|----------|-------------------------------------------------|---------|
| token     | Yes*     | DigitalOcean API token                          | -       |
| version   | No       | Specific version of doctl to install            | Latest  |
| no_auth   | No       | Skip authentication step                        | false   |

*Not required if `no_auth` is set to true

### Common Use Cases

1. **Retrieve Kubernetes Config**

```yaml
- name: Save DigitalOcean kubeconfig
  run: doctl kubernetes cluster kubeconfig save my-cluster
```

2. **List Droplets**

```yaml
- name: List Droplets
  run: doctl compute droplet list
```

3. **Create a new Droplet**

```yaml
- name: Create Droplet
  run: doctl compute droplet create my-droplet --size s-1vcpu-1gb --image ubuntu-20-04-x64 --region nyc1
```

4. **Deploy to App Platform**

```yaml
- name: Deploy to App Platform
  run: doctl apps create --spec app-spec.yaml
```

### Advanced Usage

1. **Using a specific doctl version**

```yaml
- name: Install doctl
  uses: digitalocean/action-doctl@v2.5.1
  with:
    token: ${{ secrets.DIGITALOCEAN_ACCESS_TOKEN }}
    version: 1.84.0
```

2. **Skip authentication (for public operations)**

```yaml
- name: Install doctl without auth
  uses: digitalocean/action-doctl@v2.5.1
  with:
    no_auth: true
```

3. **Combine with other actions**

```yaml
- name: Install doctl
  uses: digitalocean/action-doctl@v2.5.1
  with:
    token: ${{ secrets.DIGITALOCEAN_ACCESS_TOKEN }}

- name: Build and push Docker image
  uses: docker/build-push-action@v2
  with:
    push: true
    tags: registry.digitalocean.com/my-registry/my-image:${{ github.sha }}

- name: Deploy to Kubernetes
  run: |
    doctl kubernetes cluster kubeconfig save my-cluster
    kubectl apply -f k8s-manifests/
```

### Best Practices

1. Store your DigitalOcean API token as a GitHub secret
2. Use specific versions of the action for stability
3. Combine `doctl` commands with other GitHub Actions for comprehensive workflows
4. Use `no_auth: true` for operations that don't require authentication
5. Leverage `doctl`'s various commands to manage DigitalOcean resources effectively

### Notes

- The action now uses Node.js 20 for improved performance and security
- Supports non-amd64 architectures as of v2.4.0
- The token is automatically registered as a secret in the GitHub Actions environment

This cheat sheet covers the key aspects of using the digitalocean/action-doctl@v2.5.1 in your GitHub Actions workflows, providing a quick reference for common use cases and configuration options[1][2][4][5].

Citations:
[1] https://docs.digitalocean.com/reference/doctl/reference/compute/action/
[2] https://github.com/marketplace/actions/github-action-for-digitalocean-doctl
[3] https://docs.digitalocean.com/reference/doctl/reference/compute/action/list/
[4] https://github.com/digitalocean/action-doctl/actions
[5] https://github.com/digitalocean/action-doctl/releases


## DigitalOcean App Platform App Specification Structure (app.yaml)

Here's a list of the main sections and their possible fields:

### Top-level fields

- `name`: String (2-32 characters, lowercase alphanumeric and dashes)
- `region`: String (e.g., "nyc", "ams", "sfo")
- `services`: Array of service objects
- `static_sites`: Array of static site objects
- `databases`: Array of database objects
- `domains`: Array of domain objects
- `envs`: Array of environment variable objects
- `alerts`: Array of alert objects

### Services

Each service object can have the following fields:

- `name`: String (2-32 characters, lowercase alphanumeric and dashes)
- `git`: Object (for Git-based deployments)
  - `repo_clone_url`: String
  - `branch`: String
  - `repo_name`: String
- `github`: Object (for GitHub-based deployments)
  - `repo`: String
  - `branch`: String
  - `deploy_on_push`: Boolean
- `gitlab`: Object (for GitLab-based deployments)
  - `repo`: String
  - `branch`: String
  - `deploy_on_push`: Boolean
- `image`: Object (for container-based deployments)
  - `registry_type`: String ("DOCKER_HUB" or "DOCR")
  - `registry`: String
  - `repository`: String
  - `tag`: String
- `dockerfile_path`: String
- `build_command`: String
- `run_command`: String
- `source_dir`: String
- `envs`: Array of environment variable objects
- `environment_slug`: String (e.g., "node-js", "python", "go")
- `instance_count`: Integer
- `instance_size_slug`: String (e.g., "basic-xxs", "professional-xs")
- `health_check`: Object
  - `http_path`: String
  - `initial_delay_seconds`: Integer
  - `period_seconds`: Integer
- `http_port`: Integer
- `internal_ports`: Array of integers
- `routes`: Array of route objects
  - `path`: String
  - `preserve_path_prefix`: Boolean
- `cors`: Object
  - `allow_origins`: Array of strings
  - `allow_methods`: Array of strings
  - `allow_headers`: Array of strings
  - `expose_headers`: Array of strings
  - `max_age`: String
  - `allow_credentials`: Boolean

### Static Sites

Static site objects can have these fields:

- `name`: String (2-32 characters, lowercase alphanumeric and dashes)
- `git`: Object (same as in Services)
- `github`: Object (same as in Services)
- `gitlab`: Object (same as in Services)
- `dockerfile_path`: String
- `build_command`: String
- `output_dir`: String
- `index_document`: String
- `error_document`: String
- `catchall_document`: String
- `environment_slug`: String
- `envs`: Array of environment variable objects

### Databases

Database objects can include:

- `name`: String (2-32 characters, lowercase alphanumeric and dashes)
- `engine`: String ("MYSQL", "PG", "REDIS", "MONGODB", "KAFKA", "OPENSEARCH")
- `version`: String
- `production`: Boolean
- `cluster_name`: String
- `db_name`: String
- `db_user`: String

### Domains

Domain objects have these fields:

- `domain`: String
- `type`: String ("DEFAULT", "PRIMARY", "ALIAS")
- `zone`: String
- `wildcard`: Boolean

### Environment Variables

Environment variable objects consist of:

- `key`: String
- `value`: String
- `scope`: String ("RUN_TIME", "BUILD_TIME", "RUN_AND_BUILD_TIME")
- `type`: String ("GENERAL" or "SECRET")

### Alerts

Alert objects can have:

- `rule`: String (e.g., "DEPLOYMENT_FAILED", "DOMAIN_FAILED")
- `disabled`: Boolean

## Example app.yaml Structure

Here's a basic example of how an app.yaml file might be structured:

```yaml
name: my-app
region: nyc
services:
  - name: web
    github:
      repo: username/repo
      branch: main
    build_command: npm run build
    run_command: npm start
    environment_slug: node-js
    instance_size_slug: basic-xxs
    instance_count: 1
    routes:
      - path: /api
    envs:
      - key: NODE_ENV
        value: production
static_sites:
  - name: frontend
    github:
      repo: username/frontend-repo
      branch: main
    build_command: npm run build
    output_dir: build
databases:
  - name: main-db
    engine: PG
    version: "12"
domains:
  - domain: example.com
    type: PRIMARY
alerts:
  - rule: DEPLOYMENT_FAILED
    disabled: false
```

This reference covers the main fields and structures you can use in your app.yaml file for DigitalOcean App Platform deployments[1][2]. Remember that some fields may have additional options or constraints, so always refer to the most up-to-date DigitalOcean documentation for the complete and current specification.

Citations:
[1] https://docs.digitalocean.com/products/app-platform/reference/app-spec/
[2] https://docs.digitalocean.com/products/app-platform/how-to/create-apps/

When deploying to DigitalOcean App Platform using GitHub Actions and doctl, with Docker Hub hosting private images, there are several important considerations and tips to ensure a smooth deployment process. Here's a comprehensive guide to address your specific situation:

## Authentication and Access

The error you're encountering suggests an authentication issue with your private Docker Hub images. To resolve this:

1. **Docker Hub Credentials**: Ensure you have valid Docker Hub credentials with access to the private images[3].

2. **Secrets Management**: Store your Docker Hub credentials as secrets in your GitHub repository[2]. Add the following secrets:
   - `DOCKERHUB_USERNAME`
   - `DOCKERHUB_TOKEN` (use an access token instead of your password for better security)

3. **Registry Credentials**: In your App Platform specification, include the registry credentials[3][5]. The format should be:

   ```yaml
   image:
     registry_type: DOCKER_HUB
     registry: hub.docker.com
     repository: your-repo/your-image
     tag: latest
     registry_credentials: "${DOCKERHUB_USERNAME}:${DOCKERHUB_TOKEN}"
   ```

## GitHub Actions Workflow

Set up your GitHub Actions workflow to handle the deployment:

1. **Checkout Repository**:
   ```yaml
   - name: Checkout repository
     uses: actions/checkout@v4
   ```

2. **Install doctl**:
   ```yaml
   - name: Install doctl
     uses: digitalocean/action-doctl@v2
     with:
       token: ${{ secrets.DIGITALOCEAN_ACCESS_TOKEN }}
   ```

3. **Authenticate with Docker Hub**:
   ```yaml
   - name: Log in to Docker Hub
     uses: docker/login-action@v3.3.0
     with:
       username: ${{ secrets.DOCKERHUB_USERNAME }}
       password: ${{ secrets.DOCKERHUB_TOKEN }}
   ```

4. **Deploy to App Platform**:
   ```yaml
   - name: Deploy to DigitalOcean App Platform
     run: |
       doctl apps create --spec .do/app.yaml
   ```

## App Specification

In your `.do/app.yaml` file, ensure you're correctly specifying the Docker Hub image:

```yaml
name: your-app-name
services:
  - name: web
    image:
      registry_type: DOCKER_HUB
      registry: hub.docker.com
      repository: your-repo/your-image
      tag: latest
      registry_credentials: "${DOCKERHUB_USERNAME}:${DOCKERHUB_TOKEN}"
```

## Additional Tips

1. **Image Verification**: Double-check that the image exists in Docker Hub and that your credentials have access to it[5].

2. **Tag Specificity**: Use specific image tags rather than `latest` to ensure you're deploying the correct version[3].

3. **DigitalOcean Access Token**: Ensure your `DIGITALOCEAN_ACCESS_TOKEN` has the necessary permissions to create and manage apps[4].

4. **Error Logging**: If issues persist, enable verbose logging in your GitHub Actions workflow to get more detailed error messages.

5. **App Platform Limitations**: Be aware that App Platform doesn't support auto-deployment of images from Docker Hub. You may need to manually trigger deployments or use a custom solution[3].

6. **Alternative Approach**: Consider pushing your Docker image to DigitalOcean Container Registry first, then deploying from there. This can sometimes be more reliable for App Platform deployments[1][6].


## Cheat Sheet for docker/build-push-action@v5 in GitHub Actions

### Basic Usage

```yaml
- name: Build and push Docker image
  uses: docker/build-push-action@v5
  with:
    context: .
    push: true
    tags: user/app:latest
```

### Key Parameters

- `context`: Build context (default: .)
- `file`: Path to Dockerfile (default: ./Dockerfile)
- `push`: Push the image to registry (default: false)
- `tags`: Image tags (comma-separated)
- `platforms`: Target platforms for multi-platform build (e.g., linux/amd64,linux/arm64)

### Authentication

```yaml
- name: Login to Docker Hub
  uses: docker/login-action@v3
  with:
    username: ${{ secrets.DOCKERHUB_USERNAME }}
    password: ${{ secrets.DOCKERHUB_TOKEN }}
```

### Build Arguments

```yaml
- name: Build and push
  uses: docker/build-push-action@v5
  with:
    build-args: |
      ARG1=value1
      ARG2=value2
```

### Caching

```yaml
- name: Build and push
  uses: docker/build-push-action@v5
  with:
    cache-from: type=registry,ref=user/app:buildcache
    cache-to: type=registry,ref=user/app:buildcache,mode=max
```

### Multi-Platform Builds

```yaml
- name: Set up QEMU
  uses: docker/setup-qemu-action@v3
- name: Set up Docker Buildx
  uses: docker/setup-buildx-action@v3
- name: Build and push
  uses: docker/build-push-action@v5
  with:
    platforms: linux/amd64,linux/arm64
```

### Metadata

```yaml
- name: Docker meta
  id: meta
  uses: docker/metadata-action@v5
  with:
    images: user/app
- name: Build and push
  uses: docker/build-push-action@v5
  with:
    tags: ${{ steps.meta.outputs.tags }}
    labels: ${{ steps.meta.outputs.labels }}
```

### Outputs

```yaml
- name: Build and push
  id: docker_build
  uses: docker/build-push-action@v5
  with:
    push: true
    tags: user/app:latest
- name: Image digest
  run: echo ${{ steps.docker_build.outputs.digest }}
```

### GitHub Container Registry (GHCR)

```yaml
- name: Login to GHCR
  uses: docker/login-action@v3
  with:
    registry: ghcr.io
    username: ${{ github.actor }}
    password: ${{ secrets.GITHUB_TOKEN }}
- name: Build and push
  uses: docker/build-push-action@v5
  with:
    push: true
    tags: ghcr.io/${{ github.repository }}:latest
```

### Advanced Features

- **Load**: Build and load image into Docker daemon
  ```yaml
  with:
    load: true
  ```

- **Secrets**: Pass secrets to build
  ```yaml
  with:
    secrets: |
      "mysecret=${{ secrets.MYSECRET }}"
  ```

- **SSH**: Use SSH agent for private repositories
  ```yaml
  with:
    ssh: |
      default=${{ secrets.SSH_PRIVATE_KEY }}
  ```

- **Outputs**: Generate image metadata as output
  ```yaml
  with:
    outputs: |
      type=docker,dest=/tmp/image.tar
      type=oci,dest=/tmp/image.tar
  ```

Remember to set up Docker Buildx and QEMU for multi-platform builds:

```yaml
- name: Set up QEMU
  uses: docker/setup-qemu-action@v3
- name: Set up Docker Buildx
  uses: docker/setup-buildx-action@v3
```