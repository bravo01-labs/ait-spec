# Sovereignty Verification Protocol (SVP)
### Agent Identity and Routing Framework for Autonomous Systems
### An open protocol for declaring, verifying, authorizing, and auditing autonomous AI agents at the network edge.

**Version:** 1.0.0-draft  
**Status:** Open for community review  
**License:** CC0 1.0 Universal (SVP schema and routing spec — see [LICENSE](LICENSE))  
**IETF Internet-Draft:** draft-bravo-svp-ait-spec-02 (draft, not an adopted standard)  
**Maintained by: Bravo 01 Labs & Dynamics Inc. — United States  
**Contact:** contact@bravo-01-labs.com  

---

> **The Agent Identity Token (AIT) is a profile of JWT optimized for autonomous agent identity and routing.**
> Rather than inventing new cryptographic primitives, SVP specifies how existing proven standards —
> JWT (RFC 7519), JWS (RFC 7515), and JWKS (RFC 7517) — are applied in the context of
> autonomous agent identity at the network perimeter.

---

## The problem

Autonomous AI agents are becoming network participants, but existing
internet infrastructure generally identifies requests through human
accounts, API credentials, sessions, IP addresses, or bot-detection
signals.

These mechanisms do not provide a standardized way for an autonomous
agent to declare:

- who operates it
- what type of agent it is
- what assurance level its identity has
- what resources it is authorized to access
- whether its identity has been revoked

SVP provides an open protocol for that declaration and verification.

## How SVP Works

SVP connects four components:

1. Agent Owner
   The organization operating an autonomous agent.

2. Agent Identity Registry (AIR)
   The registry where organizations and agents are enrolled and
   where AIT credentials are issued and managed.

3. SVP Gateway
   The verification layer deployed by a website, API, platform,
   or other resource owner at its network or application edge.

4. Resource Owner
   The organization controlling the website, API, data, or service
   being accessed.


### The basic flow

Agent Owner
     │
     │ registers agent
     ▼
    AIR
     │
     │ issues AIT
     ▼
   Agent
     │
     │ presents AIT
     ▼
SVP Gateway
     │
     ├── Verify identity
     ├── Check signature
     ├── Check expiration
     ├── Check revocation
     ├── Check assurance
     └── Check authorization scope
            │
       ┌────┴────┐
       ▼         ▼
      VAZ       GAZ
    Governed   General
     Access    Access

## The model

```
Traffic Arrives at Network Edge
            ↓
X-Agent-Token header present and valid?
        /              \
      YES               NO
       ↓                 ↓
  Verified          General Access Zone
  Agent Zone        (risk assessment —
  (governed,         not a block.
   audited,          Human users served
   scoped)           normally.)
```

The burden shifts from detection to declaration. Declare your agent, get a governed path. Undeclared traffic — including all human users — is not blocked; it receives implementation-specific risk assessment in the GAZ.

---

## Who Does What?

### Agent Owners

Organizations register the autonomous agents they operate.

They define the agent's declared type and requested scopes and obtain
an Agent Identity Token (AIT).

### Resource Owners

Websites, APIs, platforms, and infrastructure operators deploy an
SVP Gateway.

They define the policies governing which verified agents and scopes
their resources will accept.

### SVP Gateways

The gateway evaluates inbound requests before they reach the protected
application or resource.

It verifies the AIT and applies the resource owner's policy.

### AIR Operators

An Agent Identity Registry maintains enrolled organizations, agent
identities, signing keys, and revocation information.

An AIR may be operated by Bravo 01 Labs, an enterprise, a government
organization, or another trusted SVP ecosystem participant.

## Agent Identity Token (AIT)

The AIT is the identity credential presented by an autonomous agent.

AIT is a profile of JSON Web Token (JWT), using standard JWT/JWS
mechanisms for signing and verification.

An AIT declares:

- Organization
- Agent identity
- Agent type
- Assurance level
- Authorized scopes
- Issuance time
- Expiration time
- Token identifier

## Agent Registration

An organization does not register an agent with every website it
intends to visit.

The organization registers the agent with an AIR.

The resulting AIT establishes the agent's declared identity and
authorization claims.

The resource owner remains responsible for deciding whether that
identity and those scopes are accepted by its own infrastructure.

Organization
     │
     ▼
Register Agent
     │
     ▼
AIR
     │
     ▼
AIT
     │
     ▼
Agent presents AIT

## Deploying SVP

A resource owner integrates SVP at the network or application edge.

SVP does not require the protected application to become an AI
application or to understand the internal implementation of an agent.

The SVP Gateway evaluates the request before forwarding it to the
existing application.

