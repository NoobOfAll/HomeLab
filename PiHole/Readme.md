# PiHole

### Networks

```yaml
networks:
  dockernetwork:
    external: true
```
- **networks**: This section defines the networks that are used by the services in the `docker-compose.yml` file.
- **dockernetwork**: This specifies a network named `dockernetwork`.
- **external: true**: This indicates that the network is an external Docker network, meaning it was created outside of this `docker-compose.yml` and is not managed by this setup.

### Services

```yaml
services:
  pi-hole:
    image: pihole/pihole:latest
    hostname: pi-hole
    container_name: pi-hole
```
- **services**: This section defines the services that make up your application.
- **pi-hole**: This is the name of the service, which will be used to reference it within this `docker-compose.yml`.
- **image: pihole/pihole:latest**: This specifies the Docker image to use for the service. The latest version of the Pi-hole image from the official repository is used here.
- **hostname: pi-hole**: This sets the hostname inside the container.
- **container_name: pi-hole**: This assigns a specific name to the container when it's run.

### Security Options

```yaml
    security_opt:
      - no-new-privileges:true
```
- **security_opt**: This section allows you to specify security options for the container.
- **no-new-privileges: true**: This option prevents any new privileges from being granted inside the container, enhancing security by preventing processes within the container from gaining additional capabilities.

### Networks Configuration

```yaml
    networks:
      dockernetwork:
        ipv4_address: 172.28.1.97
```
- **networks**: This section configures the network settings for the service.
- **dockernetwork**: This specifies that the service should join the `dockernetwork`.
- **ipv4_address: 172.28.1.97**: This assigns a specific IP address within the network to the container.

### Ports

```yaml
    ports:
      - "8081:80/tcp" # Pi-hole web admin interface, proxied through Caddy (configure port in Caddyfile)
      - "53:53/udp"
      - "53:53/tcp"
```
- **ports**: This section maps the ports of the container to the host machine.
- **8081:80/tcp**: This maps port 80 inside the container to port 8081 on the host, allowing access to the Pi-hole web admin interface.
- **53:53/udp** and **53:53/tcp**: These map ports 53 (both UDP and TCP) from the container to the same ports on the host, which are used for DNS queries.

### Environment Variables

```yaml
    environment:
      PUID: '1000'
      PGID: '1000'
      TZ: 'Asia/Kolkata'
      FTLCONF_webserver_api_password: $PIHOLE_PWD
```
- **environment**: This section sets environment variables inside the container.
- **PUID: '1000'** and **PGID: '1000'**: These specify the user ID (UID) and group ID (GID) that the Pi-hole service should run as.
- **TZ: 'Asia/Kolkata'**: This sets the timezone inside the container to Asia/Kolkata.
- **FTLCONF_webserver_api_password: PIHOLE_PWD**: This sets a password for accessing the Pi-hole API, using an environment variable `$PIHOLE_PWD`.

### Volumes

```yaml
    volumes:
      - './etc-pihole:/etc/pihole'
      - './etc-dnsmasq.d:/etc/dnsmasq.d'
```
- **volumes**: This section mounts directories from the host into the container.
- **./etc-pihole:/etc/pihole**: This mounts the `./etc-pihole` directory on the host to `/etc/pihole` in the container, allowing persistent storage of Pi-hole configuration files.
- **./etc-dnsmasq.d:/etc/dnsmasq.d**: This mounts the `./etc-dnsmasq.d` directory on the host to `/etc/dnsmasq.d` in the container, allowing for custom dnsmasq configuration files.

### Restart Policy

```yaml
    restart: unless-stopped
```
- **restart**: This specifies the restart policy for the container.
- **unless-stopped**: This means that the container will always start on boot and only stop if explicitly stopped by the user.