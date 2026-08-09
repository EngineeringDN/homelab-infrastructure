# homelab-infrastructure

Infrastructure-as-code and documentation for my self-hosted homelab.

## Goals
- **Reproducible** deployments
- **LAN-first** by default (no public exposure unless explicitly enabled

## High-level stack
- **Hypervisor**: Proxmox (VMs + LXC)
- **Containers**: Docker / Docker Compose (via Portainer)
- **Storage**: NFS shares from a dedicated storage host (OMV)
- **Networking**: local DNS + reverse proxy for internal HTTPS (NPM/Nginx Proxy Manager)
- **Apps**: Immich, Nextcloud, Paperless(-ngx), Vikunja, Booklore (and more)