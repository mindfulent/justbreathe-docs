# Reference for DigitalOcean App Platform, App Specification (For .do/app.yaml)

*Validated on 6 Oct 2020 - Last edited on 11 Mar 2024*

App Platform is a Platform-as-a-Service (PaaS) offering that allows developers to publish code directly to DigitalOcean servers without worrying about the underlying infrastructure.

As an alternative to configuring your app in the control panel, you can define an app specification using YAML. YAML-based app specs are useful for deploying fully-configured apps with the DigitalOcean API or `doctl`, the DigitalOcean command-line tool.

## App Spec Definition

### App Spec

The application specification, or app spec, is a YAML manifest that declaratively states everything about your App Platform app, including each resource and all of your app's environment variables and configuration variables.

## Example App Spec

This example app spec defines a single service, `api`, and a single static site, `website`. A CORS exception is defined for `internal.example-app.com`, and an alert is triggered in the event of deployment failure.

```yaml
alerts:
- rule: DEPLOYMENT_FAILED
- rule: DOMAIN_FAILED
features:
- buildpack-stack-auto-22
ingress:
  rules:
    - component: api
      match:
        path:
          prefix: /api
    - component: website
      cors:
        allow_origins:
          - prefix: https://internal.example-app.com
      match:
        path:
          prefix: /
name: example-app
region: nyc
services:
  - environment_slug: go
    github:
      branch: main
      deploy_on_push: true
    repo: git-user-name/api
    http_port: 8081
    instance_count: 2
    instance_size_slug: professional-xs
    name: api
    run_command: bin/api
    source_dir: /
    gitlab:
      repo: digitalocean/sample-golang
      branch: main
      deploy_on_push: true
    autoscaling:
      min_instance_count: 1
      max_instance_count: 5
      metrics:
        cpu:
          percent: 70
static_sites:
  - environment_slug: html
    github:
      branch: master
      deploy_on_push: true
    repo: git-user-name/website
    name: website
    source_dir: /
```

## YAML File Structure

This reference covers all possible values that can be defined in an app spec. Whitespace defines hierarchy in YAML files, so this reference uses whitespace to nest child values under parent values. When the string `(array)` appears, you can define more than one of the child values, but you must prefix them with a `-` dash as shown, per YAML syntax.

### Core Fields

#### name

String. The name of the app. Must be unique across all apps in the same account.

- Minimum length: 2
- Maximum length: 32
- Must comply with the following regular expression: `^[a-z][a-z0-9-]{0,30}[a-z0-9]$`

#### services (array)

Array of Objects. Workloads which expose publicly-accessible HTTP services.

Each service object can contain the following fields:

##### name

String. The name of the service component. Must be unique across all components within the same app.

- Minimum length: 2
- Maximum length: 32
- Must comply with the following regular expression: `^[a-z][a-z0-9-]{0,30}[a-z0-9]$`

##### git
Object. A Git repo to use as component's source. Only one of `git`, `github`, `gitlab`, or `image` must be set.

###### repo_clone_url
String. The clone URL of the repo.
- Maximum length: 255
- Example: `https://github.com/digitalocean/sample-golang.git`

###### branch
String. The name of the branch to use

##### github
Object. A GitHub repo to use as component's source. Only one of `git`, `github`, `gitlab`, or `image` must be set.

###### repo
String. The name of the repo in the format owner/repo.
- Example: `digitalocean/sample-golang`
- Must comply with the following regular expression: `^[^/]+/[^/]+$`

###### branch
String. The name of the branch to use

###### deploy_on_push
Boolean. Whether to automatically deploy new commits made to the repo

##### image
Object. An image to use as the component's source. Only one of `git`, `github`, `gitlab`, or `image` must be set.

###### registry_type
String. The registry type.
- `DOCR`: The DigitalOcean container registry type
- `DOCKER_HUB`: The DockerHub container registry type
- `GHCR`: The Github container registry type

###### registry
String. The registry name. Must be left empty for the `DOCR` registry type. Required for the `DOCKER_HUB` registry type.
- Maximum length: 192

###### repository
String. The repository name.
- Maximum length: 192

###### tag
String. The repository tag. Defaults to `latest` if not provided and no digest is provided. Cannot be specified if digest is provided.
- Maximum length: 192

###### digest
String. The image digest. Cannot be specified if tag is provided.
- Maximum length: 192

###### registry_credentials
String. The credentials to be able to pull the image. The value will be encrypted on first submission. On following submissions, the encrypted value should be used.
- Format for DOCKER_HUB: `"$username:$access_token"`
- Format for GHCR: `"$username:$access_token"`

###### deploy_on_push
Object. Deploy on new image tags. Only for DOCR images.

###### enabled
Boolean. Automatically deploy new images. Only for DOCR images. Can't be enabled when a specific digest is specified.

##### gitlab
Object. A GitLab repo to use as component's source. Only one of `git`, `github`, `gitlab`, or `image` must be set.

###### repo
String. The name of the repo in the format owner/repo or owner/subgroup/repo.
- Example: `digitalocean/sample-golang` or `digitalocean/subgroup/sample-golang`
- Must comply with the following regular expression: `^[^/]+/([^/]+/)?[^/]+$`

###### branch
String. The name of the branch to use for deployments.

###### deploy_on_push
Boolean. Whether to automatically deploy new commits made to the repo.

##### dockerfile_path
String. The path to the Dockerfile relative to the root of the repo. If set, it will be used to build this component. Otherwise, App Platform will attempt to build it using buildpacks.
- Must be relative to the root of the repo
- Example: `Dockerfile` or `docker/Dockerfile`

##### build_command
String. An optional build command to run while building this component from source.
- Example: `npm run build` or `make`

##### run_command
String. An optional run command to override the component's default.
- Example: `npm start` or `./bin/api`

##### source_dir
String. An optional path to the working directory to use for the build. For Dockerfile builds, this will be used as the build context. Must be relative to the root of the repo.
- Example: `/` or `src/`

##### environment_slug
String. A slug identifying the type of app. Available values are:
- `node-js`
- `php`
- `ruby` 
- `python`
- `go`
- `hugo`
- `html`
- `hexo`
- `ruby-on-rails`
- `jekyll`
- `gatsby`

##### envs (array)
Array of Objects. A list of environment variables made available to the component.

Each environment variable object can contain the following fields:

