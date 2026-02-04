# MinnowVPN

[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](LICENSE)
[![Status](https://img.shields.io/badge/Status-Alpha-orange.svg)]()
[![Under Development](https://img.shields.io/badge/Under%20Development-⚠️-yellow.svg)]()
[![Docker Pulls](https://img.shields.io/docker/pulls/minnowvpn/api)](https://hub.docker.com/r/minnowvpn/api)

> ⚠️ **Alpha Software**: MinnowVPN is under active development and not yet ready for production use. APIs and features may change without notice. Use at your own risk.

> **Note:** This is a reference repository. Bug reports are welcome, but pull requests will not be accepted.

**Small. Fast. Lean.** A self-hosted WireGuard VPN with modern web management.

## Why MinnowVPN?

| | |
|---|---|
| 🐟 **Small** | Lightweight Rust VPN daemon. Minimal resource footprint. Runs on a Raspberry Pi. |
| ⚡ **Fast** | Native WireGuard performance. Sub-second connections. Efficient encryption. |
| 🎯 **Lean** | No bloat. Deploy in minutes with Docker. Simple, focused, easy to maintain. |

Like its namesake, MinnowVPN is deliberately small and nimble. It provides a beautiful web console for managing clients, monitoring connections, and configuring SSO—all without touching the command line after initial setup.

## Who is it for?

- **Small businesses** wanting a self-hosted VPN without complex setup
- **IT administrators** who need centralized VPN client management
- **Teams** that want SSO integration with their existing identity provider
- **Home users** who want a user-friendly WireGuard management interface

## Features

### Core VPN
- **WireGuard protocol** - Fast, modern, and cryptographically secure
- **Multi-architecture support** - Runs on AMD64 and ARM64 (including Raspberry Pi)
- **Automatic key management** - No manual key generation required

### Web Management Console
- **Real-time dashboard** - Monitor active connections and bandwidth
- **Client management** - Add, edit, revoke, and organize VPN clients
- **Enrollment codes** - Generate one-time codes for easy device setup
- **Audit logging** - Track all administrative actions

### Enterprise Features
- **SSO integration** - Azure AD (Entra ID), Okta, and Google Workspace
- **Email notifications** - Send enrollment emails directly to users
- **API access** - RESTful API for automation and integrations

### Security & Operations
- **Automatic HTTPS** - Let's Encrypt certificates via Caddy
- **Fail2ban protection** - Automatic IP blocking for brute-force attacks
- **Auto-updates** - Watchtower keeps containers up to date
- **Docker secrets** - Sensitive data never stored in environment variables

### Desktop Clients
- **Native apps** - macOS, Windows, and Linux desktop clients
- **One-click enrollment** - Deep link enrollment from email invitations
- **Auto-reconnect** - Maintains connection through network changes

## Quick Start

### Prerequisites

- A server with Docker Engine 20.10+ and Docker Compose 2.0+
- A domain name pointing to your server (e.g., `vpn.company.com`)
- Ports 80, 443 (TCP) and 51820 (UDP) open in your firewall

### One-Command Setup

```bash
git clone https://github.com/minnowvpn/minnowvpn.git
cd minnowvpn
./scripts/setup.sh
```

The setup wizard will:
1. Check prerequisites
2. Prompt for your domain, email, and server IP
3. Generate secure random secrets
4. Pull and start all containers
5. Wait for services to become healthy

### First Login

1. Open `https://your-domain.com` in your browser
2. Create your admin account on the setup screen
3. Start adding VPN clients!

## Getting Started Guide

### Step 1: Deploy the Server

After running the setup wizard, verify all services are running:

```bash
docker compose ps
```

You should see all services with status "healthy" or "running".

### Step 2: Create Admin Account

Navigate to your domain in a web browser. On first visit, you'll see the setup screen where you create your administrator account.

### Step 3: Add VPN Clients

From the admin console:

1. Click **Clients** in the navigation
2. Click **Add Client**
3. Enter a name (e.g., "John's Laptop")
4. Optionally add an email address for enrollment emails
5. Click **Create**

### Step 4: Connect Devices

**Option A: Enrollment Code (Recommended)**

1. Generate an enrollment code for the client
2. On the user's device, either:
   - Click the enrollment email link, or
   - Open the MinnowVPN app and enter the code manually

**Option B: QR Code**

1. Click the QR icon next to a client
2. Scan with the WireGuard mobile app

**Option C: Manual Config**

1. Download the `.conf` file from the admin console
2. Import into any WireGuard-compatible client

## Client Setup

### Desktop App (Recommended)

Download the MinnowVPN desktop client for your platform:
- **macOS**: PKG installer
- **Windows**: MSI installer
- **Linux**: DEB or RPM package

The desktop app provides:
- One-click connect/disconnect
- System tray integration
- Automatic config updates
- SSO authentication support

### Enrollment Flow

1. Admin generates an enrollment code in the console
2. User receives an email with enrollment link
3. User clicks link, which opens the MinnowVPN app
4. App automatically configures and connects

### Manual WireGuard Config

If you prefer to use the official WireGuard app:

1. Download the config file from the admin console
2. Import into WireGuard:
   - **macOS/iOS**: WireGuard → Import from file
   - **Windows**: WireGuard → Import tunnel(s) from file
   - **Android**: WireGuard → + → Import from file or archive
   - **Linux**: `wg-quick up /path/to/config.conf`

## Admin Console

### Dashboard

The dashboard provides real-time visibility into your VPN:
- **Active connections** - Currently connected clients
- **Bandwidth usage** - Upload and download statistics
- **Recent activity** - Connection events and admin actions
- **Error summary** - Any issues requiring attention

### Client Management

Manage your VPN clients from a single interface:
- **Status indicators** - Green (connected), gray (offline), amber (warning)
- **Quick actions** - Edit, regenerate keys, revoke access
- **Bulk operations** - Select multiple clients for batch actions
- **Search and filter** - Find clients by name, email, or status

### Enrollment Codes

Streamline device onboarding:
- Generate time-limited codes (24-hour expiry)
- Send enrollment emails directly from the console
- Track code usage and expiration
- Revoke unused codes

### SSO Configuration

Connect your identity provider:

1. Go to **Settings** → **SSO Configuration**
2. Select your provider (Azure AD, Okta, or Google)
3. Enter your OAuth client credentials
4. Configure group mappings (optional)
5. Test the connection

### Audit Logs

Track all administrative actions:
- Login/logout events
- Client creation and modification
- Configuration changes
- Security events (failed logins, hostname mismatches)

## Architecture

```
Internet
    │
    ├─── TCP 80/443 ───► Caddy (HTTPS) ───► Dart API Server
    │                         │
    │                         └───────────► Flutter Console
    │
    └─── UDP 51820 ────► Rust VPN Daemon
```

### Services

| Service | Description | Port |
|---------|-------------|------|
| `postgres` | PostgreSQL 15 database | 5432 (internal) |
| `redis` | Redis 7 cache & pub/sub | 6379 (internal) |
| `dart-server` | REST API server | 8080 (internal) |
| `flutter-console` | Web management console | 80 (internal) |
| `vpn-daemon` | WireGuard VPN server | UDP 51820, TCP 51821 |
| `caddy` | Reverse proxy + HTTPS | 80, 443 |
| `watchtower` | Auto-updates | - |
| `fail2ban` | Brute-force protection | - |

### Monitoring Stack (Optional)

Enable with `docker compose --profile monitoring up -d`:

| Service | Description | Port |
|---------|-------------|------|
| `prometheus` | Metrics collection | 9090 (internal) |
| `grafana` | Dashboards | 3000 (internal) |
| `node-exporter` | Host metrics | 9100 (internal) |
| `redis-exporter` | Redis metrics | 9121 (internal) |
| `postgres-exporter` | PostgreSQL metrics | 9187 (internal) |

## Configuration

### Environment Variables

Copy `.env.example` to `.env` and customize:

```bash
# Required
DOMAIN=vpn.example.com
ACME_EMAIL=admin@example.com
VPN_PUBLIC_IP=1.2.3.4

# Optional
HTTP_PORT=80
HTTPS_PORT=443
GRAFANA_DOMAIN=grafana.example.com
TZ=UTC
```

### Secrets

Secrets are stored in the `secrets/` directory (auto-generated by setup.sh):

```
secrets/
├── db_password.txt         # PostgreSQL password
├── redis_password.txt      # Redis password
├── jwt_secret.txt          # JWT signing key
├── encryption_key.txt      # AES-256 encryption key
└── grafana_admin_password.txt  # Grafana admin password
```

**Never commit secrets to version control!**

## Running the Server

### Starting and Stopping

```bash
# Start services
docker compose up -d

# Start with monitoring stack
docker compose --profile monitoring up -d

# Stop services
docker compose down

# Stop and remove volumes (deletes all data!)
docker compose down -v
```

### Viewing Logs

```bash
# All services
docker compose logs -f

# Specific service
docker compose logs -f dart-server

# Last 100 lines
docker compose logs --tail 100 dart-server
```

### Health Checks

```bash
# Check service status
docker compose ps

# API health endpoint
curl https://your-domain.com/api/v1/health

# VPN daemon status
curl http://localhost:51821/api/v1/status
```

### Resource Requirements

Minimum recommended resources:
- **CPU**: 1 core
- **RAM**: 2 GB
- **Disk**: 10 GB

For larger deployments (100+ clients):
- **CPU**: 2+ cores
- **RAM**: 4+ GB
- **Disk**: 20+ GB (for logs and metrics)

## Security

### Fail2ban Protection

The following jails are configured:

| Jail | Trigger | Ban Time |
|------|---------|----------|
| `minnowvpn-auth` | 5 failed logins in 5 min | 1 hour |
| `minnowvpn-enrollment` | 10 failed enrollments in 1 min | 30 min |
| `minnowvpn-api` | 20 API errors in 1 min | 15 min |

Manage bans:

```bash
# View banned IPs
docker compose exec fail2ban fail2ban-client status minnowvpn-auth

# Unban an IP
docker compose exec fail2ban fail2ban-client set minnowvpn-auth unbanip 1.2.3.4
```

### Security Headers

Caddy automatically adds security headers:
- `Strict-Transport-Security` (HSTS)
- `X-Content-Type-Options`
- `X-Frame-Options`
- `Content-Security-Policy`
- `Permissions-Policy`

### Network Isolation

- Only Caddy is exposed to the internet (ports 80, 443)
- Internal services communicate via Docker network
- VPN daemon uses host networking for TUN device access
- Database and Redis are not externally accessible

### Secrets Management

- All sensitive values stored as Docker secrets
- Secrets mounted as files, never in environment variables
- File permissions restrict access to necessary containers

## Backup & Restore

### Create Backup

```bash
# Backup to default location
./scripts/backup.sh

# Backup to custom location
./scripts/backup.sh /path/to/backups
```

Backups include:
- PostgreSQL database dump
- Redis data
- Secrets directory
- Configuration files

### Restore from Backup

```bash
# From directory
./scripts/restore.sh ./backups/minnowvpn_20240101_120000

# From tarball
./scripts/restore.sh ./backups/minnowvpn_20240101_120000.tar.gz
```

**Always test restores on a staging environment first!**

## Upgrading

### Automatic Updates

Watchtower automatically updates containers daily at 4am UTC. Configure the schedule in `.env`:

```bash
WATCHTOWER_SCHEDULE=0 0 4 * * *
```

### Manual Update

```bash
# Pull latest images
docker compose pull

# Restart with new images
docker compose up -d
```

### Major Version Upgrade

For major version upgrades with breaking changes:

1. Create a backup
2. Review the changelog for migration steps
3. Stop services
4. Pull new images
5. Run any migration scripts
6. Start services

```bash
./scripts/backup.sh
git pull
docker compose down
docker compose pull
docker compose up -d
```

## Troubleshooting

### Services Not Starting

```bash
# Check logs for errors
docker compose logs dart-server
docker compose logs postgres

# Check health status
docker compose ps

# Restart a specific service
docker compose restart dart-server
```

### Certificate Issues

```bash
# Check Caddy logs
docker compose logs caddy

# Force certificate renewal
docker compose exec caddy caddy reload --config /etc/caddy/Caddyfile
```

### Database Connection Issues

```bash
# Check PostgreSQL is healthy
docker compose exec postgres pg_isready -U minnowvpn

# Check database logs
docker compose logs postgres

# Connect to database directly
docker compose exec postgres psql -U minnowvpn -d minnowvpn
```

### VPN Connection Issues

```bash
# Check VPN daemon status
curl http://localhost:51821/api/v1/status

# Check VPN logs
docker compose logs vpn-daemon

# Verify UDP port is reachable (from another machine)
nc -zvu your-server-ip 51820
```

### Client Can't Connect

1. Verify the client's allowed IPs don't conflict
2. Check the client isn't revoked in the admin console
3. Verify firewall allows UDP 51820
4. Check for NAT/routing issues on the server

## API Reference

MinnowVPN provides a RESTful API for automation and integrations.

### Authentication

All API requests require a JWT token obtained via login:

```bash
# Login
curl -X POST https://your-domain.com/api/v1/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email": "admin@example.com", "password": "your-password"}'

# Use token in subsequent requests
curl https://your-domain.com/api/v1/clients \
  -H "Authorization: Bearer YOUR_TOKEN"
```

### Key Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/api/v1/health` | Health check |
| `POST` | `/api/v1/auth/login` | Admin login |
| `GET` | `/api/v1/clients` | List all clients |
| `POST` | `/api/v1/clients` | Create client |
| `GET` | `/api/v1/clients/:id` | Get client details |
| `PUT` | `/api/v1/clients/:id` | Update client |
| `DELETE` | `/api/v1/clients/:id` | Delete client |
| `POST` | `/api/v1/clients/:id/enrollment-code` | Generate enrollment code |
| `GET` | `/api/v1/dashboard/stats` | Dashboard statistics |
| `GET` | `/api/v1/logs/audit` | Audit logs |

### WebSocket

Real-time updates are available via WebSocket:

```javascript
const ws = new WebSocket('wss://your-domain.com/api/v1/ws/dashboard?token=YOUR_TOKEN');

ws.onmessage = (event) => {
  const data = JSON.parse(event.data);
  console.log('Event:', data.type, data.data);
};
```

## Reporting Issues

Found a bug? Please open an issue on GitHub with:
- Steps to reproduce
- Expected vs actual behavior
- Platform and version information

For issues with the core VPN daemon, use the [minnowvpn-service repository](https://github.com/minnowvpn/minnowvpn-service/issues).

## License

MinnowVPN is licensed under the Apache License, Version 2.0. See [LICENSE](LICENSE) for the full license text.

```
Copyright 2024 MinnowVPN

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```
