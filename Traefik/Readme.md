# Traefik
## Overview
Traefik is an open-source reverse proxy and load balancer designed to handle dynamic routing in modern microservices architectures. It supports multiple backends, including Docker, Kubernetes, Marathon, Mesos, Consul, Etcd, Zookeeper, Amazon ECS, and more. Traefik is known for its automatic configuration updates, high performance, and ease of use, making it a popular choice for managing traffic in cloud-native environments.

### Key Features:
- Dynamic Configuration: Automatically detects changes in the underlying infrastructure (e.g., new services, updated configurations) and adjusts routing rules accordingly.
- Service Discovery: Integrates with various service discovery mechanisms to dynamically route requests to the appropriate backend services.
- Load Balancing: Distributes incoming traffic across multiple instances of a service for improved performance and reliability.
- SSL/TLS Support: Manages SSL certificates, including automatic renewal, ensuring secure communication between clients and services.
- High Availability: Designed to run in clusters, providing failover capabilities and load distribution for the proxy itself.
- API Gateway Capabilities: Can be used as an API gateway, managing authentication, rate limiting, and other request processing tasks.

### Use Cases:

1. Microservices Architecture:
   - Traefik is often used in microservices environments to route traffic to different services based on URL paths or headers. It can automatically discover new services running in a Docker Swarm or Kubernetes cluster, making it easier to manage a large number of services.

2. Container Orchestration:
   - In containerized environments like Docker Swarm or Kubernetes, Traefik can act as the ingress controller, routing incoming traffic to the appropriate pods based on their labels and annotations. This simplifies service discovery and load balancing.

3. API Management:
   - As an API gateway, Traefik can be used to manage access to APIs, including authentication (e.g., OAuth2), rate limiting, caching, and request transformation. It can also handle SSL termination and routing requests to different versions of a service based on headers or paths.

4. Multi-Tenant Applications:
   - In multi-tenant applications, Traefik can be used to route traffic to different tenant-specific services or databases based on the hostname or path in the request URL.

5. Edge Computing:
   - Traefik can be deployed at the edge of a network to handle initial routing and load balancing, reducing latency and improving performance for users accessing applications hosted in remote data centers.

6. CI/CD Pipelines:
   - In continuous integration and deployment pipelines, Traefik can be used to dynamically route traffic between different stages of an application (e.g., development, testing, production) based on environment-specific configurations.

By leveraging Traefik's dynamic configuration capabilities and its wide range of integrations, teams can efficiently manage traffic and ensure high availability for their applications in complex, distributed environments.

## Docker Compose File
### Networks Section
```yaml
networks:
  dockernetwork:
    external: true
```
- **`networks`**: Defines the network configuration for the services.
- **`dockernetwork`**: Specifies a custom Docker network named `dockernetwork`.
- **`external: true`**: Indicates that this network is not managed by Docker Compose but exists outside of it. This allows other containers or services running on the same host to communicate with Traefik.

### Services Section
```yaml
services:
  traefik:
    image: traefik
```
- **`services`**: Defines the services that will be run.
- **`traefik`**: The name of the service, which in this case is Traefik itself.

#### Image
```yaml
image: traefik
```
- Specifies the Docker image to use for the Traefik service. In this case, it's the official Traefik image.

#### Hostname and Container Name
```yaml
hostname: traefik
container_name: traefik
```
- **`hostname`**: Sets the hostname of the container.
- **`container_name`**: Assigns a specific name to the container for easier identification.

#### Security Options
```yaml
security_opt:
  - no-new-privileges:true
```
- **`security_opt`**: Provides additional security options for the container.
- **`no-new-privileges: true`**: Prevents the creation of new privileges within the container, enhancing security.

#### Networks Configuration
```yaml
networks:
  dockernetwork:
    ipv4_address: 172.28.1.2
```
- **`networks`**: Defines which network(s) the service will connect to.
- **`dockernetwork`**: Specifies the previously defined `dockernetwork`.
- **`ipv4_address`**: Assigns a static IP address (`172.28.1.2`) to the Traefik container on the specified network.

