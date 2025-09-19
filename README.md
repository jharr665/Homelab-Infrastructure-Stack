# Infrastructure & Virtualization Stack

Docs and examples for Proxmox, TrueNAS, OPNsense, networking, DR.
# Homelab Infrastructure & Virtualization Stack

> Proxmox VE hosts • TrueNAS (ZFS) storage VM • OPNsense firewall • VLAN-segmented network • Snapshots, replication & off-site backups.

[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](#license)
![Platform](https://img.shields.io/badge/Platform-Proxmox,_TrueNAS,_OPNsense-blue)
![Focus](https://img.shields.io/badge/Focus-Virtualization,_Storage,_Networking-orange)

This repo documents the **foundation** under the rest of my homelab: hypervisors, storage, and network. It’s designed to be **reproducible**, **secure by default**, and **easy to evolve** as I add app stacks (media, SOC, observability, etc.).

---

## ✨ Highlights

- **Proxmox VE** for virtualization (GPU/NIC/HBA passthrough ready)
- **TrueNAS VM** with **ZFS** (snapshots, replication, ARC/L2ARC notes)
- **OPNsense** firewall & routing (policy, DNS, VLANs, WireGuard)
- **Network layout** with **VLAN segmentation** (mgmt, storage, apps, SOC, pentest)
- **Backup/DR**: ZFS snapshots + rclone/restic off-site (Object Lock/WORM guidance)
- **Hardening**: 2FA, SSH certs, least privilege, restricted mgmt plane

---

## 🧭 High-Level Architecture

```mermaid
flowchart LR
  %% --- Subgraphs ---
  subgraph DC["Homelab Rack"]
    PVE1["Proxmox Node 1"];
    PVE2["Proxmox Node 2"];
    PVE3["Proxmox Node 3"];
    SW["10GbE Switch"];
  end

  subgraph VMs["Core VMs"]
    TRUENAS["TrueNAS VM (ZFS Pools, NFS/SMB, iSCSI)"];
    OPN["OPNsense (Firewall, VLANs, WG)"];
  end

  %% --- Other Nodes ---
  U["Admin Laptop"];

  %% --- Edges ---
  U -->|"Mgmt VLAN (HTTPS/SSH)"| SW;
  SW --- PVE1;
  SW --- PVE2;
  SW --- PVE3;

  PVE1 --> TRUENAS;
  PVE2 --> OPN;

  TRUENAS -->|"NFS/SMB/iSCSI"| PVE1;
  TRUENAS -->|"NFS/SMB/iSCSI"| PVE2;
  TRUENAS -->|"NFS/SMB/iSCSI"| PVE3;

  OPN -->|"Inter-VLAN routing + ACLs"| SW;

