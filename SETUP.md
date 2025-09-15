# SETUP Guide — mirror.iith.ac.in

> This guide explains how to set up and replicate the internal IITH mirror service for Ubuntu releases and apt-cacher-ng.  

---

## 1. Directory Structure

All mirror files and templates are stored on the mirror server under `/srv/mirror`:

```
/srv/mirror/
├── ubuntu/              # Optional: future full Ubuntu package mirror
├── ubuntu/cdimages/     # Ubuntu ISO images & CD releases
└── ...

/srv/templates/          # Browse templates for directory listings
├── index.html
└── mirror-browse.html
```

- **Cached apt packages** are stored by apt-cacher-ng in:  
  `/var/cache/apt-cacher-ng/`

- **Caddy configuration** lives at:  
  `/etc/caddy/Caddyfile`

---

## 2. Install Dependencies

On Ubuntu/Debian server, run:

```bash
sudo apt update
sudo apt install -y caddy apt-cacher-ng rsync
```

- **Caddy:** Serves mirror files and reverse-proxies apt requests.
- **apt-cacher-ng:** Handles on-demand caching of `.deb` packages.
- **rsync:** Used to sync Ubuntu CD images from upstream mirrors.

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

The default configuration works for most setups.

- **Config file:** `/etc/apt-cacher-ng/acng.conf`
- **Cache storage:** `/var/cache/apt-cacher-ng/`

Start and enable the service.  
`apt-cacher-ng` caches `.deb` packages on-demand.

- Metadata files (`Release`, `Packages.gz`) are automatically refreshed when clients request updates.
- Cached `.deb` files remain in storage; they are immutable and safely reused.

---

## 5. How Things Work

### Directory Listing / Browse Template

- Requests to `/` or `/ubuntu-releases/` are served by Caddy’s `file_server`.
- Custom templates (`index.html`) provide nicer HTML views for directories.

### Ubuntu ISOs

- Stored under `/srv/mirror/ubuntu/cdimages`.

### APT Caching

- Clients inside IITH use the mirror as an apt proxy:  
  `http://mirror.iith.ac.in/apt/`
- Packages are fetched from upstream only if not already cached.
- Metadata files are automatically updated on request.