#### Ports
```yaml
ports:
  # - 80:80
  - 443:443
```
- **`ports`**: Maps ports from the host to the container.
- **`# - 80:80`**: This line is commented out, indicating that port 80 on the host would be mapped to port 80 in the container if uncommented.
- **`443:443`**: Port 443 on the host is mapped to port 443 in the container. This is typically used for HTTPS traffic.

#### Volumes
```yaml
volumes:
  - ./configs/traefik.yml:/etc/traefik/traefik.yml:ro
  - ./configs/config.yml:/config.yml:ro
  - ./configs/acme.json:/acme.json
  - ./configs/logs:/logs
  - /etc/localtime:/etc/localtime:ro
```
- **`volumes`**: Binds directories from the host to the container.
- **`./configs/traefik.yml:/etc/traefik/traefik.yml:ro`**: Maps the `traefik.yml` configuration file from the local directory to `/etc/traefik/traefik.yml` inside the container with read-only permissions (`:ro`).
- **`./configs/config.yml:/config.yml:ro`**: Maps another configuration file from the local directory.
- **`./configs/acme.json:/acme.json`**: Maps a JSON file used by Traefik for managing Let's Encrypt certificates.
- **`./configs/logs:/logs`**: Maps the logs directory on the host to `/logs` inside the container for persisting log files.
- **`/etc/localtime:/etc/localtime:ro`**: Synchronizes the timezone of the container with the host.

#### Environment Variables
```yaml
environment:
  TZ: Asia/Kolkata
  CF_DNS_API_TOKEN_FILE: $CF_TOKEN
```
- **`environment`**: Defines environment variables for the container.
- **`TZ: Asia/Kolkata`**: Sets the timezone inside the container to India Standard Time (IST).
- **`CF_DNS_API_TOKEN_FILE: $CF_TOKEN`**: Points to a file containing a Cloudflare API token used by Traefik for DNS challenges during certificate issuance.

#### Restart Policy
```yaml
restart: unless-stopped
```
- **`restart`**: Defines the restart policy for the container.
- **`unless-stopped`**: Ensures that the container will restart automatically if it stops, unless explicitly stopped by the user. This is useful for maintaining uptime and reliability.

## Traefik.yml File
The `traefik.yml` file is the main configuration file for Traefik, a popular open-source reverse proxy and load balancer. Below is an explanation of each section in the provided configuration:

```yaml
global:
  checkNewVersion: true
  sendAnonymousUsage: true
```
- **Global Settings**: 
  - `checkNewVersion`: Enables Traefik to periodically check for new versions.
  - `sendAnonymousUsage`: Sends anonymous usage statistics to improve future versions.

```yaml
api:
  dashboard: true
```
- **API and Dashboard**:
  - `dashboard`: Enables the Traefik dashboard, which is a web interface to monitor and manage Traefik.

```yaml
entryPoints:
  https:
    address: :443
```
- **Entry Points**:
  - Defines an entry point named `https` that listens on port 443 for HTTPS traffic.

```yaml
serversTransport:
  insecureSkipVerify: true
```
- **Servers Transport**:
  - `insecureSkipVerify`: Disables SSL certificate verification when connecting to backend servers. This is useful for testing but should be avoided in production environments due to security risks.

```yaml
providers:
  file:
    filename: /config.yml
    watch: true
```
- **Providers**:
  - Specifies that Traefik should use a file provider to load configuration from `/config.yml`.
  - `watch`: Enables watching for changes in the configuration file, allowing Traefik to reload automatically.

```yaml
certificatesResolvers:
  cloudflare:
    acme:
      email: "youremail@domain.com"
      storage: "acme.json"
      caServer: "https://acme-staging-v02.api.letsencrypt.org/directory" # staging (for testing)
      dnsChallenge:
        provider: cloudflare
        resolvers:
          - "1.1.1.1:53"
          - "1.0.0.1:53"
```
- **Certificates Resolvers**:
  - Defines a resolver named `cloudflare` using ACME (Automatic Certificate Management Environment) to obtain certificates.
  - `email`: The email address used for account registration with Let's Encrypt.
  - `storage`: Specifies the file where ACME account information and certificates are stored.
  - `caServer`: Points to the Let's Encrypt staging server, which is used for testing purposes. For production, use the default Let's Encrypt server.
  - `dnsChallenge`: Configures DNS-based challenge for certificate issuance using Cloudflare as the DNS provider.

