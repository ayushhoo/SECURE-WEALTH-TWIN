# SecureWealth Twin - Deployment Checklist (Vercel + Railway)

## 1) Backend (Railway)
- Provision `PostgreSQL` and `Redis`.
- Set backend env vars:
  - `DATABASE_URL=postgresql+asyncpg://...`
  - `SYNC_DATABASE_URL=postgresql://...`
  - `REDIS_URL=redis://...`
  - `SECRET_KEY=<strong-random>`
  - `JWT_SECRET_KEY=<strong-random>`
  - `ALLOWED_ORIGINS=https://<your-vercel-domain>`
- Deploy `securewealthtwin-backend`.
- Run migrations:
  - `alembic upgrade head`
- Seed demo data:
  - `python -m scripts.seed_data`
- Health check:
  - `GET /health` returns `healthy`.

## 2) Frontend (Vercel)
- Import/deploy `securewealth-twin` project.
- Set env var:
  - `NEXT_PUBLIC_API_URL=https://<railway-backend-domain>/api/v1`
- Build and deploy.
- Smoke check:
  - `/` marketing site loads.
  - `/product` product workspace loads.

## 3) Security and Compliance Checks
- Confirm consent gating:
  - Without consent, analytics endpoints return `403`.
- Confirm fraud cooling-off:
  - `warn_cooling_off` scenario triggers cooldown.
- Confirm no sensitive data leaks:
  - Only mock/demo data in responses and UI.
- Confirm advisory language:
  - No guaranteed-return claims in recommendation responses.
- Confirm KYC messaging appears on investment/tax recommendations.

## 4) Demo Scenario Validation
- Run low-risk scenario (`/fraud/scenario/low`) -> decision `allow`.
- Run medium-risk scenario (`/fraud/scenario/medium`) -> decision `warn_cooling_off`.
- Run high-risk scenario (`/fraud/scenario/high`) -> decision `block`.
- Verify audit trail captures each scenario in `/audit/events`.

## 5) Data and Observability
- Verify seeded entities exist:
  - accounts, transactions, goals, investments, assets, consent, aggregator links.
- Verify aggregator summary:
  - `/aggregator/summary` returns consolidated cross-bank snapshot.
- Verify explainability:
  - `/wealth/explainability` includes reasons/assumptions/disclaimers.
- Verify admin inspector:
  - `/product` shows fraud logs + audit events JSON panels.