###### key
String. The name of the environment variable.
- Must comply with Unix environment variable name rules
- Must comply with the following regular expression: `^[_A-Za-z][_A-Za-z0-9]*$`

###### value
String. The value of the environment variable.
- If type is `SECRET`, the value will be encrypted on first submission
- On subsequent submissions, use the encrypted value

###### scope
String. The visibility scope of the environment variable. Available values:
- `RUN_TIME`: Made available only at run-time
- `BUILD_TIME`: Made available only at build-time
- `RUN_AND_BUILD_TIME`: Made available at both build and run-time

###### type
String. The type of environment variable. Available values:
- `GENERAL`: A plain-text environment variable
- `SECRET`: A secret encrypted environment variable

##### instance_size_slug
String. The instance size to use for this component. Available values:

- `basic-xxs`: 1 vCPU, 512MB RAM
- `basic-xs`: 1 vCPU, 1GB RAM
- `basic-s`: 1 vCPU, 2GB RAM
- `basic-m`: 2 vCPU, 4GB RAM
- `professional-xs`: 2 vCPU, 4GB RAM
- `professional-s`: 2 vCPU, 8GB RAM
- `professional-m`: 4 vCPU, 16GB RAM
- `professional-l`: 8 vCPU, 32GB RAM
- `professional-xl`: 16 vCPU, 64GB RAM

Default: `basic-xxs`

##### instance_count
Integer. The amount of instances that this component should be scaled to.

##### autoscaling
Object. Configuration for automatically scaling this component based on metrics.

###### min_instance_count
Integer. The minimum number of instances this component should be scaled to.
- Must be greater than 0
- Must be less than or equal to max_instance_count
- Example: `1`

###### max_instance_count
Integer. The maximum number of instances this component should be scaled to.
- Must be greater than or equal to min_instance_count
- Example: `5`

###### metrics
Object. The metrics that trigger autoscaling for this component.

####### cpu
Object. Settings for CPU-based autoscaling.

######## percent
Integer. The target CPU utilization percentage that triggers scaling.
- Must be between 1 and 100
- Example: `70`

Example autoscaling configuration:
```yaml
services:
  - name: api
    // ... other service config ...
    autoscaling:
      min_instance_count: 1
      max_instance_count: 5
      metrics:
        cpu:
          percent: 70
```

##### http_port
Integer. The internal port on which this service's run command will listen. Default: 8081 if there is not an environment variable with the name `PORT`, one will be automatically added with its value set to the value of this field.

##### routes (array)
Array of Objects. (Deprecated) A list of HTTP routes that should be routed to this component.

Each route object can contain the following fields:

###### path
String. (Deprecated) An HTTP path prefix. Paths must start with / and must be unique across all components within an app.
- Must start with /
- Must be unique within the app
- Example: `/api` or `/v1/users`

###### preserve_path_prefix
Boolean. (Deprecated) An optional flag to preserve the path that is forwarded to the backend service. By default, the HTTP request path will be trimmed from the left when forwarded to the component. For example, a component with `path=/api` will have requests to `/api/list` trimmed to `/list`. If this value is `true`, the path

##### health_check
Object. A health check to determine the availability of this component.

###### initial_delay_seconds
Integer. The number of seconds to wait before beginning health checks.
- Default: 0 seconds
- Minimum: 0
- Maximum: 3600

###### period_seconds
Integer. The number of seconds to wait between health checks.
- Default: 10 seconds
- Minimum: 1
- Maximum: 300

###### timeout_seconds
Integer. The number of seconds after which the check times out.
- Default: 1 second
- Minimum: 1
- Maximum: 120

###### success_threshold
Integer. The number of successful health checks before considered healthy.
- Default: 1
- Minimum: 1
- Maximum: 50

###### failure_threshold
Integer. The number of failed health checks before considered unhealthy.
- Default: 9
- Minimum: 1
- Maximum: 50

###### http_path
String. The route path used for the HTTP health check ping.
- If not set, the HTTP health check will be disabled and a TCP health check used instead

###### port
Integer. The port on which the health check will be performed.
- If not set, the health check will be performed on the component's http_port

Example health check configuration:
```yaml
services:
  - name: api
    // ... other service config ...
    health_check:
      initial_delay_seconds: 30
      period_seconds: 15
      timeout_seconds: 5
      success_threshold: 1
      failure_threshold: 3
      http_path: /health
      port: 8081
```

##### cors
Object. (Deprecated, see `ingress > rules > cors`) A Cross-Origin Resource Sharing policy (CORS).

###### allow_origins (array)
Array of Objects. The set of allowed CORS origins. This configures the Access-Control-Allow-Origin header.

Each origin object must specify exactly one of the following fields:
- `exact`: String. Exact string match. Minimum length: 1, Maximum length: 256
- `prefix`: String. Prefix-based match. Minimum length: 1, Maximum length: 256
- `regex`: String. RE2 style regex-based match. For more information about RE2 syntax, see: https://github.com/google/re2/wiki/Syntax

###### allow_methods (array)
Array of Strings. The set of allowed HTTP methods. This configures the Access-Control-Allow-Methods header.

###### allow_headers (array)
Array of Strings. The set of allowed HTTP request headers. This configures the Access-Control-Allow-Headers header.

###### expose_headers (array)
Array of Strings. The set of HTTP response headers that browsers are allowed to access. This configures the Access-Control-Expose-Headers header.

###### max_age
String. An optional duration specifying how long browsers can cache the results of a preflight request. This configures the Access-Control-Max-Age header. Example: `5h30m`.

###### allow_credentials
Boolean. Whether browsers should expose the response to the client-side JavaScript code when the request's credentials mode is `include`. This configures the Access-Control-Allow-Credentials header.

Example CORS configuration:
```yaml
services:
  - name: api
    // ... other service config ...
    cors:
      allow_origins:
        - exact: https://example.com
        - prefix: https://staging.
      allow_methods:
        - GET
        - POST
      allow_headers:
        - Authorization
        - Content-Type
      expose_headers:
        - X-Custom-Header
      max_age: 5h30m
      allow_credentials: true
```

##### internal_ports (array)
Array of Int64s. The ports on which this service will listen for internal traffic.

##### alerts (array)
Array of Objects. A list of configured alerts which apply to the component.

Each alert object can contain the following fields:

