# Keycloak + Stalwart IAM Stack

A self-contained identity and mail infrastructure stack combining **Keycloak** (IAM) with **Stalwart Mail Server** and a **JMAP Webmail** client.

The project follows a two-phase approach: local validation on Docker, then bare-metal deployment on Proxmox.

---

## Architecture
```
┌─────────────────────────────────────────────────────┐
│                                                     │
│   Keycloak (IAM)  ←──OIDC──→  Stalwart (Mail)       │
│                                    ↑                │
│                               JMAP Webmail          │
│                                                     │
└─────────────────────────────────────────────────────┘
         ↑
         └── Prometheus (monitoring-stack) scrapes Stalwart
```

---

## Project Structure
```
keycloak-stalwart-stack/
├── Phase-1_Docker/                    # Phase 1 — Docker test environment
│   ├── img/                           # Screenshots
│   └── Docker.md                      # Full Docker setup guide
├── Phase-2_Proxmox/                   # Phase 2 — Bare-metal production
│   ├── img/                           # Screenshots
│   └── Proxmox.md                     # Full Proxmox deployment guide
├── docker-compose.yml                 # Docker stack definition
└── README.md
```

---

## Project Phases

| Phase | Environment | Doc |
|-------|-------------|-----|
| Phase 1 — Testing | Docker (local) | [Docker.md](Phase-1_Docker/Docker.md) |
| Phase 2 — Production | Bare metal on Proxmox | [Proxmox.md](Phase-2_Proxmox/Proxmox.md) |

---

## Stack

| Service | Image | Port(s) |
|---------|-------|---------|
| Keycloak | `quay.io/keycloak/keycloak:26.0` | 8080 |
| Stalwart | `stalwartlabs/stalwart:latest` | 8081 (admin), 25, 143, 587 |
| Webmail | `rootfr/jmap-webmail:latest` | 3001 |
| PostgreSQL | `postgres:16` | 5432 (internal) |

---

## Quick Start
```bash
git clone git@github.com:Souheib-h/keycloak-stalwart-stack.git
cd keycloak-stalwart-stack
docker compose up -d
```

| Service | URL | Credentials |
|---------|-----|-------------|
| Keycloak | `http://localhost:8080` | `admin / admin` |
| Stalwart | `http://localhost:8081` | Generated on first boot |
| Webmail | `http://localhost:3001` | — |

> Stalwart credentials are printed on first boot:
> ```bash
> docker logs stalwart | grep administrator
> ```

---

## Related Projects

- [stalwart-monitoring-stack](https://github.com/Souheib-h/stalwart-monitoring-stack) — Prometheus + Grafana monitoring for this stack
- [Packet-Analysis-Lab](https://github.com/Souheib-h/Packet-Analysis-Lab) — SOC analyst lab

---

## Next Steps

- [ ] Keycloak realm and OIDC client configuration
- [ ] Stalwart OIDC integration with Keycloak
- [ ] Webmail OAuth2 login via Keycloak
- [ ] TOTP / MFA enforcement
- [ ] Proxmox bare-metal deployment