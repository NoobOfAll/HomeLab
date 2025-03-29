# Nextcloud
Nextcloud is an open-source, self-hosted file synchronization and sharing platform. It provides users with control over their data by allowing them to store files on their own servers instead of relying on third-party cloud services. The application offers a wide range of features that mimic those found in popular cloud storage solutions like Dropbox or Google Drive, but with the added benefit of privacy and security since all data is stored within your control.

---
## Key Features:

- File Synchronization: Users can synchronize files across multiple devices, ensuring they always have access to their latest documents.
  
- Collaboration Tools: Nextcloud includes applications for teamwork such as Collaborative Documents, Calendars, and Tasks, which allow users to work on projects together in real-time.

- Data Security: With data stored on your own server, it is protected from unauthorized access. Encryption options are available to further secure files both at rest and during transmission.

- Customizability: Nextcloud supports a wide range of applications (referred to as apps) that can be installed to extend its functionality, catering to various needs such as document management, backups, and communication tools.

- Integration with Other Services: It can integrate with other services like LDAP for user authentication, SSO for single sign-on, and external storage systems like S3 or Ceph for additional storage options.

---
## Main Uses:

- Business Data Management: Organizations can use Nextcloud to securely store and share sensitive documents among employees while maintaining full control over their data.

- Personal Cloud Storage: Individuals can host their own cloud service at home, ensuring that all personal files are stored locally without the need for third-party services.

- Education: Schools and universities can deploy Nextcloud to provide a secure environment for students and teachers to share course materials, collaborate on projects, and manage digital assignments.

- Media Streaming and Backup: Users can store their media libraries in Nextcloud and stream them from any device with internet access, while also using it as a backup solution for important data.

- Remote Work Environment: Teams working remotely can use Nextcloud to share files, collaborate on documents, and manage project timelines, all without the risks associated with third-party cloud services.

---
## Docker Compose File Explanation
The `docker-compose.yml` file is a configuration file used to define and run multi-container Docker applications. Below is an explanation of each section and component in the provided `docker-compose.yml` file:

### Networks
```yaml
networks:
  dockernetwork:
    external: true
```
- dockernetwork: This defines a custom network named `dockernetwork`.
- external: true: Specifies that this network is created outside of Docker Compose and should be used as-is.

### Services

#### Nextcloud Service
```yaml
services:
  nextcloud:
    image: linuxserver/nextcloud:latest
    hostname: nextcloud
    container_name: nextcloud
    security_opt:
      - no-new-privileges:true
    networks:
      dockernetwork:
        ipv4_address: 172.28.1.98
    ports:
      - 443:443
    volumes:
      - ./nextcloud/data:/data
      - ./nextcloud/config:/config
    environment:
      TZ: Asia/Kolkata
      PUID: 1000
      PGID: 1000
    restart: unless-stopped
```
- image: Specifies the Docker image to use for Nextcloud.
- hostname and container_name: Define the hostname and container name for the service.
- security_opt: Ensures that no new privileges are granted to the container.
- networks: Connects the Nextcloud container to `dockernetwork` with a specific IPv4 address.
- ports: Maps port 443 on the host to port 443 in the container, allowing HTTPS access.
- volumes: Mounts directories from the host to the container for persistent storage of data and configuration.
- environment: Sets environment variables such as timezone (`TZ`), user ID (`PUID`), and group ID (`PGID`).
- restart: Ensures that the Nextcloud service restarts unless explicitly stopped.

#### Redis Service
```yaml
  redis:
    image: redis
    hostname: redis
    container_name: redis
    security_opt:
      - no-new-privileges:true
    networks:
      dockernetwork:
        ipv4_address: 172.28.1.99
    expose:
      - 6379
    volumes:
      - ./redis/data:/data
    environment:
      REDIS_PASSWORD: $REDIS_PASSWORD
      REDIS_IO_THREAD: 4
      REDIS_IO_THREADS_DO_READS: 'yes'
    restart: unless-stopped
```
- image: Specifies the Docker image to use for Redis.
- hostname and container_name: Define the hostname and container name for the service.
- security_opt: Ensures that no new privileges are granted to the container.
- networks: Connects the Redis container to `dockernetwork` with a specific IPv4 address.
- expose: Exposes port 6379 on the host, which is the default port for Redis.
- volumes: Mounts directories from the host to the container for persistent storage of data.
- environment: Sets environment variables such as `REDIS_PASSWORD`, number of I/O threads (`REDIS_IO_THREAD`), and whether I/O threads can perform read operations (`REDIS_IO_THREADS_DO_READS`).
- restart: Ensures that the Redis service restarts unless explicitly stopped.

