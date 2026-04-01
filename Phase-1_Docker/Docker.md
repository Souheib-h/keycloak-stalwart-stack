# Phase 1 — Docker Deployment

> This phase covers the local Docker-based test environment for the Keycloak + Stalwart + Webmail stack.
> Stalwart bare-metal setup and Prometheus monitoring are covered in [stalwart-monitoring-stack](https://github.com/Souheib-h/stalwart-monitoring-stack).

---

## Prerequisites

- Docker `>= 29.x` and Docker Compose `>= 5.x`
- Ports available: `8080`, `8081`, `3001`, `25`, `143`, `587`

---

## Stack Overview
```
postgres-iam ──► keycloak :8080
stalwart     :8081 (admin) / :25 / :143 / :587
webmail      :3001
```

---

## Deployment

### 1. Clone the repository
```bash
git clone git@github.com:Souheib-h/keycloak-stalwart-stack.git
cd keycloak-stalwart-stack
```

### 2. Start the stack
```bash
docker compose up -d
```

### 3. Retrieve Stalwart credentials
```bash
docker logs stalwart | grep administrator
```

### 4. Access the services

| Service | URL | Credentials |
|---------|-----|-------------|
| Keycloak | `http://localhost:8080` | `admin / admin` |
| Stalwart | `http://localhost:8081` | Generated on first boot |
| Webmail | `http://localhost:3001` | — |

---

## Keycloak Configuration

> Documentation in progress — being filled as the setup is completed.

---

## Stalwart OIDC Integration

> Documentation in progress — being filled as the setup is completed.

---

## Webmail OAuth2 Integration

> Documentation in progress — being filled as the setup is completed.

---

## Next Steps

- [ ] Create Keycloak realm `mail`
- [ ] Configure OIDC client for Stalwart
- [ ] Configure OAuth2 client for Webmail
- [ ] Enable TOTP enforcement in Keycloak
- [ ] Test end-to-end login flow