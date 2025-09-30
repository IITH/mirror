# SETUP Guide — mirror.iith.ac.in

> This guide walks you through setting up and replicating the internal IITH mirror service for Ubuntu releases and apt-cacher-ng.  

---
## Initial Setup
Follow these steps for a fresh installation.

1. **Install Caddy**
```bash
# Install required dependencies
sudo apt update
sudo apt install -y debian-keyring debian-archive-keyring apt-transport-https curl

# Add the official Caddy repository
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/gpg.key' | sudo gpg --dearmor -o /usr/share/keyrings/caddy-stable.gpg
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/debian.deb.txt' | sudo tee /etc/apt/sources.list.d/caddy-stable.list

# Install Caddy
sudo apt update
sudo apt install -y caddy
```

2. **Enable and Start Caddy**
```bash
sudo systemctl enable caddy
sudo systemctl start caddy
```

3. **Configure Caddyfile**
```bash
sudo nano Caddyfile
```
Paste the Caddyfile contents from this repository.

---

### 1. Directory Structure

All mirror files and templates are organized under `/srv/mirror`:

```
/srv/mirror/
├── ubuntu/              # (Optional) Full Ubuntu package mirror
├── ubuntu/cdimages/     # Ubuntu ISO images & CD releases
└── ...

/srv/templates/          # Directory listing templates
├── index.html
└── mirror-browse.html
```

- **Cached apt packages:**  
  Stored by apt-cacher-ng in `/var/cache/apt-cacher-ng/`

- **Caddy configuration:**  
  Located at `/etc/caddy/Caddyfile`

---

### 2. Install Dependencies

On your Ubuntu/Debian server, run:

```bash
sudo apt update
sudo apt install -y apt-cacher-ng rsync
```

- **apt-cacher-ng:** Caches `.deb` packages on demand.
- **rsync:** Synchronizes Ubuntu CD images from upstream mirrors.

---

## 3. Sync Ubuntu Releases

Create the directory and sync Ubuntu CD images:

```bash
sudo mkdir -p /srv/mirror/ubuntu/cdimages
sudo rsync -a --delete rsync://cdimage.ubuntu.com/cdimage/releases/ /srv/mirror/ubuntu/cdimages/
```

To automate daily sync at 2 AM, add this cron job:

```bash
0 2 * * * rsync -a --delete rsync://cdimage.ubuntu.com/cdimage/releases/ /srv/mirror/ubuntu/cdimages/
```

---

## 4. Configure apt-cacher-ng

The default configuration is suitable for most environments.

- **Config file:** `/etc/apt-cacher-ng/acng.conf`
- **Cache storage:** `/var/cache/apt-cacher-ng/`

Start and enable the service.  
`apt-cacher-ng` will cache `.deb` packages as clients request them.

- Metadata files (`Release`, `Packages.gz`) are refreshed automatically when clients run `apt update`.
- Cached `.deb` files are stored immutably and reused for future requests.

---

## 5. How It Works

### Directory Listing / Browse Template

- Requests to `/` or `/ubuntu-releases/` are served by Caddy’s `file_server`.
- Custom templates (`index.html`) provide a user-friendly HTML view for directories.

### Ubuntu ISOs

- All ISO images are stored under `/srv/mirror/ubuntu/cdimages`.

### APT Caching

- IITH clients configure their systems to use the mirror as an apt proxy:  
  `http://mirror.iith.ac.in/apt/`
- Packages are downloaded from upstream only if not already cached locally.
- Metadata files are updated automatically whenever clients request them.