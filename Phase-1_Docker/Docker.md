# Phase 1 — Docker Deployment

> This phase covers the local Docker-based test environment for the Keycloak + Stalwart + Webmail stack.
> Stalwart setup and Prometheus monitoring are covered in [stalwart-monitoring-stack](https://github.com/Souheib-h/stalwart-monitoring-stack).

---

## Architecture
```
postgres-iam ──► keycloak :8082 (host) / :8080 (internal)
stalwart     :8080 (host) / :8080 (internal)
webmail      :3001
```

---

## Port Mapping

| Service | Host Port | Internal Port | Access |
|---------|-----------|---------------|--------|
| Keycloak | 8082 | 8080 | http://localhost:8082 |
| Stalwart | 8080 | 8080 | http://localhost:8080 |
| Webmail | 3001 | 3000 | http://localhost:3001 |

---

## /etc/hosts required
```
127.0.0.1 keycloak
```

This is required so the browser can reach Keycloak using its Docker DNS name, which is used as the OIDC issuer.

---

## Deployment

### 1. Clone
```bash
git clone git@github.com:Souheib-h/keycloak-stalwart-stack.git
cd keycloak-stalwart-stack
```

### 2. Add to /etc/hosts
```bash
echo "127.0.0.1 keycloak" | sudo tee -a /etc/hosts
```

### 3. Start the stack
```bash
docker compose up -d
```

### 4. Retrieve Stalwart credentials
```bash
docker logs stalwart | grep administrator
```

### 5. Services

| Service | URL | Credentials |
|---------|-----|-------------|
| Keycloak | http://localhost:8082 | admin / admin |
| Stalwart | http://localhost:8080 | Generated on first boot |
| Webmail | http://localhost:3001 | — |

---

## Keycloak Configuration

### Realm

- Realm name: `mail`
- Frontend URL: `http://keycloak:8080`

### Client: webmail

- Client ID: `webmail`
- Standard flow: enabled
- PKCE: S256
- Valid redirect URIs: `http://localhost:3001/*`
- Web origins: `http://localhost:3001`

### Test user

- Username: `testuser`
- Email: `testuser@mail.local`
- Password: `Test1234!`
- Email verified: ON

---

## Stalwart OIDC Configuration

Via Stalwart UI (http://localhost:8080) → Authentication → Directories → Add Directory:

| Field | Value |
|-------|-------|
| Directory ID | keycloak |
| Type | OpenID Connect |
| URL | http://keycloak:8080/realms/mail/protocol/openid-connect/userinfo |
| URL Type | OpenID Connect Userinfo |
| Timeout | 15 seconds |
| Email field | email |
| Username field | preferred_username |
| Name field | name |

Then: Authentication → Settings → Directory → OpenID Connect (keycloak) → Save & Reload

Also enable: HTTP → Security → Permissive CORS policy → ON

---

## Known Issue — jmap-webmail SSO

The `rootfr/jmap-webmail` image exposes `JMAP_SERVER_URL` directly to the browser. In a local Docker environment this creates a conflict:

- The browser needs to reach Stalwart via `localhost:8080`
- The container needs to reach Keycloak via `keycloak:8080` (Docker DNS)
- There is no separate `JMAP_SERVER_URL_INTERNAL` variable

The SSO button appears and Keycloak authentication succeeds, but the webmail fails to complete the JMAP session because `localhost:8080` is not resolvable from inside the container.

**Workaround attempted:** `host.docker.internal` + `extra_hosts` — discovery works from container but webmail still fails to initialize the SSO flow correctly.

**Resolution:** Switching to Roundcube for the webmail client, which handles IMAP/SMTP separately from the OIDC flow and does not have this architectural conflict.

---

## Next Steps

- [ ] Replace jmap-webmail with Roundcube
- [ ] Configure Roundcube OIDC plugin with Keycloak
- [ ] Test end-to-end SSO login flow
- [ ] Enable TOTP enforcement in Keycloak