###### rule
String. The specific type of alert. Available values:
- `CPU_UTILIZATION`: CPU usage for a container instance (component level)
- `MEM_UTILIZATION`: RAM usage for a container instance (component level) 
- `RESTART_COUNT`: Container restart count (component level)
- `DEPLOYMENT_FAILED`: Failed deployment status (app level)
- `DEPLOYMENT_LIVE`: Successful deployment status (app level)
- `DEPLOYMENT_STARTED`: Deployment start status (app level)
- `DEPLOYMENT_CANCELED`: Canceled deployment status (app level)
- `DOMAIN_FAILED`: Failed domain configuration (app level)
- `DOMAIN_LIVE`: Successful domain configuration (app level)
- `FUNCTIONS_ACTIVATION_COUNT`: Function activation count (functions only)
- `FUNCTIONS_AVERAGE_DURATION_MS`: Average function runtime duration (functions only)
- `FUNCTIONS_ERROR_RATE_PER_MINUTE`: Function error rate per minute (functions only)
- `FUNCTIONS_AVERAGE_WAIT_TIME_MS`: Average function wait time (functions only)
- `FUNCTIONS_ERROR_COUNT`: Function error count (functions only)
- `FUNCTIONS_GB_RATE_PER_SECOND`: Function memory consumption rate (functions only)

###### disabled
Boolean. Determines whether or not the alert is disabled.

###### operator
String. The comparison operator. Available values:
- `GREATER_THAN`
- `LESS_THAN`

###### value
Number. The threshold value used with the operator and window to determine when an alert should trigger.

###### window
String. The time window for alert evaluation. Available values:
- `FIVE_MINUTES`
- `TEN_MINUTES`
- `THIRTY_MINUTES`
- `ONE_HOUR`

Example alert configuration:
```yaml
services:
  - name: api
    // ... other service config ...
    alerts:
      - rule: CPU_UTILIZATION
        disabled: false
        operator: GREATER_THAN
        value: 80
        window: FIVE_MINUTES
      - rule: MEM_UTILIZATION
        disabled: false
        operator: GREATER_THAN
        value: 90
        window: TEN_MINUTES
```

##### log_destinations (array)
Array of Objects. A list of configured log forwarding destinations.

Each log destination object must contain exactly one of the following destination configurations:

###### papertrail
Object. Papertrail configuration.

####### endpoint
String. Papertrail syslog endpoint.

###### datadog
Object. Datadog configuration.

####### endpoint
String. Datadog HTTP log intake endpoint.

####### api_key
String. Datadog API key.

###### logtail
Object. Logtail configuration.

####### token
String. Logtail token.

###### opensearch
Object. OpenSearch configuration.

####### endpoint
String. OpenSearch API Endpoint. Only HTTPS is supported. Format https://... Cannot be specified if cluster_name is also specified.

####### basic_auth
Object.

######## user
String. Username to authenticate with. Only required when endpoint is set. Defaults to 'admin' when cluster_name is set.

######## password
String. Password for user defined in User. Is required when endpoint is set. Cannot be set if using a DigitalOcean DBaaS OpenSearch cluster.

####### index_name
String. The index name to use for the logs. If not set, the default index name is "logs".

####### cluster_name
String. The name of a DigitalOcean DBaaS OpenSearch cluster to use as a log forwarding destination. Cannot be specified if endpoint is also specified.

Example log destination configuration:
```yaml
services:
  - name: api
    // ... other service config ...
    log_destinations:
      - papertrail:
          endpoint: "logs.papertrailapp.com:12345"
      - datadog:
          endpoint: "https://http-intake.logs.datadoghq.com/v1/input"
          api_key: "your-datadog-api-key"
      - opensearch:
          endpoint: "https://opensearch.example.com"
          basic_auth:
            user: "admin"
            password: "secret"
          index_name: "app-logs"
```

##### termination
Object. Configuration for graceful shutdown behavior of the component.

###### drain_seconds
Integer. The number of seconds to wait between selecting a container instance for termination and issuing the TERM signal. Selecting a container instance for termination begins an asynchronous drain of new requests on upstream load-balancers.
- Default: 15 seconds
- Minimum: 1
- Maximum: 110

###### grace_period_seconds
Integer. The number of seconds to wait between sending a TERM signal to a container and issuing a KILL which causes immediate shutdown.
- Default: 120 seconds  
- Minimum: 1
- Maximum: 600

Example termination configuration:
```yaml
services:
  - name: api
    // ... other service config ...
    termination:
      drain_seconds: 30
      grace_period_seconds: 60
```
#### static_sites (array)

Array of Objects. Content which can be rendered to static web assets.

Each static site object can contain the following fields:

##### name
String. The name of the static site component. Must be unique across all components within the same app.
- Minimum length: 2
- Maximum length: 32
- Must comply with the following regular expression: `^[a-z][a-z0-9-]{0,30}[a-z0-9]$`

##### git
Object. A Git repo to use as component's source. Only one of `git`, `github`, or `gitlab` must be set.

###### repo_clone_url
String. The clone URL of the repo.
- Maximum length: 255
- Example: `https://github.com/digitalocean/sample-static-site.git`

###### branch
String. The name of the branch to use

##### github
Object. A GitHub repo to use as component's source. Only one of `git`, `github`, or `gitlab` must be set.

###### repo
String. The name of the repo in the format owner/repo.
- Example: `digitalocean/sample-static-site`
- Must comply with the following regular expression: `^[^/]+/[^/]+$`

###### branch
String. The name of the branch to use

###### deploy_on_push
Boolean. Whether to automatically deploy new commits made to the repo

##### gitlab
Object. A GitLab repo to use as component's source. Only one of `git`, `github`, or `gitlab` must be set.

###### repo
String. The name of the repo in the format owner/repo or owner/subgroup/repo.
- Example: `digitalocean/sample-static-site` or `digitalocean/subgroup/sample-static-site`
- Must comply with the following regular expression: `^[^/]+/([^/]+/)?[^/]+$`

###### branch
String. The name of the branch to use

###### deploy_on_push
Boolean. Whether to automatically deploy new commits made to the repo

##### dockerfile_path
String. The path to the Dockerfile relative to the root of the repo. If set, it will be used to build this component.
- Must be relative to the root of the repo
- Example: `Dockerfile` or `docker/Dockerfile`

##### build_command
String. An optional build command to run while building this component from source.
- Example: `npm run build` or `hugo`

##### source_dir
String. An optional path to the working directory to use for the build.
- Must be relative to the root of the repo
- Example: `/` or `src/`

