# Media Server Deployment Guide

This guide explains how to deploy and configure the media server stack, including testing the VPN tunnel and setting up services with indexers.

---

## Prerequisites

1. **Docker and Docker Compose**: Ensure Docker and Docker Compose are installed on your system.
2. **Environment Variables**: Update the `.env` file with your credentials and directory paths.
   - Example:
     ```env
     OPENVPN_USER=your_vpn_username
     OPENVPN_PASSWORD=your_vpn_password
     MEDIA_SERVER_MEDIA=/path/to/your/media
     ```

---

## Deployment Steps

1. **Clone the Repository**:
   ```bash
   git clone <repository-url>
   cd /workspaces/homelab/media-server
   ```

2. **Update the `.env` File**:
   - Open `/workspaces/homelab/media-server/.env` and fill in the required values.

3. **Start the Stack**:
   ```bash
   docker-compose -f jellyfin-deployment.yml -f torrent-stack.yml up -d
   ```

4. **Verify Services**:
   - Run `docker ps` to ensure all containers are running.
   - Access the services via their respective ports:
     - Jellyfin: `http://<IP_ADDR>:8096`
     - qBittorrent: `http://<IP_ADDR>:8080`
     - Sonarr: `http://<IP_ADDR>:8989`
     - Radarr: `http://<IP_ADDR>:7878`
     - Prowlarr: `http://<IP_ADDR>:9696`
     - Overseerr: `http://<IP_ADDR>:5055`

---

## Testing the VPN Tunnel

1. **Access the qBittorrent Web UI**:
   - Navigate to `http://<IP_ADDR>:8080`.

2. **Download a Test Torrent**:
   - Add a public domain torrent (e.g., a Linux ISO) to qBittorrent.

3. **Verify VPN Usage**:
   - Check the IP address used for downloading:
     ```bash
     docker exec -it gluetun curl ifconfig.me
     ```
   - Ensure the IP address matches your VPN provider's IP.

---

## Setting Up Indexers

1. **Access Prowlarr**:
   - Navigate to `http://<IP_ADDR>:9696`.

2. **Add Indexers**:
   - Go to the "Indexers" tab and click "Add Indexer."
   - Select your preferred indexer (e.g., 1337x, RARBG).
   - Configure the indexer with your credentials (if required).

3. **Link Prowlarr to Sonarr and Radarr**:
   - In Prowlarr, go to "Apps" and add Sonarr and Radarr.
   - Provide the URLs and API keys for Sonarr (`http://<IP_ADDR>:8989`) and Radarr (`http://<IP_ADDR>:7878`).

---

## Configuring Sonarr and Radarr

1. **Sonarr**:
   - Navigate to `http://<IP_ADDR>:8989`.
   - Add your TV show library under "Settings > Media Management."
   - Configure download clients:
     - Go to "Settings > Download Clients."
     - Add qBittorrent with the URL `http://gluetun:8080`.

2. **Radarr**:
   - Navigate to `http://<IP_ADDR>:7878`.
   - Add your movie library under "Settings > Media Management."
   - Configure download clients:
     - Go to "Settings > Download Clients."
     - Add qBittorrent with the URL `http://gluetun:8080`.

---

## Additional Notes

- **Overseerr**:
  - Use Overseerr (`http://<IP_ADDR>:5055`) to allow users to request content.
  - Configure Overseerr to link with Sonarr and Radarr under "Settings > Services."

- **Flaresolverr**:
  - Ensure Flaresolverr is running (`http://<IP_ADDR>:8191`) for indexers that require bypassing Cloudflare.

---

## Troubleshooting

- **Containers Not Starting**:
  - Check logs with `docker logs <container_name>`.

- **VPN Issues**:
  - Verify VPN credentials in the `.env` file.
  - Check Gluetun logs: `docker logs gluetun`.

- **Indexers Not Working**:
  - Ensure Flaresolverr is running for indexers like 1337x.
  - Verify indexer credentials in Prowlarr.

---

This guide should help you deploy and configure your media server stack successfully. For further assistance, consult the documentation for each service.
