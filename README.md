# Keynova CI/CD Test Harness Microservice

[![Keynova PQC Gate](https://github.com/axant4323/test_keynova/actions/workflows/keynova_pqc_gate.yml/badge.svg)](https://github.com/axant4323/test_keynova/actions/workflows/keynova_pqc_gate.yml)
[![CycloneDX](https://img.shields.io/badge/CBOM-CycloneDX_1.6-blue)](https://cyclonedx.org)
[![NIST](https://img.shields.io/badge/NIST-FIPS_203_%7C_204-purple)](https://csrc.nist.gov/pqc)
[![Status](https://img.shields.io/badge/Security-Air--Gapped_Gate-brightgreen)](README.md)

This repository is a test harness microservice demonstrating automated **Post-Quantum Cryptographic (PQC) Readiness Policy Enforcement** using Keynova and GitHub Actions.

---

## 🌟 How the CI/CD Pipeline Works

Every time code is **pushed** or a **Pull Request** is opened, GitHub Actions executes [keynova_pqc_gate.yml](.github/workflows/keynova_pqc_gate.yml):

1. **Air-Gapped AST Discovery:** Traverses Python Abstract Syntax Trees in memory to extract cryptographic primitives, key lengths, and cipher parameters.
2. **Harvest-Now-Decrypt-Later (HNDL) Gating:** Fails immediately if legacy asymmetric algorithms (RSA-2048, ECC SECP256R1, Diffie-Hellman) are detected.
3. **Readiness Threshold:** Enforces a minimum Keynova Readiness Index of **80/100**.
4. **CycloneDX 1.6 CBOM Generation:** Automatically exports an official Cryptographic Bill of Materials JSON artifact (`cyclonedx-cbom.json`).
5. **SARIF Security Reporting:** Generates an OASIS SARIF v2.1.0 code scanning report.

---

## 🧪 How to Test Pull Request Gating

### Test 1: Triggering a Policy Violation (Blocked PR) ❌

1. Create a new test branch:
   ```bash
   git checkout -b test-vulnerable-rsa
   ```
2. Copy the sample classical cryptographic implementation into the service:
   ```bash
   cp examples/vulnerable_sample.py.txt service/legacy_crypto.py
   ```
3. Commit and push:
   ```bash
   git add service/legacy_crypto.py
   git commit -m "feat: introduce legacy RSA and ECC encryption"
   git push origin test-vulnerable-rsa
   ```
4. Open a **Pull Request** on GitHub.
5. **Result:** GitHub Actions triggers, detects the RSA-2048 and ECC HNDL threats, exits with code `1`, and **blocks the PR from merging**. You can view the full Markdown summary and download the generated CycloneDX CBOM from the Actions tab.

---

### Test 2: Passing the Gate (PQC Compliant) 🟢

1. Migrate the code to NIST standardized quantum-resistant algorithms:
   * Key Encapsulation: **FIPS 203 (ML-KEM-768)**
   * Digital Signatures: **FIPS 204 (ML-DSA-65)**
   * Symmetric Payload: **AES-256-GCM**
   * Hashing: **SHA-3-512 / Argon2id**
2. Commit and push:
   ```bash
   git rm service/legacy_crypto.py
   git commit -m "fix(security): migrate to NIST PQC standards"
   git push origin test-vulnerable-rsa
   ```
3. **Result:** The CI check turns **Green ✅**, confirming $R \ge 80$ with 0 HNDL violations, and unlocks the Pull Request merge button.

---

## 📦 Downloading the CycloneDX CBOM

1. Go to the **Actions** tab on GitHub.
2. Select any workflow run.
3. Scroll down to the **Artifacts** section at the bottom.
4. Download `keynova-cyclonedx-cbom` to inspect the machine-readable CycloneDX 1.6 Cryptographic BOM.