##### environment_slug
String. A slug identifying the type of app. Available values are:
- `node-js`
- `php`
- `ruby`
- `python`
- `go`
- `hugo`
- `html`
- `hexo`
- `ruby-on-rails`
- `jekyll`
- `gatsby`

##### output_dir
String. An optional path to where the built assets will be located, relative to the build context. If not set, App Platform will automatically scan for these directory names: `_static`, `dist`, `public`, `build`.

##### index_document
String. The name of the index document to use when serving this static site.
- Default: `index.html`

##### error_document
String. The name of the error document to use when serving this static site. If no such file exists within the built assets, App Platform will supply one.
- Default: `404.html`

##### envs (array)
Array of Objects. A list of environment variables made available to the component.

Each environment variable object can contain the following fields:

###### key
String. The name of the environment variable.
- Must comply with Unix environment variable name rules
- Must comply with the following regular expression: `^[_A-Za-z][_A-Za-z0-9]*$`

###### value
String. The value of the environment variable.
- If type is `SECRET`, the value will be encrypted on first submission
- On subsequent submissions, use the encrypted value

###### scope
String. The visibility scope of the environment variable. Available values:
- `RUN_TIME`: Made available only at run-time
- `BUILD_TIME`: Made available only at build-time
- `RUN_AND_BUILD_TIME`: Made available at both build and run-time

###### type
String. The type of environment variable. Available values:
- `GENERAL`: A plain-text environment variable
- `SECRET`: A secret encrypted environment variable

##### routes (array)
Array of Objects. (Deprecated) A list of HTTP routes that should be routed to this component.

Each route object can contain the following fields:

###### path
String. (Deprecated) An HTTP path prefix. Paths must start with / and must be unique across all components within an app.
- Must start with /
- Must be unique within the app
- Example: `/api` or `/v1/users`

###### preserve_path_prefix
Boolean. (Deprecated) An optional flag to preserve the path that is forwarded to the backend service. By default, the HTTP request path will be trimmed from the left when forwarded to the component. For example:
- A component with `path=/api` will have requests to `/api/list` trimmed to `/list`
- If this value is `true`, the path will remain `/api/list`
- Note: this is not applicable for Functions Components

##### cors
Object. (Deprecated, see `ingress > rules > cors`) A Cross-Origin Resource Sharing policy (CORS).

###### allow_origins (array)
Array of Objects. The set of allowed CORS origins. This configures the Access-Control-Allow-Origin header.

Each origin object must specify exactly one of the following fields:
- `exact`: String. Exact string match. Minimum length: 1, Maximum length: 256
- `prefix`: String. Prefix-based match. Minimum length: 1, Maximum length: 256
- `regex`: String. RE2 style regex-based match. For more information about RE2 syntax, see: https://github.com/google/re2/wiki/Syntax

###### allow_methods (array)
Array of Strings. The set of allowed HTTP methods. This configures the Access-Control-Allow-Methods header.

###### allow_headers (array)
Array of Strings. The set of allowed HTTP request headers. This configures the Access-Control-Allow-Headers header.

###### expose_headers (array)
Array of Strings. The set of HTTP response headers that browsers are allowed to access. This configures the Access-Control-Expose-Headers header.

###### max_age
String. An optional duration specifying how long browsers can cache the results of a preflight request. This configures the Access-Control-Max-Age header. Example: `5h30m`.

###### allow_credentials
Boolean. Whether browsers should expose the response to the client-side JavaScript code when the request's credentials mode is `include`. This configures the Access-Control-Allow-Credentials header.

##### catchall_document
String. The name of the document to use as the fallback for any requests to documents that are not found when serving this static site. Only 1 of catchall_document or error_document can be set.

#### workers (array)

Array of Objects. Workloads which do not expose publicly-accessible HTTP services.

Each worker object can contain the following fields:

##### name
String. The name of the worker component. Must be unique across all components within the same app.
- Minimum length: 2
- Maximum length: 32
- Must comply with the following regular expression: `^[a-z][a-z0-9-]{0,30}[a-z0-9]$`

##### git
Object. A Git repo to use as component's source. Only one of `git`, `github`, `gitlab`, or `image` must be set.

###### repo_clone_url
String. The clone URL of the repo.
- Maximum length: 255
- Example: `https://github.com/digitalocean/sample-worker.git`

###### branch
String. The name of the branch to use

##### github
Object. A GitHub repo to use as component's source. Only one of `git`, `github`, `gitlab`, or `image` must be set.

###### repo
String. The name of the repo in the format owner/repo.
- Example: `digitalocean/sample-worker`
- Must comply with the following regular expression: `^[^/]+/[^/]+$`

###### branch
String. The name of the branch to use

###### deploy_on_push
Boolean. Whether to automatically deploy new commits made to the repo

##### image
Object. An image to use as the component's source. Only one of `git`, `github`, `gitlab`, or `image` must be set.

###### registry_type
String. The registry type. Available values:
- `DOCR`: The DigitalOcean container registry type
- `DOCKER_HUB`: The DockerHub container registry type
- `GHCR`: The Github container registry type

###### registry
String. The registry name.
- Must be left empty for the `DOCR` registry type
- Required for the `DOCKER_HUB` registry type
- Maximum length: 192

###### repository
String. The repository name.
- Maximum length: 192

###### tag
String. The repository tag.
- Defaults to `latest` if not provided and no digest is provided
- Cannot be specified if digest is provided
- Maximum length: 192

###### digest
String. The image digest.
- Cannot be specified if tag is provided
- Maximum length: 192

###### registry_credentials
String. The credentials to use for pulling the image.
- Value will be encrypted on first submission
- Use encrypted value for subsequent submissions
- Format for DOCKER_HUB: `"$username:$access_token"`
- Format for GHCR: `"$username:$access_token"`

###### deploy_on_push
Object. Configuration for automatic deployment of new image tags (DOCR only).

####### enabled
Boolean. Whether to automatically deploy new images.
- Only applicable for DOCR images
- Cannot be enabled when a specific digest is specified

##### gitlab
Object. A GitLab repo to use as component's source. Only one of `git`, `github`, `gitlab`, or `image` must be set.

[Configuration follows same pattern as services gitlab configuration]

##### dockerfile_path
String. The path to the Dockerfile relative to the root of the repo. If set, it will be used to build this component. Otherwise, App Platform will attempt to build it using buildpacks.
- Must be relative to the root of the repo
- Example: `Dockerfile` or `docker/Dockerfile`