Typical deployment:

Internet
   │
   ▼
SVP Gateway
   │
   ├── SVP verification
   ├── policy enforcement
   └── audit
   │
   ▼
Existing Website / API / Platform

### Deployment models

SVP can be deployed as:

- Cloud edge worker
- Reverse proxy
- API gateway middleware
- Service-mesh component
- Enterprise gateway
- Private infrastructure
- Federated/private AIR deployment

### Example: protecting an existing API

An existing API does not need to be rewritten.

The resource owner places an SVP Gateway in front of the existing
endpoint:

Internet
   │
   ▼
SVP Gateway
   │
   ├── No AIT → GAZ policy
   │
   ├── Valid AIT → verify → enforce scope → VAZ
   │
   └── Invalid AIT → GAZ / reject according to policy
   │
   ▼
Existing API

The gateway can be deployed as an edge function, reverse proxy,
API-gateway middleware, service-mesh component, or private gateway.

The protected application receives the request only after SVP policy
has been evaluated.

## Verified Agent Zone and General Access Zone

SVP does not require unknown traffic to be automatically blocked.

### Verified Agent Zone (VAZ)

Requests presenting a valid, non-revoked AIT and satisfying the
resource owner's policy enter the Verified Agent Zone.

The request may then receive governed access and produces an audit
record.

### General Access Zone (GAZ)

Requests without a valid SVP identity, including human users,
legacy software, and undeclared automation, remain available to the
resource owner's existing security controls.

GAZ is not synonymous with denial.

It is a classification and policy boundary.

## Verification Pipeline

When an SVP-enabled gateway receives a request containing an AIT,
the gateway performs the following checks:

## Core components

| Component | Name | Description |
|---|---|---|
| **SVP** | Sovereignty Verification Protocol | The framework. Defines AIT, AIR, VAZ, GAZ, and the routing model. |
| **AIT** | Agent Identity Token | JWT-profile cryptographic credential. Transmitted as `X-Agent-Token`. |
| **AIR** | Agent Identity Registry | Issues, validates, and revokes AIT tokens. Interface is open; implementation is proprietary. |
| **VAZ** | Verified Agent Zone | Routing domain for declared agents with valid credentials. |
| **GAZ** | General Access Zone | Routing domain for all undeclared traffic including human users. |

---

## Token structure

