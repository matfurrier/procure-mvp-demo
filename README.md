# Procure MVP — Procurement Management System

> Enterprise procurement platform built for De Sangosse Brasil, replacing the legacy BlueEz flow with native SAP B1 integration via Service Layer.

**Status:** Production (`compras.desangosse.com.br`) · v1.2.0

---

## Overview

Full procurement cycle:

```
Purchase Request (any employee)
  → Email notification to manager
  → Approval / Rejection at /approvals
  → RFQ generation + supplier invitation (email with public link)
  → Supplier quotes via portal (no login required)
  → Comparative map → Award (line-level, multi-supplier split allowed)
  → SAP B1 sync (PurchaseRequest via Service Layer)
```

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Backend | FastAPI 0.109 · Python 3.11 · SQLAlchemy 2.0 |
| Database | PostgreSQL 15 |
| Frontend | React 18 · TypeScript · ShadCN · Tailwind CSS |
| Auth | Keycloak (SSO/OIDC) |
| ERP Integration | SAP B1 Service Layer (REST) |
| Infra | Docker · Docker Compose · GitHub Actions |

---

## Architecture

See [`docs/architecture.md`](docs/architecture.md) for component diagram and data flow.

---

## Local Setup

```bash
cp .env.example .env
# Fill in your values
docker compose up -d
```

See `.env.example` for all required variables.

---

## Key Design Decisions

- **Supplier portal without authentication** — suppliers receive a unique token link per RFQ; no account creation required, reducing friction and improving quote response rates.
- **Line-level award splitting** — each RFQ line can be awarded to a different supplier, reflecting real procurement practice.
- **SAP B1 sync is async** — purchase requests are created in SAP B1 asynchronously after award, preventing UI blocking on Service Layer latency.
- **Schema-driven approval rules** — approval thresholds and escalation paths are configured in the database, not hardcoded.

---

## Security Notes

- All API endpoints protected by Keycloak JWT validation
- Supplier tokens are single-use and time-limited
- SAP B1 credentials stored in environment variables only (never in code)
- LGPD-compliant: no PII logged without anonymization