##### build_command
String. An optional build command to run while building this component from source.
- Example: `npm run build` or `make`

##### run_command
String. An optional run command to override the component's default.
- Example: `npm start` or `./bin/worker`

##### source_dir
String. An optional path to the working directory to use for the build. For Dockerfile builds, this will be used as the build context. Must be relative to the root of the repo.
- Example: `/` or `src/`

##### environment_slug
String. A slug identifying the type of app. Available values are:
- `node-js`
- `php`
- `ruby`
- `python`
- `go`
- `hugo`
- `html`
- `hexo`
- `ruby-on-rails`
- `jekyll`
- `gatsby`

##### envs (array)
Array of Objects. A list of environment variables made available to the component.

Each environment variable object can contain the following fields:

###### key
String. The name of the environment variable.
- Must comply with Unix environment variable name rules
- Must comply with the following regular expression: `^[_A-Za-z][_A-Za-z0-9]*$`

###### value
String. The value of the environment variable.
- If type is `SECRET`, the value will be encrypted on first submission
- On subsequent submissions, use the encrypted value

###### scope
String. The visibility scope of the environment variable. Available values:
- `RUN_TIME`: Made available only at run-time
- `BUILD_TIME`: Made available only at build-time
- `RUN_AND_BUILD_TIME`: Made available at both build and run-time

###### type
String. The type of environment variable. Available values:
- `GENERAL`: A plain-text environment variable
- `SECRET`: A secret encrypted environment variable

##### instance_size_slug
String. The instance size to use for this component.

##### instance_count
Integer. The amount of instances that this component should be scaled to. Default: 1

##### autoscaling
Object. Configuration for automatically scaling this component based on metrics.

###### min_instance_count
Integer. The minimum amount of instances for this component.

###### max_instance_count
Integer. The maximum amount of instances for this component.

###### metrics
Object. The metrics that the component is scaled on.

####### cpu
Object. Settings for scaling the component based on CPU utilization.

######## percent
Integer. The average target CPU utilization for the component.

##### alerts (array)
Array of Objects. A list of configured alerts which apply to the component.

Each alert object can contain the following fields:

###### rule
String. The specific type of alert. Available values:
- `CPU_UTILIZATION`: CPU usage for a container instance (component level)
- `MEM_UTILIZATION`: RAM usage for a container instance (component level)
- `RESTART_COUNT`: Container restart count (component level)
- `DEPLOYMENT_FAILED`: Failed deployment status (app level)
- `DEPLOYMENT_LIVE`: Successful deployment status (app level)
- `DEPLOYMENT_STARTED`: Deployment start status (app level)
- `DEPLOYMENT_CANCELED`: Canceled deployment status (app level)
- `DOMAIN_FAILED`: Failed domain configuration (app level)
- `DOMAIN_LIVE`: Successful domain configuration (app level)
- `FUNCTIONS_ACTIVATION_COUNT`: Function activation count (functions only)
- `FUNCTIONS_AVERAGE_DURATION_MS`: Average function runtime duration (functions only)
- `FUNCTIONS_ERROR_RATE_PER_MINUTE`: Function error rate per minute (functions only)
- `FUNCTIONS_AVERAGE_WAIT_TIME_MS`: Average function wait time (functions only)
- `FUNCTIONS_ERROR_COUNT`: Function error count (functions only)
- `FUNCTIONS_GB_RATE_PER_SECOND`: Function memory consumption rate (functions only)

###### disabled
Boolean. Determines whether or not the alert is disabled.

###### operator
String. The comparison operator. Available values:
- `GREATER_THAN`
- `LESS_THAN`

###### value
Number. The threshold value used with the operator and window to determine when an alert should trigger.

###### window
String. The time window for alert evaluation. Available values:
- `FIVE_MINUTES`
- `TEN_MINUTES`
- `THIRTY_MINUTES`
- `ONE_HOUR`

##### log_destinations (array)
Array of Objects. A list of configured log forwarding destinations.

Each log destination object must contain exactly one of the following destination configurations:

###### papertrail
Object. Papertrail configuration.

####### endpoint
String. Papertrail syslog endpoint.

###### datadog
Object. Datadog configuration.

####### endpoint
String. Datadog HTTP log intake endpoint.

####### api_key
String. Datadog API key.

###### logtail
Object. Logtail configuration.

####### token
String. Logtail token.

###### opensearch
Object. OpenSearch configuration.

####### endpoint
String. OpenSearch API Endpoint.
- Only HTTPS is supported
- Format must be `https://...`
- Cannot be specified if `cluster_name` is also specified

####### basic_auth
Object. Authentication configuration for OpenSearch.

######## user
String. Username to authenticate with.
- Required when `endpoint` is set
- Defaults to 'admin' when `cluster_name` is set

######## password
String. Password for user defined in `user` field.
- Required when `endpoint` is set
- Cannot be set if using a DigitalOcean DBaaS OpenSearch cluster

####### index_name
String. The index name to use for the logs.
- If not set, the default index name is "logs"

####### cluster_name
String. The name of a DigitalOcean DBaaS OpenSearch cluster to use as a log forwarding destination.
- Cannot be specified if `endpoint` is also specified

##### termination
Object. Configuration for graceful shutdown behavior of the component.

###### grace_period_seconds
Integer. The number of seconds to wait between sending a TERM signal to a container and issuing a KILL which causes immediate shutdown. 
- Default: 120 seconds  
- Minimum: 1
- Maximum: 600

#### functions (array)

Array of Objects. Workloads which expose publicly-accessible HTTP services via Functions Components.

Each function object can contain the following fields:

##### name
String. The name of the function component. Must be unique across all components within the same app.
- Minimum length: 2
- Maximum length: 32
- Must comply with the following regular expression: `^[a-z][a-z0-9-]{0,30}[a-z0-9]$`

##### git
Object. A Git repo to use as component's source. Only one of `git`, `github`, or `gitlab` must be set.

###### repo_clone_url
String. The clone URL of the repo.
- Maximum length: 255
- Example: `https://github.com/digitalocean/sample-functions.git`

###### branch
String. The name of the branch to use

##### github
Object. A GitHub repo to use as component's source. Only one of `git`, `github`, or `gitlab` must be set.

