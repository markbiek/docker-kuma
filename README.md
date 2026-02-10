# Uptime Kuma Docker Setup

Portable [Uptime Kuma](https://github.com/louislam/uptime-kuma) deployment using Docker Compose.

## Quick Start

1. Clone this repo and start Kuma:

```bash
git clone <repo-url>
cd docker-kuma
docker compose up -d
```

2. Kuma is now running on port 3001. Set up a reverse proxy to expose it externally (see below).

3. Visit your domain in a browser. On first launch, you'll be prompted to create an admin account.

## Configuration

Edit `.env` to change the host port:

```
KUMA_PORT=3001
```

## Reverse Proxy Setup

Example configs are in `examples/`. Both are configured to listen on port 80 — after setup, use Certbot to add SSL.

### nginx

```bash
sudo cp examples/nginx/uptime-kuma.conf /etc/nginx/sites-available/uptime-kuma
sudo sed -i 's/YOUR_DOMAIN/mon.example.com/' /etc/nginx/sites-available/uptime-kuma
sudo ln -s /etc/nginx/sites-available/uptime-kuma /etc/nginx/sites-enabled/
sudo nginx -t && sudo systemctl reload nginx
```

### Apache

```bash
sudo a2enmod proxy proxy_http proxy_wstunnel rewrite
sudo cp examples/apache/uptime-kuma.conf /etc/apache2/sites-available/uptime-kuma.conf
sudo sed -i 's/YOUR_DOMAIN/mon.example.com/' /etc/apache2/sites-available/uptime-kuma.conf
sudo a2ensite uptime-kuma
sudo systemctl reload apache2
```

### SSL with Certbot

```bash
# nginx
sudo certbot --nginx -d mon.example.com

# Apache
sudo certbot --apache -d mon.example.com
```

## Data

Kuma stores its SQLite database and config in `./data/`. This directory is git-ignored. Back it up however you normally back up your servers.

## Migrating from an Existing Install

If you have Kuma running in a named Docker volume, export the data first:

```bash
docker cp uptime-kuma:/app/data ./data
```

Then stop the old container and start this compose stack.
