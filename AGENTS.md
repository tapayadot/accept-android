# AGENTS.md — accept-android

## Repository overview

This is the **public-facing repository** for the Tapaya Accept Android SDK (`com.tapaya:accept`).
It contains:

- `README.md` — integration guide (installation, usage examples, error reference)
- `docs/` — generated KDoc API reference (HTML), deployed to GitHub Pages
- `LICENSE` / `NOTICE` — Apache 2.0

There is **no SDK source code** in this repository. The SDK is distributed as a compiled artifact from `https://maven.tapaya.com/releases`.

## What agents can help with

- Editing the integration guide in `README.md` (code samples, usage patterns, error tables)
- Updating or regenerating content in `docs/` (KDoc HTML output)
- Keeping `LICENSE`, `NOTICE`, and badge URLs accurate
- Reviewing Kotlin snippets in the README for correctness against the public API

## Key facts

| Item | Value |
|---|---|
| Artifact | `com.tapaya:accept` |
| Maven repo | `https://maven.tapaya.com/releases` |
| Min SDK | 30 (Android 11) |
| Target SDK | 36 |
| Language | Kotlin / JVM 11 |
| Full docs | https://docs.tapaya.com |

## Main public API surface (from README)

- `AcceptSDK` — entry point: `initialize`, `authenticate`, `logOut`, `status`, `isPaymentPending`, `getPaymentStatus`
- `AcceptSDK.helper` — activity lifecycle & runtime permissions
- `AcceptSDK.cardTerminal` — tap-to-pay card payments (`CardChargeIntent`)
- `AcceptSDK.sepaTerminal` — SEPA Instant Credit Transfer (`SepaInstantPaymentIntent`)
- `AcceptSDK.certisTerminal` — Czech Instant Transfer / Certis (`CertisInstantPaymentIntent`)
- `AcceptSDK.identity` — KYB onboarding (`loadStatus`, `presentKyb`)
- `AcceptEnvironment` — `SANDBOX` | `PRODUCTION`

## Conventions

- Code samples in the README are Kotlin.
- Keep examples minimal and copy-paste-ready.
- Error handling examples must use `AcceptSDKException` subclasses (see error table in README).
- Do not invent API surface that is not documented here or in `docs/`.

## Out of scope for this repo

- SDK internals, networking, cryptography — not present here.
- Publishing new SDK versions — handled by a separate internal pipeline.
- Android Manifest or Gradle plugin changes beyond what is shown in the README.
