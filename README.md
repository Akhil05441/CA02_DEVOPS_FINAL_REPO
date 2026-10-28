# CA-2: Docker & Container Security (FestPass)

| Item | Value |
|---|---|
| Name | Akhil |
| REGNO | 202401100200028 |
| PORT | 8028 |
| Docker Hub username | YOUR_DH_USER |
| Docker Hub repository | https://hub.docker.com/r/YOUR_DH_USER/fest-backend |
| GitHub repository | https://github.com/Akhil05441/CA02_DEVOPS_FINAL_REPO |


## Stage 1: Containerise the backend
- `backend/Dockerfile` starts from `python:3.9`, installs `requirements.txt` before copying `app.py`, and starts the app with `CMD`.
- Built `fest-backend:0.1` and ran it as container `backend` on port 8028.
- Evidence: Check SS folder

## Stage 2: Compose the three-tier stack
- `compose.yaml` runs `db` (postgres:17-alpine, named volume `pgdata`, `pg_isready` healthcheck), `backend` and `frontend` (nginx:1.30-alpine).
- Two networks: `frontend_net` (frontend and backend) and `backend_net` (backend and db), so the frontend cannot reach the database.
- The database password is in `.env` (not committed); `.env.example` is committed.
- Data survives `docker compose down` and `up`.
- Evidence: Check SS folder

## Stage 3: Scan and fix with Trivy
- Scanned `fest-backend:0.1` and saved `reports/trivy-before.txt`.
- Fixed the image: `python:3.13-slim` base, non-root user `appuser`, upgraded Flask, Werkzeug and psycopg2-binary. Built `fest-backend:0.2` and saved `reports/trivy-after.txt`.
- `trivy image --severity CRITICAL --ignore-unfixed --exit-code 1 fest-backend:0.2` exits with code 0.

| Image | CRITICAL | HIGH | Size |
|---|---|---|---|
| 0.1 (python:3.9) | ? | ? | 1.61GB |
| 0.2 (python:3.13-slim) | ? | ? | ? |


## Stage 4: Version and publish to Docker Hub
- Built with `APP_VERSION=1.0.0` and pushed `YOUR_DH_USER/fest-backend:1.0.0` and `:1.0` to a public repository.
- Digest of 1.0.0 as pushed to Docker Hub:

```
YOUR_DIGEST
```

- `compose.yaml` uses `YOUR_DH_USER/fest-backend:1.0.0`. After deleting the local images, the page showed backend 1.0.0 and my registrations.
- Evidence: Check SS folder.

## Stage 5: Sign and verify with cosign
- Generated a password-protected key pair. Only `cosign.pub` is committed; `*.key` is in `.gitignore`.
- Signed `1.0.0` by digest and verified it with `cosign.pub`.
- Pushed `1.0.1` without signing it; `cosign verify` rejected it.
- Evidence: Check SS folder.

## Steps not completed
- Stage 5(partially)