```yaml
log:
  filePath: /logs/traefik.log
  level: "INFO"
  format: json
```
- **Logging**:
  - Specifies the log file path, log level, and log format.
  - `filePath`: The path to the log file.
  - `level`: The logging level (e.g., DEBUG, INFO, WARN, ERROR).
  - `format`: The format of the log entries (JSON in this case).

```yaml
accesslog:
  filepath: /logs/access.log
  format: json
  addInternals: true
  fields:
    defaultMode: keep
    headers:
      defaultmode: keep
```
- **Access Logs**:
  - Configures the access log settings.
  - `filepath`: The path to the access log file.
  - `format`: The format of the access log entries (JSON in this case).
  - `addInternals`: Adds internal information to the logs.
  - `fields`: Defines which fields to include in the logs, with `defaultMode` set to `keep` and headers also kept.

```yaml
experimental:
  plugins:
    cloudflarewarp:
      moduleName: github.com/BetterCorp/cloudflarewarp
      version: v1.3.3
```
- **Experimental Features**:
  - Enables experimental features, specifically a plugin named `cloudflarewarp`.
  - `moduleName`: The module name of the plugin.
  - `version`: The version of the plugin.

This configuration sets up Traefik to handle HTTPS traffic using Let's Encrypt certificates obtained through DNS challenges with Cloudflare. It also enables logging and monitoring features, including an API dashboard for management.

## Config.yml File
The `config.yml` file you provided is a configuration file for Traefik, which is a popular open-source reverse proxy and load balancer. Below is an explanation of the different sections and their purposes within this configuration file:

### HTTP Configuration

```yaml
http:
  routers:
    traefik-secured:
      entryPoints:
        - "https"
      rule: "Host(`traefik.yourdomain.com`)"
      middlewares:
        - default_headers
        - cloudflarewarped
      tls:
        certResolver: cloudflare
        domains:
          - main: "yourdomain.com"
            sans:
              - "*.yourdomain.com"
      service: api@internal

    example:
      entryPoints:
        - "https"
      rule: "Host(`example.yourdomain.com`)"
      middlewares:
        - default_headers
        - cloudflarewarped
      tls:
        certResolver: cloudflare
        domains:
          - main: "yourdomain.com"
            sans:
              - "*.yourdomain.com"
      service: example
```

- **Routers**: Define how requests are routed to services.
  - **traefik-secured**: Routes traffic for `traefik.yourdomain.com` over HTTPS using the Cloudflare certificate resolver. It applies two middlewares (`default_headers` and `cloudflarewarped`) and points to the internal Traefik API.
  - **example**: Routes traffic for `example.yourdomain.com` over HTTPS, also using the Cloudflare certificate resolver, with the same middlewares applied and pointing to a service named `example`.

### Services

```yaml
services:
  example:
    loadBalancer:
      servers:
        - url: "https://exampleIP:examplePort"
      passHostHeader: true
      sticky:
        cookie:
          name: example
          secure: true
          SameSite: strict
```

- **Services**: Define the backend services that will handle requests.
  - **example**: Uses a load balancer to distribute traffic to a server running at `https://exampleIP:examplePort`. The `passHostHeader` option ensures that the original host header is passed to the backend service. Sticky sessions are enabled using a cookie with specific security settings.

### Middlewares

```yaml
middlewares:
  default_headers:
    headers:
      frameDeny: true
      browserXssFilter: true
      contentTypeNosniff: true
      forceSTSHeader: true
      stsIncludeSubdomains: true
      stsPreload: true
      stsSeconds: 15552000
      customFrameOptionsValue: SAMEORIGIN

  cloudflarewarped:
    plugin:
      cloudflarewarp:
        disableDefault: false
        trustip:
          - "<cloudflareContainerIP>"
```

- **Middlewares**: Define processing layers that can be applied to requests.
  - **default_headers**: Applies various security headers to enhance the security of responses, such as preventing clickjacking (`frameDeny`), mitigating XSS attacks (`browserXssFilter`), and ensuring content type integrity (`contentTypeNosniff`).
  - **cloudflarewarped**: Uses a plugin (likely `cloudflarewarp`) to handle IP trust issues, allowing Traefik to trust requests coming from Cloudflare's network.