###### repo
String. The name of the repo in the format owner/repo.
- Example: `digitalocean/sample-functions`
- Must comply with the following regular expression: `^[^/]+/[^/]+$`

###### branch
String. The name of the branch to use

###### deploy_on_push
Boolean. Whether to automatically deploy new commits made to the repo

##### gitlab
Object. A GitLab repo to use as component's source. Only one of `git`, `github`, or `gitlab` must be set.

###### repo
String. The name of the repo in the format owner/repo or owner/subgroup/repo.
- Example: `digitalocean/sample-functions` or `digitalocean/subgroup/sample-functions`
- Must comply with the following regular expression: `^[^/]+/([^/]+/)?[^/]+$`

###### branch
String. The name of the branch to use for deployments.

###### deploy_on_push
Boolean. Whether to automatically deploy new commits made to the repo.

##### source_dir
String. An optional path to the working directory to use for the build. Must be relative to the root of the repo.
- Example: `/` or `src/`

##### envs (array)
Array of Objects. A list of environment variables made available to the component.

Each environment variable object can contain the following fields:

###### key
String. The name of the environment variable.
- Must comply with Unix environment variable name rules
- Must comply with the following regular expression: `^[_A-Za-z][_A-Za-z0-9]*$`

###### value
String. The value of the environment variable.
- If type is `SECRET`, the value will be encrypted on first submission
- On subsequent submissions, use the encrypted value

###### scope
String. The visibility scope of the environment variable. Available values:
- `RUN_TIME`: Made available only at run-time
- `BUILD_TIME`: Made available only at build-time
- `RUN_AND_BUILD_TIME`: Made available at both build and run-time

###### type
String. The type of environment variable. Available values:
- `GENERAL`: A plain-text environment variable
- `SECRET`: A secret encrypted environment variable

##### routes (array)
Array of Objects. (Deprecated) A list of HTTP routes that should be routed to this component.

Each route object can contain the following fields:

###### path
String. (Deprecated) An HTTP path prefix. Paths must start with / and must be unique across all components within an app.
- Must start with /
- Must be unique within the app
- Example: `/api` or `/v1/users`

###### preserve_path_prefix
Boolean. (Deprecated) An optional flag to preserve the path that is forwarded to the backend service. By default, the HTTP request path will be trimmed from the left when forwarded to the component. For example:
- A component with `path=/api` will have requests to `/api/list` trimmed to `/list`
- If this value is `true`, the path will remain `/api/list`
- Note: this is not applicable for Functions Components

##### alerts (array)
Array of Objects. A list of configured alerts which apply to the component.

Each alert object can contain the following fields:

###### rule
String. The specific type of alert. Available values:
- `FUNCTIONS_ACTIVATION_COUNT`: Function activation count
- `FUNCTIONS_AVERAGE_DURATION_MS`: Average function runtime duration
- `FUNCTIONS_ERROR_RATE_PER_MINUTE`: Function error rate per minute
- `FUNCTIONS_AVERAGE_WAIT_TIME_MS`: Average function wait time
- `FUNCTIONS_ERROR_COUNT`: Function error count
- `FUNCTIONS_GB_RATE_PER_SECOND`: Function memory consumption rate

###### disabled
Boolean. Determines whether or not the alert is disabled.

###### operator
String. The comparison operator. Available values:
- `GREATER_THAN`
- `LESS_THAN`

###### value
Number. The threshold value used with the operator and window to determine when an alert should trigger.

###### window
String. The time window for alert evaluation. Available values:
- `FIVE_MINUTES`
- `TEN_MINUTES`
- `THIRTY_MINUTES`
- `ONE_HOUR`

##### log_destinations (array)
Array of Objects. A list of configured log forwarding destinations.

Each log destination object must contain exactly one of the following destination configurations:

###### papertrail
Object. Papertrail configuration.

####### endpoint
String. Papertrail syslog endpoint.

###### datadog
Object. Datadog configuration.

####### endpoint
String. Datadog HTTP log intake endpoint.

####### api_key
String. Datadog API key.

###### logtail
Object. Logtail configuration.

####### token
String. Logtail token.

###### opensearch
Object. OpenSearch configuration.

####### endpoint
String. OpenSearch API Endpoint.
- Only HTTPS is supported
- Format must be `https://...`
- Cannot be specified if `cluster_name` is also specified

####### basic_auth
Object. Authentication configuration for OpenSearch.

######## user
String. Username to authenticate with.
- Required when `endpoint` is set
- Defaults to 'admin' when `cluster_name` is set

######## password
String. Password for user defined in `user` field.
- Required when `endpoint` is set
- Cannot be set if using a DigitalOcean DBaaS OpenSearch cluster

####### index_name
String. The index name to use for the logs.
- If not set, the default index name is "logs"

####### cluster_name
String. The name of a DigitalOcean DBaaS OpenSearch cluster to use as a log forwarding destination.
- Cannot be specified if `endpoint` is also specified

##### cors
Object. (Deprecated, see `ingress > rules > cors`) A Cross-Origin Resource Sharing policy (CORS).

###### allow_origins (array)
Array of Objects. The set of allowed CORS origins. This configures the Access-Control-Allow-Origin header.

Each origin object must specify

##### alerts (array)
Array of Objects. A list of configured alerts which apply to the component.

Each alert object can contain the following fields:

###### rule
String. The specific type of alert. Available values:
- `CPU_UTILIZATION`: Represents CPU for a given container instance. Only applicable at the component level.
- `MEM_UTILIZATION`: Represents RAM for a given container instance. Only applicable at the component level.
- `RESTART_COUNT`: Represents restart count for a given container instance. Only applicable at the component level.
- `DEPLOYMENT_FAILED`: Represents whether a deployment has failed. Only applicable at the app level.
- `DEPLOYMENT_LIVE`: Represents whether a deployment has succeeded. Only applicable at the app level.
- `DEPLOYMENT_STARTED`: Represents whether a deployment has started. Only applicable at the app level.
- `DEPLOYMENT_CANCELED`: Represents whether a deployment has been canceled. Only applicable at the app level.
- `DOMAIN_FAILED`: Represents whether a domain configuration has failed. Only applicable at the app level.
- `DOMAIN_LIVE`: Represents whether a domain configuration has succeeded. Only applicable at the app level.
- `FUNCTIONS_ACTIVATION_COUNT`: Represents an activation count for a given functions instance. Only applicable to functions components.
- `FUNCTIONS_AVERAGE_DURATION_MS`: Represents the average duration for function runtimes. Only applicable to functions components.
- `FUNCTIONS_ERROR_RATE_PER_MINUTE`: Represents an error rate per minute for a given functions instance. Only applicable to functions components.
- `FUNCTIONS_AVERAGE_WAIT_TIME_MS`: Represents the average wait time for functions. Only applicable to functions components.
- `FUNCTIONS_ERROR_COUNT`: Represents an error count for a given functions instance. Only applicable to functions components.
- `FUNCTIONS_GB_RATE_PER_SECOND`: Represents the rate of memory consumption (GB × seconds) for functions. Only applicable to functions components.

