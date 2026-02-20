# Research Notes

## SSO Providers (for Project Beta)

Researched by Dana Kim — 2024-02-16

### Options Evaluated

| Provider | Pricing | SAML | OIDC | Notes |
|----------|---------|------|------|-------|
| Auth0 | Usage-based | Yes | Yes | Strong docs, free tier available |
| Okta | Per-user/month | Yes | Yes | Enterprise-focused, higher cost |
| Keycloak | Open source | Yes | Yes | Self-hosted, requires ops overhead |

### Recommendation

Auth0 for initial implementation. Competitive pricing at our scale, minimal ops burden, and good developer tooling. Revisit Okta if we grow beyond 5,000 monthly active users.

---

## Cloud Infrastructure (for Project Beta)

Researched by Morgan Lee — 2024-02-28

### Vendors Shortlisted

1. **Vendor A** — Strong EU data residency options; higher cost
2. **Vendor B** — Best price-performance; good GDPR tooling
3. **Vendor C** — Existing relationship; support quality inconsistent

### Decision

Vendor B selected (approved 2024-03-10). Contract sent 2024-03-15.

---

## ETL Tool Comparison (historical — Project Gamma)

| Tool | Pros | Cons |
|------|------|------|
| Apache Airflow | Mature, flexible | Complex setup |
| Prefect | Modern UI, easy local dev | Smaller community |
| dbt | SQL-native, great for transforms | Not a full pipeline tool |

Decision at the time: Airflow for orchestration + dbt for transformations.