#### PostgreSQL Service
```yaml
  postgresql:
    image: postgres:16
    hostname: postgresql
    container_name: postgresql
    security_opt:
      - no-new-privileges:true
    networks:
      dockernetwork:
        ipv4_address: 172.28.1.100
    expose:
      - 5432
    volumes:
      - ./postgresql/data:/var/lib/postgresql/data
    environment:
      POSTGRESQL_TIMEZONE: Asia/Kolkata
      POSTGRESQL_LOG_TIMEZONE: Asia/Kolkata
      ALLOW_EMPTY_PASSWORD: no
      POSTGRES_USER: $POSTGRES_USER
      POSTGRES_PASSWORD: $POSTGRES_PASSWORD
      POSTGRES_DB: "nextcloud"
    restart: unless-stopped
```
- image: Specifies the Docker image to use for PostgreSQL.
- hostname and container_name: Define the hostname and container name for the service.
- security_opt: Ensures that no new privileges are granted to the container.
- networks: Connects the PostgreSQL container to `dockernetwork` with a specific IPv4 address.
- expose: Exposes port 5432 on the host, which is the default port for PostgreSQL.
- volumes: Mounts directories from the host to the container for persistent storage of data.
- environment: Sets environment variables such as timezone (`POSTGRESQL_TIMEZONE`), log timezone (`POSTGRESQL_LOG_TIMEZONE`), user credentials (`POSTGRES_USER`, `POSTGRES_PASSWORD`), and database name (`POSTGRES_DB`).
- restart: Ensures that the PostgreSQL service restarts unless explicitly stopped.

### Summary
This `docker-compose.yml` file defines three services: Nextcloud, Redis, and PostgreSQL. Each service is configured with specific images, network settings, volumes, environment variables, and restart policies to ensure a robust and secure multi-container application setup.

## Common Nextcloud OCC commands

#### Update all Apps

```sh
occ app:update --all
```

#### Add missing Database Indices

```sh
occ db:add-missing-indices
```

#### Repair Files Structure

```sh
occ files:repair-tree
```

#### Clear Files Cache

```sh
occ files:cleanup
```

#### Scan New Files

```sh
occ files:scan --all
```

#### update the systems data-fingerprint

```sh
occ maintenance:data-fingerprint
```

#### Enable Maintenance Mode

```sh
occ maintenance:mode --on
```

#### Disable Maintenance Mode

```sh
occ maintenance:mode --off
```

#### Update database mimetypes and update filecache

```sh
occ maintenance:mimetype:update-db
```

#### Update mimetypelist.js

```sh
occ maintenance:mimetype:update-js
```

#### Repair

```sh
occ maintenance:repair --include-expensive
```

#### Update .htaccess

```sh
occ maintenance:update:htaccess
```

## Common Configurations changes for Nextcloud 
### Basic config.php changes for nextcloud

> Nextcloud/config/www/nextcloud/config/config.php

```php
<?php
$CONFIG = array (
# default settings and add
  'trusted_domains' =>
  array (
    0 => 'mydomain.com',
    1 => '<internal-ip>:443',
  ),
  'upload_max_filesize' => '10G',
  'post_max_size' => '10G',
  'default_locale' => 'en_IN',
  'default_phone_region' => 'IN',
  'default_timezone' => 'Asia/Kolkata',
  'filelocking.enabled' => true,
  'redis' =>
  array (
    'host' => 'redis', # Redis container name in docker-compose.yml
    'port' => '6379',
    'timeout' => 0.0,
    'user' => 'default',
    'password' => 'REDIS_PASSWORD', # Replace with your actual Redis password
  ),
  'apcu_enabled' => true,
  'apcu_ttl' => 0,
  'apcu_imagethreshold' => 8,
  'memcache.local' => '\\OC\\Memcache\\Redis',
  'memcache.distributed' => '\\OC\\Memcache\\Redis',
  'memcache.locking' => '\\OC\\Memcache\\Redis',
);
```

### Configuration for Nextcloud NGINX Site conf

> Nextcloud/config/nginx/site-confs/default.conf

```conf
# display real ip in nginx logs when connected through reverse proxy via docker network
    set_real_ip_from 172.28.1.2; # Reverse Proxy container IP
    set_real_ip_from 172.28.1.3; # Cloudflare Tunnel (If used) container IP
    set_real_ip_from <internal-ip>; # IP Of the VPS
    real_ip_header X-Forwarded-For; # The header field name to determine the client's IP address.
    real_ip_recursive on; # Enables recursive determination of the client's IP address
```

```conf
# set max upload size and increase upload timeout:
    client_max_body_size 10240M; # Set the maximum allowed size of an uploaded file to 10GB.
    client_body_timeout 600s; # Set the timeout for reading the client request body
    fastcgi_buffers 64 32K; # Increase the number of fastCGI buffers to 64, each with a size of 32KB.
```

### Configuration for Nextcloud PHP ini

> Nextcloud/config/php/php-local.ini

```ini
; Edit this file to override php.ini directives
memory_limit = 8G
upload_max_filesize = 10G
post_max_size = 10G
max_input_time = 3600
max_execution_time = 3600
opcache.enable = 1
opcache.memory_consumption = 1024
opcache.jit = 1255
opcache.jit_buffer_size = 512M
opcache.revalidate_freq=0
opcache.validate_timestamps=0 ;(comment this out in your dev environment)
opcache.max_accelerated_files=7963
opcache.interned_strings_buffer=16
opcache.fast_shutdown=1
```

### Configuration for Nextcloud www-conf

> Nextcloud/config/php/www2.conf

```conf
pm = dynamic # Use dynamic process management
pm.max_children = 120 # Maximum number of child processes to spawn
pm.max_requests = 500 # Maximum number of requests a child process will handle before respawning
pm.start_servers = 12 # Number of child processes to start at startup
pm.min_spare_servers = 6 # Minimum number of idle child processes to keep around
pm.max_spare_servers = 18 # Maximum number of idle child processes to keep around
```