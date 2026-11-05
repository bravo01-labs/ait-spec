# Changelog — Sovereignty Verification Protocol (SVP)

All notable changes to the SVP Specification are documented here.

Format: [Keep a Changelog](https://keepachangelog.com/en/1.0.0/)
Versioning: [Semantic Versioning](https://semver.org/spec/v2.0.0.html)

---

## [1.0.0-draft.2] — 2026-10-27

Repository moved to this organization. The previous repository is archived.

### Changed
- Specification is now draft-bravo-svp-ait-spec-02.
- README and CONTRIBUTING clarified: this repository is the open specification only; compliance language softened to "designed to support"; maturity and implementation-status claims removed; a reference to a rescinded executive order removed.

---

## [1.0.0-draft] — 2026-08-05

### Initial public release

First publication of the Sovereignty Verification Protocol (SVP)
Agent Identity Token (AIT) Specification as an open standard.

Published simultaneously as:
- GitHub: `github.com/sovereignty-verification-protocol/ait-spec`
- IETF Internet-Draft: `draft-bravo-svp-ait-spec-00`

### Core specification

**Framework**
- Defined Sovereignty Verification Protocol (SVP) as the framework name
- Defined AIT (Agent Identity Token) as the cryptographic primitive
- Defined AIR (Agent Identity Registry) as the issuing authority
- Defined VAZ (Verified Agent Zone) and GAZ (General Access Zone) routing domains
- Established declaration-over-detection as the core security paradigm

**AIT Token Schema**
- Defined AIT as JWT profile per RFC 7519 with `typ` claim value `AIT`
- Required JOSE header fields: `alg`, `kid`, `typ`
- Required payload fields: `iss`, `org`, `agent_id`, `agent_type`, `assurance`, `scope`, `iat`, `exp`, `jti`
- HTTP transmission header: `X-Agent-Token`
- JSON Schema published: `schema/ait-token.json`

**AIT Assurance Levels**
- AIT-0: Self-Issued (IAL1 self-asserted)
- AIT-1: Registry Verified (IAL1 verified)
- AIT-2: Organization Verified (IAL2 remote)
- AIT-3: Government Validated (IAL2 in-person equivalent — PIV/CAC/Login.gov)
- AIT-4: High Assurance (IAL3 — IL4/IL5 environments)

**Cryptographic Requirements**
- ES256: Recommended for all implementations
- ES384: Required for AIT-3 and AIT-4
- RS256: Required minimum for production
- RS512: Permitted
- HS256: Prohibited in production
- `none`: Prohibited always

**Key Infrastructure**
- JWKS endpoint: `/.well-known/jwks.json` per RFC 7517
- Key rotation: 24-hour advance publication requirement
- Key length minimums defined per assurance level

**SVP Discovery Endpoint**
- `/.well-known/svp-identity` per RFC 8615
- Full discovery schema: `schema/svp-discovery.json`
- IANA well-known URI registration requested: `svp-identity`

**Nine-Step Validation Pipeline**
- Steps 1–9 defined with explicit pass conditions and failure actions
- All nine steps required for VAZ routing
- GAZ routing on any failure — not a block for human traffic

**Revocation**
- Signed Revocation List (SRL) model (analogous to OCSP stapling)
- Default TTL: 5 minutes. Maximum: 15 minutes
- Emergency push-invalidation for AIT-3/AIT-4 compromise
- Propagation target: 60 seconds to all edge nodes
- Endpoints: `POST /v1/revoke/{jti}`, `POST /v1/revoke/agent/{agent_id}`, `GET /v1/revocation-list`

**Audit Record Schema**
- Required fields: `record_id`, `agent_id`, `org`, `scope_used`, `resource`, `timestamp`, `outcome`, `edge_node`, `token_jti`
- CMMC 2.0 AU.3.045 and AU.3.046 alignment documented
- Export formats: JSON, CEF, LEEF

**IANA Considerations**
- JWT Claims registration requested: `org`, `agent_id`, `agent_type`, `assurance`
- Well-Known URI registration requested: `svp-identity`

### Standards alignment
- RFC 7519 (JWT), RFC 7515 (JWS), RFC 7517 (JWK/JWKS)
- RFC 7518 (JWA), RFC 8615 (Well-Known URIs)
- RFC 8693 (OAuth 2.0 Token Exchange — scope semantics)
- RFC 4122 (UUID — jti field format)
- NIST SP 800-63-3 (IAL assurance level alignment)

### Schemas
- `schema/ait-token.json` — JSON Schema for AIT payload validation
- `schema/svp-discovery.json` — JSON Schema for SVP discovery document

---

## Upcoming

### [1.0.0] — Target Q4 2026

- IANA JWT Claims registration confirmed
- IETF working group assignment (if applicable)
- Community review period complete
- Stable release — `-draft` designation removed

---

*Sovereignty Verification Protocol is developed by Bravo 01 Labs & Dynamics, United States.*
*SVP specification: CC0 1.0 Universal. Implementation infrastructure: proprietary.*
