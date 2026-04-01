# Keycloak + Stalwart IAM Stack

A self-contained identity and mail infrastructure stack combining **Keycloak** (IAM) with **Stalwart Mail Server** and a **JMAP Webmail** client.

---

## Architecture
```
┌──────────────────────────────────────────────┐
│                                              │
│   Keycloak (IAM) ←──OIDC──→ Stalwart (Mail) │
│                                   ↑          │
│                             JMAP Webmail     │
│                                              │
└──────────────────────────────────────────────┘
         ↑
         └── Prometheus (monitoring-stack) scrapes Stalwart
```

---

## Stack

| Service | Image | Port(s) |
|---------|-------|---------|
| Keycloak | `quay.io/keycloak/keycloak:26.0` | 8080 |
| Stalwart | `stalwartlabs/stalwart:latest` | 8081 (admin), 25, 143, 587 |
| Webmail | `rootfr/jmap-webmail:latest` | 3001 |
| PostgreSQL | `postgres:16` | 5432 (internal) |

---

## Prerequisites

- Docker `>= 29.x` and Docker Compose `>= 5.x`
- [stalwart-monitoring-stack](https://github.com/Souheib-h/stalwart-monitoring-stack) — Stalwart is part of this stack and is monitored by Prometheus

---

## Project Phases

| Phase | Environment | Doc |
|-------|-------------|-----|
| Phase 1 — Testing | Docker (local) | [Docker.md](Phase-1_Docker/Docker.md) |
| Phase 2 — Production | Bare metal on Proxmox | [Proxmox.md](Phase-2_Proxmox/Proxmox.md) |

---

## Related Projects

| Project | Description |
|---------|-------------|
| [stalwart-monitoring-stack](https://github.com/Souheib-h/stalwart-monitoring-stack) | Prometheus + Grafana monitoring |
| [Packet-Analysis-Lab](https://github.com/Souheib-h/Packet-Analysis-Lab) | SOC analyst lab |

---

## Next Steps

- [ ] Keycloak realm and OIDC client configuration
- [ ] Stalwart OIDC integration with Keycloak
- [ ] Webmail OAuth2 login via Keycloak
- [ ] TOTP / MFA enforcement
- [ ] Proxmox bare-metal deployment