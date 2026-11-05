# Contributing to the SVP Specification

Thank you for your interest in contributing to the Sovereignty Verification Protocol (SVP). This document explains what belongs here, how to contribute, and how the review process works.

---

## What this repository is

This repository contains the **open specification** for the SVP framework and AIT token: the schema definitions, routing model, and audit record format.

This is a standards document first. Contributions that belong here are specification improvements and documentation. Implementation code is not accepted.

---

## What does NOT belong here

Everything about Bravo01 Labs & Dynamics' implementation (production code, internal architecture, operational infrastructure, hosted services, detection or scoring components) is proprietary and will not be accepted as a contribution.

These are outside scope. If you are implementing these independently, the specification in this repository is your normative reference.

---

## Contributions welcome

### Specification improvements
Schema clarifications, field definition corrections, normative language improvements. Open an Issue first describing what is unclear or incorrect and what you propose. All spec changes require an Issue before a PR.

### Documentation
README improvements, new use case guides (federal deployment, publisher crawler differentiation, enterprise integration), and FAQ additions are welcome without a prior Issue.

---

## How to contribute

**1. Fork** this repository.

**2. Create a branch:**
```bash
git checkout -b fix/scope-field-description
git checkout -b feat/clarify-claim-semantics
git checkout -b docs/federal-deployment-guide
```

**3. Validate JSON schemas before submitting:**
```bash
# Install ajv-cli
npm install -g ajv-cli

# Validate token schema
ajv validate -s schema/ait-token.json -d examples/ait-example-ait2.json

# Validate discovery schema
ajv validate -s schema/svp-discovery.json -d examples/discovery-example.json
```

**4. Open a Pull Request** against `main`. Your PR description should explain what changed, why, whether it is a breaking schema change, and how you tested it.

---

## Schema versioning

- **Patch (1.0.x):** Documentation, examples, non-normative clarifications. No Issue required.
- **Minor (1.x.0):** New optional fields, additional examples. Requires Issue discussion.
- **Major (x.0.0):** Breaking changes to required fields, routing model changes, assurance level redefinition. Requires 30-day community discussion period minimum.

Breaking changes will not be merged before `1.0.0` stable.

---

## Contributor License Agreement

By submitting a contribution to this repository you agree that your contribution is licensed under CC0 1.0 Universal, the same license that covers the rest of this repository. This is a lightweight CLA sufficient for a CC0 project.

---

## Questions

Open an Issue with the `question` label. For security-sensitive matters email albert@bravo-01-labs.com directly with subject line: `SVP Spec — Security`.

---

## Code of conduct

This project follows the [Contributor Covenant](https://www.contributor-covenant.org/version/2/1/code_of_conduct/). Be respectful and technically focused.