###### disabled
Boolean. Determines whether or not the alert is disabled.

###### operator
String. Can be `GREATER_THAN` or `LESS_THAN`

###### value
Number. The meaning is dependent upon the rule. It is used in conjunction with the operator and window to determine when an alert should trigger.

###### window
String. Can be `FIVE_MINUTES`, `TEN_MINUTES`, `THIRTY_MINUTES`, or `ONE_HOUR`

##### log_destinations (array)
Array of Objects. A list of configured log forwarding destinations.

Each log destination object must contain exactly one of the following destination configurations:

###### papertrail
Object. Papertrail configuration.

####### endpoint
String. Papertrail syslog endpoint.

###### datadog
Object. Datadog configuration.

####### endpoint
String. Datadog HTTP log intake endpoint.

####### api_key
String. Datadog API key.

###### logtail
Object. Logtail configuration.

####### token
String. Logtail token.

###### opensearch
Object. OpenSearch configuration.

####### endpoint
String. OpenSearch API Endpoint. Only HTTPS is supported. Format https://... Cannot be specified if cluster_name is also specified.

####### basic_auth
Object.

######## user
String. Username to authenticate with. Only required when endpoint is set. Defaults to 'admin' when cluster_name is set.

######## password
String. Password for user defined in User. Is required when endpoint is set. Cannot be set if using a DigitalOcean DBaaS OpenSearch cluster.

####### index_name
String. The index name to use for the logs. If not set, the default index name is "logs".

####### cluster_name
String. The name of a DigitalOcean DBaaS OpenSearch cluster to use as a log forwarding destination. Cannot be specified if endpoint is also specified.

##### cors
Object. (Deprecated, see `ingress > rules > cors`) A Cross-Origin Resource Sharing policy (CORS).

###### allow_origins (array)
Array of Objects. The set of allowed CORS origins. This configures the Access-Control-Allow-Origin header.

Each origin object must specify exactly one of:
- `exact`: String. Exact string match. Minimum length: 1, Maximum length: 256
- `prefix`: String. Prefix-based match. Minimum length: 1, Maximum length: 256  
- `regex`: String. RE2 style regex-based match. For more information about RE2 syntax, see: https://github.com/google/re2/wiki/Syntax

###### allow_methods (array)
Array of Strings. The set of allowed HTTP methods. This configures the Access-Control-Allow-Methods header.

###### allow_headers (array)
Array of Strings. The set of allowed HTTP request headers. This configures the Access-Control-Allow-Headers header.

###### expose_headers (array)
Array of Strings. The set of HTTP response headers that browsers are allowed to access. This configures the Access-Control-Expose-Headers header.

###### max_age
String. An optional duration specifying how long browsers can cache the results of a preflight request. This configures the Access-Control-Max-Age header. Example: `5h30m`.

###### allow_credentials
Boolean. Whether browsers should expose the response to the client-side JavaScript code when the request's credentials mode is `include`. This configures the Access-Control-Allow-Credentials header.

#### databases (array)
Array of Objects. Database instances which can provide persistence to workloads within the application.

Each database object can contain the following fields:

##### name
String. The database's name. The name must be unique across all components within the same app and cannot use capital letters.
- Minimum length: 2
- Maximum length: 32
- Must comply with the following regular expression: `^[a-z][a-z0-9-]{0,30}[a-z0-9]$`

##### engine
String. The database engine to use. Available values:
- `MYSQL`: MySQL
- `PG`: PostgreSQL  
- `REDIS`: Redis
- `MONGODB`: MongoDB
- `KAFKA`: Kafka
- `OPENSEARCH`: OpenSearch

##### version
String. The version of the database engine

##### production
Boolean. Whether this is a production or dev database.

##### cluster_name
String. The name of the underlying DigitalOcean DBaaS cluster. This is required for production databases. For dev databases, if cluster_name is not set, a new cluster will be provisioned.

##### db_name
String. The name of the MySQL or PostgreSQL database to configure.

##### db_user
String. The name of the MySQL or PostgreSQL user to configure.

#### domains (array)

Array of Objects. A set of hostnames where the application will be available.

Each domain object can contain the following fields:

##### domain
String. The hostname for the domain.
- Minimum length: 4
- Maximum length: 253
- Must comply with the following regular expression: `^([a-zA-Z0-9]+(-+[a-zA-Z0-9]+)*\.)+[a-zA-Z0-9]{2,}$`

##### type
String. The domain type, which can be one of the following:
- `DEFAULT`: The default .ondigitalocean.app domain assigned to this app
- `PRIMARY`: The primary domain for this app that is displayed as the default in the control panel, used in bindable environment variables, and any other places that reference an app's live URL. Only one domain may be set as primary.
- `ALIAS`: A non-primary domain

##### wildcard
Boolean. Indicates whether the domain includes all sub-domains, in addition to the given domain

##### zone
String. Optional. If the domain uses DigitalOcean DNS and you would like App Platform to automatically manage it for you, set this to the name of the domain on your account.

For example, If the domain you are adding is `app.domain.com`, the zone could be `domain.com`.

##### minimum_tls_version
String. Optional. The minimum version of TLS a client application can use to access resources for the domain.

Must be one of the following values wrapped within quotations: `"1.2"` or `"1.3"`.

#### region
String. The slug form of the geographical origin of the app. Default: nearest available

#### envs (array)

Array of Objects. A list of environment variables made available to all components in the app.

Each environment variable object can contain the following fields:

##### key
String. The name of the environment variable.
- Must comply with Unix environment variable name rules
- Must comply with the following regular expression: `^[_A-Za-z][_A-Za-z0-9]*$`

##### value
String. The value of the environment variable.
- If type is `SECRET`, the value will be encrypted on first submission
- On subsequent submissions, use the encrypted value

