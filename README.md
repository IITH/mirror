# mirror.iith.ac.in ![Status](https://img.shields.io/badge/status-active-brightgreen) ![Scope](https://img.shields.io/badge/scope-private-blue)

> **Note:** This is **not a public mirror**. It is a **private mirror for use inside IIT Hyderabad (IITH)**.  
> External access is not available.

---

## 📌 Overview

`mirror.iith.ac.in` hosts a local package and ISO mirror to reduce external bandwidth usage and improve speed for users inside IITH.  
Currently supported:

- **Ubuntu Releases (ISOs, CD Images)**
- **APT Proxy via apt-cacher-ng**

Other Linux distributions will be supported later.

---

## 📂 Available Paths

- Ubuntu Releases (CD Images):  
  [http://mirror.iith.ac.in/ubuntu-releases/](http://mirror.iith.ac.in/ubuntu-releases/)

- APT Proxy (via apt-cacher-ng):  
  [http://mirror.iith.ac.in/apt/](http://mirror.iith.ac.in/apt/)

---

## ⚡ Usage Guide (for IITH users)

### 1. Use for APT Updates

Update your `/etc/apt/sources.list` to point to the mirror proxy:

```bash
sudo sed -i 's|http://archive.ubuntu.com/ubuntu/|http://mirror.iith.ac.in/apt/ubuntu/|g' /etc/apt/sources.list
sudo apt update
```

## 2. Download Ubuntu ISOs

Instead of using external mirrors, directly download ISOs from:
```
http://mirror.iith.ac.in/ubuntu-releases/
```

⚠️ **Notes**

- This mirror is intended only for IITH internal network users.
- External/public use is not supported.
- Storage is limited; hence, not all versions may be retained.

🚀 **Roadmap**

- Add support for other distributions (Arch Linux, Fedora, Debian, etc.)
- Provide rsync access for internal mirroring.
- Add monitoring and usage statistics dashboard.

🛠️ **Setup Details (for admins)**

- Web Server: Caddy
- APT Proxy: apt-cacher-ng
- Sync: rsync from official upstream mirrors