The AIT is a JSON Web Token per [RFC 7519](https://datatracker.ietf.org/doc/html/rfc7519), signed per [RFC 7515](https://datatracker.ietf.org/doc/html/rfc7515), with JWT `typ` of `AIT`.

### Header

```json
{
  "alg": "ES256",
  "kid": "svp-key-2026-06",
  "typ": "AIT"
}
```

### Payload

```json
{
  "iss": "https://air.example.com",
  "org": "acme-corp",
  "agent_id": "research-agent-001",
  "agent_type": "research",
  "assurance": "AIT-2",
  "scope": ["read:documents", "query:public-api"],
  "iat": 1789530000,
  "exp": 1789540000,
  "jti": "550e8400-e29b-41d4-a716-446655440000"
}
```

### HTTP transmission

```
X-Agent-Token: <base64url-header>.<base64url-payload>.<base64url-signature>
```

### Field reference

| Field | RFC | Description | Required |
|---|---|---|---|
| `alg` | RFC 7518 | Signing algorithm. `ES256` recommended. `HS256` prohibited in production. | Yes |
| `kid` | RFC 7517 | Key ID for JWKS resolution at `/.well-known/jwks.json`. | Yes |
| `typ` | RFC 7519 | Must be literal string `AIT`. | Yes |
| `iss` | RFC 7519 | Issuer. URI of the issuing AIR. | Yes |
| `org` | SVP Ext. | Registered organization identifier. Must resolve in AIR. | Yes |
| `agent_id` | SVP Ext. | Unique agent instance identifier within org namespace. | Yes |
| `agent_type` | SVP Ext. | Declared agent class (e.g. `research`, `procurement`, `search-indexer`, `training-crawler`). | Yes |
| `assurance` | SVP Ext. | Assurance level: `AIT-0` through `AIT-4`. | Yes |
| `scope` | RFC 8693 | Array of declared access scopes. Format: `action:resource`. | Yes |
| `iat` | RFC 7519 | Issued-at (Unix epoch). | Yes |
| `exp` | RFC 7519 | Expiration (Unix epoch). | Yes |
| `jti` | RFC 7519 | JWT ID. UUID v4. Used for revocation matching. | Yes |

---

## Assurance levels

| Level | Designation | Verification | NIST SP 800-63 | Use case |
|---|---|---|---|---|
| `AIT-0` | Self-Issued | Self-signed, no external verification | IAL1 (self-asserted) | Dev, testing, open APIs |
| `AIT-1` | Registry Verified | Enrolled in AIR, signing key registered | IAL1 (verified) | Commercial APIs, low-risk automation |
| `AIT-2` | Organization Verified | Legal entity verification against official records | IAL2 (remote) | Enterprise integrations, B2B agent access |
| `AIT-3` | Government Validated | Identity verified against PIV, CAC, or Login.gov | IAL2 (in-person eq.) | Federal contractors, defense industrial base |
| `AIT-4` | High Assurance | Multi-factor, security review, ongoing compliance attestation | IAL3 | DoD, IC, critical infrastructure, IL4/IL5 |

---

## Nine-step validation pipeline

Edge validators execute this pipeline on every inbound `X-Agent-Token` request:

```
1. Header present?          → absent: GAZ (no log)
2. Valid JWT structure?      → invalid: GAZ + log
3. Approved algorithm?       → HS256/none: GAZ + log
4. kid resolves in JWKS?     → unknown: GAZ + log
5. Signature valid?          → fail: GAZ + log + notify AIR
6. Not expired?              → expired: GAZ + log
7. jti not revoked?          → revoked: GAZ + log + alert AIR
8. Scope covers resource?    → violation: GAZ + log + notify AIR
9. org active in AIR?        → unknown: GAZ + log

All nine pass → VAZ
```

---

## Key infrastructure

### JWKS endpoint (RFC 7517)

```
GET /.well-known/jwks.json

{
  "keys": [{
    "kty": "EC", "use": "sig", "crv": "P-256",
    "kid": "svp-key-2026-06", "alg": "ES256",
    "x": "...", "y": "..."
  }]
}
```

### Discovery endpoint (RFC 8615)

```
GET /.well-known/svp-identity

{
  "svp_supported": true,
  "protocol_version": "1.0",
  "registry": "https://air.example.org",
  "jwks_uri": "https://air.example.org/.well-known/jwks.json",
  "token_endpoint": "https://air.example.org/v1/token",
  "revocation_endpoint": "https://air.example.org/v1/revoke",
  "assurance_levels_supported": ["AIT-0","AIT-1","AIT-2","AIT-3","AIT-4"],
  "algorithms_supported": ["ES256","ES384","RS256","RS512"],
  "issuer": "https://air.example.org"
}
```

### Cryptographic requirements

RS256 is the required minimum baseline. ES256 is also
supported and is recommended for deployments where elliptic-curve signing
is preferred.

Implementations MUST NOT accept `none` or HMAC-based signing for AITs.

| Algorithm            | Status                          |
| -------------------- | ------------------------------- |
| RS256 (RSA SHA-256)  | **Reference baseline**          |
| ES256 (ECDSA P-256)  | **Supported / Recommended**     |
| RS384 (RSA SHA-384)  | Permitted                       |
| RS512 (RSA SHA-512)  | Permitted                       |
| ES384 (ECDSA P-384)  | Permitted                       |
| ES512 (ECDSA P-521)  | Permitted                       |
| HS256 (HMAC SHA-256) | **Prohibited for AITs**         |
| `none`               | **Prohibited always**           |
---

## Audit record schema

Every VAZ transaction produces an immutable audit record:

```json
{
  "record_id":  "uuid-v4",
  "agent_id":   "research-agent-001",
  "org":        "acme-corp",
  "scope_used": "read:documents",
  "resource":   "/api/v1/documents/search",
  "timestamp":  "2026-08-05T14:32:01.443Z",
  "outcome":    "allowed",
  "edge_node":  "ord-01",
  "token_jti":  "550e8400-e29b-41d4-a716-446655440000"
}
```

The specification defines export as JSON, CEF and LEEF. The record is designed to support implementation of CMMC 2.0 AU.3.045 and AU.3.046; it does not by itself satisfy them.

---

## Revocation

The AIR maintains a Signed Revocation List (SRL) — analogous to OCSP stapling in TLS.

- Default TTL: 5 minutes. Maximum: 15 minutes.
- Emergency revocation for AIT-3/AIT-4 bypasses TTL with push-invalidation.
- Propagation target: all edge nodes within 60 seconds.
- SRL signed by AIR using same key exposed via JWKS.

```
POST /v1/revoke/{jti}             # Revoke by token ID
POST /v1/revoke/agent/{agent_id}  # Revoke all tokens for agent
GET  /v1/revocation-list          # Fetch current signed SRL
```

---

## Quick start

### Python

```python
import jwt, uuid
from cryptography.hazmat.primitives.asymmetric import ec
from datetime import datetime, timezone, timedelta

private_key = ec.generate_private_key(ec.SECP256R1())

payload = {
    "iss": "https://air.example.com",
    "org": "your-org",
    "agent_id": "my-agent-001",
    "agent_type": "research",
    "assurance": "AIT-1",
    "scope": ["read:documents"],
    "iat": int(datetime.now(timezone.utc).timestamp()),
    "exp": int((datetime.now(timezone.utc) + timedelta(hours=1)).timestamp()),
    "jti": str(uuid.uuid4())
}

token = jwt.encode(
    payload, private_key, algorithm="ES256",
    headers={"typ": "AIT", "kid": "your-key-id"}
)

headers = {"X-Agent-Token": token}
```

### JavaScript

```javascript
import { SignJWT, importPKCS8 } from 'jose';
import { v4 as uuidv4 } from 'uuid';

const privateKey = await importPKCS8(process.env.SVP_PRIVATE_KEY, 'ES256');

const token = await new SignJWT({
  iss: 'https://air.example.com',
  org: 'your-org',
  agent_id: 'my-agent-001',
  agent_type: 'research',
  assurance: 'AIT-1',
  scope: ['read:documents'],
  jti: uuidv4()
})
  .setProtectedHeader({ alg: 'ES256', kid: 'your-key-id', typ: 'AIT' })
  .setIssuedAt()
  .setExpirationTime('1h')
  .sign(privateKey);

fetch('https://api.example.com/data', {
  headers: { 'X-Agent-Token': token }
});
```

### Rust

```rust
use jsonwebtoken::{encode, Header, EncodingKey, Algorithm};
use uuid::Uuid;

let mut header = Header::new(Algorithm::ES256);
header.kid = Some("your-key-id".to_string());
header.typ = Some("AIT".to_string());

let token = encode(&header, &claims, &EncodingKey::from_ec_pem(pem)?)?;
// Send as: X-Agent-Token: {token}
```

---

## Regulatory alignment

SVP's proposed relationship to these frameworks. This is **not** an endorsement, certification, or compliance claim by any agency or standards body; SVP supports implementation work and does not by itself satisfy any control.

| Framework | Requirement | How SVP is designed to relate |
|---|---|---|
| DoD Zero Trust (2027) | Continuous verification of all entities | VAZ validation is designed to support continuous verification of machine entities |
| CMMC 2.0 Level 2 | AU.3.045, AU.3.046 audit traceability | The audit record (agent_id, org, scope, resource, timestamp) is designed to support implementation of these controls |
| EU AI Act | Auditability for high-risk AI | The AIR registry and audit log are designed to support an auditable agent identity record |
| NIST AI RMF | GOVERN, MEASURE, MANAGE | The AIT schema is designed to support GOVERN; the audit log to support MEASURE |

---

## Scope and status

This repository contains the **open specification only**: the protocol text, the JSON schemas, and this documentation. It contains no implementation code. Implementations of SVP are proprietary products of Bravo01 Labs & Dynamics and are not licensed, described, or included here.

The specification is a **draft for community review**. It is not an adopted standard, and no maturity level, certification, or compliance status is claimed for it or for any implementation.

---

## What's in this repository

| Path | Description |
|---|---|
| `README.md` | This file |
| `draft/draft-bravo-svp-ait-spec-02.txt` | The specification (IETF Internet-Draft format) |
| `CHANGELOG.md` | Version history |
| `CONTRIBUTING.md` | Contribution guidelines |
| `LICENSE` | CC0 1.0 Universal |
| `schema/ait-token.json` | JSON Schema for AIT payload |
| `schema/svp-discovery.json` | JSON Schema for SVP discovery endpoint |

---

## What is NOT in this repository

Everything about how Bravo01's implementation is built or operated: production code, databases, internal architecture, operational infrastructure and documentation, hosted services, and any detection or scoring components. These are proprietary and are not part of this open specification.

---

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md). The schema, routing definition, and audit record format are open for contribution. Implementation infrastructure is out of scope.

---

## License

The SVP specification, AIT schema, routing definition, and audit record schema in this repository are **CC0 1.0 Universal** — use, modify, and redistribute freely including for commercial purposes.

CC0 applies **only** to the specification text and schemas in this repository. It does not license, and this repository does not contain, any Bravo01 implementation, which is proprietary.

The Sovereignty Verification Protocol name and SVP branding are trademarks of Bravo01 Labs & Dynamics.

---

## Contact

**Albert Bravo** — Founder & CEO, Bravo01 Labs & Dynamics  
contact@bravo-01-labs.com  
United States