##### scope
String. The visibility scope of the environment variable. Available values:
- `RUN_TIME`: Made available only at run-time
- `BUILD_TIME`: Made available only at build-time
- `RUN_AND_BUILD_TIME`: Made available at both build and run-time

##### type
String. The type of environment variable. Available values:
- `GENERAL`: A plain-text environment variable
- `SECRET`: A secret encrypted environment variable

#### alerts (array)

Array of Objects. A list of alerts which apply to the app.

Each alert object can contain the following fields:

##### rule
String. The specific type of alert. Available values:
- `CPU_UTILIZATION`: Represents CPU for a given container instance. Only applicable at the component level.
- `MEM_UTILIZATION`: Represents RAM for a given container instance. Only applicable at the component level.
- `RESTART_COUNT`: Represents restart count for a given container instance. Only applicable at the component level.
- `DEPLOYMENT_FAILED`: Represents whether a deployment has failed. Only applicable at the app level.
- `DEPLOYMENT_LIVE`: Represents whether a deployment has succeeded. Only applicable at the app level.
- `DEPLOYMENT_STARTED`: Represents whether a deployment has started. Only applicable at the app level.
- `DEPLOYMENT_CANCELED`: Represents whether a deployment has been canceled. Only applicable at the app level.
- `DOMAIN_FAILED`: Represents whether a domain configuration has failed. Only applicable at the app level.
- `DOMAIN_LIVE`: Represents whether a domain configuration has succeeded. Only applicable at the app level.
- `FUNCTIONS_ACTIVATION_COUNT`: Represents an activation count for a given functions instance. Only applicable to functions components.
- `FUNCTIONS_AVERAGE_DURATION_MS`: Represents the average duration for function runtimes. Only applicable to functions components.
- `FUNCTIONS_ERROR_RATE_PER_MINUTE`: Represents an error rate per minute for a given functions instance. Only applicable to functions components.
- `FUNCTIONS_AVERAGE_WAIT_TIME_MS`: Represents the average wait time for functions. Only applicable to functions components.
- `FUNCTIONS_ERROR_COUNT`: Represents an error count for a given functions instance. Only applicable to functions components.
- `FUNCTIONS_GB_RATE_PER_SECOND`: Represents the rate of memory consumption (GB × seconds) for functions. Only applicable to functions components.

##### disabled
Boolean. Determines whether or not the alert is disabled.

##### operator
String. Can be `GREATER_THAN` or `LESS_THAN`

##### value
Number. The meaning is dependent upon the rule. It is used in conjunction with the operator and window to determine when an alert should trigger.

##### window
String. Can be `FIVE_MINUTES`, `TEN_MINUTES`, `THIRTY_MINUTES`, or `ONE_HOUR`

#### ingress
Object. Specification for component routing, rewrites, and redirects.

#### ingress
Object. Specification for component routing, rewrites, and redirects.

##### rules (array)
Array of Objects. Rules for configuring HTTP ingress for component routes, CORS, rewrites, and redirects.

Each rule object can contain the following fields:

##### match
Object. The match configuration for the rule.

###### path
Object. The path to match on.

####### prefix
String. Prefix-based match. For example, `/api` will match `/api`, `/api/`, and any nested paths such as `/api/v1/endpoint`.
- Maximum length: 256

##### component
Object. The component to route to. Only one of `component` or `redirect` may be set.

###### name
String. The name of the component to route to

###### preserve_path_prefix
Boolean. An optional flag to preserve the path that is forwarded to the backend service. By default, the HTTP request path will be trimmed from the left when forwarded to the component. For example, a component with `path=/api` will have requests to `/api/list` trimmed to `/list`. If this value is `true`, the path will remain `/api/list`. Note: this is not applicable for Functions Components and is mutually exclusive with `rewrite`.

###### rewrite
String. An optional field that will rewrite the path of the component to be what is specified here. By default, the HTTP request path will be trimmed from the left when forwarded to the component. For example, a component with `path=/api` will have requests to `/api/list` trimmed to `/list`. If you specified the rewrite to be `/v1`, requests to `/api/list` would be rewritten to `/v1/list`. Note: this is mutually exclusive with `preserve_path_prefix`.

##### redirect
Object. The redirect configuration for the rule. Only one of `component` or `redirect` may be set.

###### uri
String. An optional URI path to redirect to. Note: if this is specified the whole URI of the original request will be overwritten to this value, irrespective of the original request URI being matched.

###### authority
String. The authority/host to redirect to. This can be a hostname or IP address. Note: use `port` to set the port.

###### port
Integer. The port to redirect to.

###### scheme
String. The scheme to redirect to. Supported values are `http` or `https`. Default: `https`.

###### redirect_code
Integer. The redirect code to use. Defaults to `302`. Supported values are 300, 301, 302, 303, 304, 307, 308.

##### cors
Object. The CORS configuration for the rule.

###### allow_origins (array)
Array of Objects. The set of allowed CORS origins. This configures the Access-Control-Allow-Origin header.

Each origin object must specify exactly one of:
- `exact`: String. Exact string match. Only 1 of `exact`, `prefix`, or `regex` must be set.
  - Minimum length: 1
  - Maximum length: 256
- `prefix`: String. Prefix-based match
- `regex`: String. RE2 style regex-based match. Only 1 of `exact`, `prefix`, or `regex` must be set. For more information about RE2 syntax, see: https://github.com/google/re2/wiki/Syntax
  - Minimum length: 1
  - Maximum length: 256

###### allow_methods (array)
Array of Strings. The set of allowed HTTP methods. This configures the Access-Control-Allow-Methods header.

###### allow_headers (array)
Array of Strings. The set of allowed HTTP request headers. This configures the Access-Control-Allow-Headers header.

###### expose_headers (array)
Array of Strings. The set of HTTP response headers that browsers are allowed to access. This configures the Access-Control-Expose-Headers header.

###### max_age
String. An optional duration specifying how long browsers can cache the results of a preflight request. This configures the Access-Control-Max-Age header. Example: `5h30m`

###### allow_credentials
Boolean. Whether browsers should expose the response to the client-side JavaScript code when the request's credentials mode is `include`. This configures the Access-Control-Allow-Credentials header.

#### maintenance
Object. The maintenance configuration for the app.

##### enabled
Boolean. Set to true to enable maintenance mode. When enabled, all traffic to the app receives a maintenance message. Defaults to `false`.
