# Changes And Notes On This Slightly Edited Docker Compose

## Cloudflare Tunnel And Cloudflare Access

In this setup, I used Cloudflare Tunnel and Cloudflare Access for authentication to the knowledge base which was provisioned as an internal-only resource. Because Cloudflare Tunnel also handles SSL, I took the `https-portal` service out of the script.

Note: I prefer to set up Cloudflare Tunnels directly on the machine so the Docker container for `cloudflared` isn't in the `docker-compose`. But you could add it in to streamline deployment even further. Run the end of tunnel to 

I created the Cloudflare Tunnel remotely, installed the daemon on the server, configured a public hostname, and then routed traffic to:

`localhost:3000` 

matching the external port configured in the Docker Compose.

## Added: Persistent Bind Points For Postgres And Redis

Edits to `docker-compose.yml` to create bind points on the host for the container volumes' data:

```
volumes:
      - /srv/docker/outline/storage-data:/var/lib/outline/data
      
      
      
volumes:
      - /srv/docker/outline/redis.conf:/redis.conf
      
volumes:
      - /srv/docker/outline/database-data:/var/lib/postgresql/data
```

### Create bind points on host before running

Run these commands before using `docker-compoose up -d` in order to create the necessary local directories for the volume to bind to in order to ensure that data persists reliably (in my case, I was deploying onto a VM).

```
sudo mkdir -p /srv/docker/outline/storage-data
sudo mkdir -p /srv/docker/outline/database-data
sudo touch /srv/docker/outline/redis.conf
```

Then, make sure that Docker has adequate permissions to read/write to those directories on the host machine.

---

## `Docker.Env` Handling

The installation method that Outline recommends suggests creating `docker.env` to hold environment variables. 

Set:

`FILE_STORAGE_LOCAL_ROOT_DIR=/var/lib/outline/storage-data`

to match.

## Postgres SSL Disabled

My environment required this change so it's made in the config:

```
    environment:
      PGSSLMODE: disable
```
