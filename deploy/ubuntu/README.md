# Deploy the browser game on Ubuntu Server

This guide is for serving a built Vite/Express browser game on your Ubuntu server. The current repository is a project brief; it does not contain the referenced game's source code. The local reference checkout is intentionally excluded from Git. Only deploy code and art that you have permission to publish. The reference `natethedrummer/stickman` checkout has no root license file, so confirm its terms before making it publicly available.

## Requirements

- Ubuntu Server with a public route to it (public IP with ports forwarded, or a tunnel provider).
- A domain name whose DNS points to the server, for automatic HTTPS through Caddy.
- Node.js 18 or newer, npm, Git, and Caddy.
- The game source placed at `/opt/stickman/game`, with `package.json` and `server.js`.

## Build and install the app

Create a dedicated account and install the game source under `/opt/stickman/game`. For a game based on Vite, build it for the domain root so links use `/` rather than `/stickman/`:

```bash
sudo adduser --system --group --home /opt/stickman stickman
sudo mkdir -p /opt/stickman/game
sudo chown -R stickman:stickman /opt/stickman
```

As the `stickman` account, put the authorized game source in that folder, then run:

```bash
cd /opt/stickman/game
npm ci --no-audit --no-fund
npm run build -- --base=/
```

Check that `server.js` serves the generated `dist/` folder and reads `PORT` from the environment. Express may listen on all interfaces by default, so block inbound port 8080 in the Ubuntu firewall and any cloud firewall/router. Caddy will connect to port 8080 locally; keep it closed to the public internet.

## Start it with systemd

Copy the included unit file, then enable the service:

```bash
sudo install -m 0644 deploy/ubuntu/stickman.service /etc/systemd/system/stickman.service
sudo systemctl daemon-reload
sudo systemctl enable --now stickman
sudo systemctl status stickman
```

Inspect logs with `sudo journalctl -u stickman -f`.

## Add HTTPS and a public address

Install Caddy using its official Ubuntu instructions. Copy `Caddyfile.example` to `/etc/caddy/Caddyfile`, replacing `game.example.com` with your domain, then validate and reload:

```bash
sudo caddy validate --config /etc/caddy/Caddyfile
sudo systemctl reload caddy
```

Point the domain's DNS record to your server's public IP. Allow inbound TCP ports 80 and 443 in the Ubuntu firewall and any cloud firewall/router; forward those ports to the Ubuntu server if it is behind a home router. Caddy will obtain and renew HTTPS certificates automatically. Do not expose port 8080 publicly.

If the ISP uses CGNAT or inbound ports cannot be forwarded, use a managed tunnel (for example, Cloudflare Tunnel) and route its public hostname to `http://localhost:8080`. Keep the origin port private.

## Updates

Fetch authorized source changes, then rebuild and restart:

```bash
cd /opt/stickman/game
sudo -u stickman git pull
sudo -u stickman npm ci --no-audit --no-fund
sudo -u stickman npm run build -- --base=/
sudo systemctl restart stickman
```

## Local network only

For devices on the same LAN, access the server's private address (for example, `http://192.168.1.20:8080`) after allowing that port on the local firewall. This does not make it reachable from outside the network; use the domain/HTTPS or tunnel setup above for access from anywhere.
