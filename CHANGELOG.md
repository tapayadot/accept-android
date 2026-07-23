# Changelog

All notable changes to the Accept SDK for Android are documented here.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added

### Changed

### Fixed

## [1.4.3] - 2026-07-22

### Added
- `Accept.setLogLevel(AcceptLogLevel)` for finer control over diagnostic log verbosity, with the
  new `AcceptLogLevel` enum (`NONE`, `ERROR`, `WARN`, `INFO`, `DEBUG`, `VERBOSE`). Defaults to
  `DEBUG` against sandbox and `NONE` against production.
- `Accept.setLogger(AcceptLogger?)` to route consumer-facing SDK logs into a custom sink (Timber,
  Crashlytics, etc.); pass `null` to restore the default Logcat sink. Only already-redacted,
  consumer-facing messages reach the logger.

### Changed
- `Accept.setDebugLoggingEnabled(enabled)` is now shorthand for `setLogLevel`: `true` maps to
  `AcceptLogLevel.DEBUG`, `false` to `AcceptLogLevel.NONE`. Prefer `setLogLevel` for finer control.

## [1.4.2] - 2026-07-22

### Changed
- The published `-sources.jar` and API reference documentation now contain only the public API;

## [1.4.1] - 2026-07-22

### Fixed
- CI: publish the API reference documentation site on release. No functional changes to the SDK from
  1.4.0.

## [1.4.0] - 2026-07-21

### Added
- New `Accept` entry point exposing SDK lifecycle as an observable `state: StateFlow<SdkState>`.
- `AcceptAuth.authenticate(merchantToken)` for merchant authentication.
- `AcceptSdk.minimumAmounts()` for merchant-configured minimum payment amounts per currency.
- `AcceptMerchant` surface: `info()`, `config()`, `onboardingStatus()`, `availableCurrencies()`.
- `AcceptPayments.pay()` returning a `Flow<PaymentEvent>` (`Creating`, `Created`, `Launched`,
  `Result`, `CreationFailed`), plus `status()` and `cancel()`.
- `PaymentEvent.Created` — the payment token is now available as soon as the backend creates
  the payment, before the plugin is launched.
- `AcceptPlugin` surface for the Accept terminal companion app: `isInstalled()`, `install()`,
  `status()`, `activateTerminal()`, and `logout()`.
- `pay()` now fails fast with `PluginNotReadyForPayments` if the plugin isn't authenticated and
  ready, instead of surfacing a downstream plugin error.
- Typed, sealed `AcceptException` hierarchy for public failure modes (auth, plugin, location,
  network, payment) — see KDoc `@throws` on each function for the exact set.
- `Accept.clear()` to reset locally persisted SDK state on logout.
- `Accept.setDebugLoggingEnabled()` for opt-in internal debug logging.
- Location fix caching to speed up payment creation.

### Changed
- `pay()` is now `Flow`-based instead of Activity-for-result; results are correlated by request
  ID and concurrent launches are rejected with `PaymentInProgress`.
- Terminal activation is now a suspend function guarded against concurrent launches
  (`ActivationInProgress`).
- Default environment is `SANDBOX`; target production explicitly via
  `Accept.initialize(context, isProduction = true)`.

## [1.3.2] - 2026-06-26

### Changed

### Fixed
- `AcceptUnknownException` is now always constructed with a message, improving error reporting.
- Companion-app discovery now matches only `com.tapaya.accept.*` packages.
- Removed a hardcoded demo token.

## [1.3.1] - 2026-06-25

### Changed
- Updated sandbox base URL to `api.sandbox.tapaya.com`.
- Example app rewritten to match the iOS example app flow: initialize → login (with auto-register on
  401/404) → authenticate state machine, KYB, onboarding status, card status, and companion
  install/pair.

## [1.3.0] - 2026-06-22

### Added
- Optional `com.tapaya:accept-stripe` module providing embedded Stripe Tap to Pay. The core
  `com.tapaya:accept` artifact no longer bundles the Stripe Terminal SDK.
- Companion-app management API on `Accept`: `isCompanionAppInstalled()`, `pairWithCompanionApp()`
  and `presentCompanionAppSheet()`.

### Changed
- Reduced size of Accept SDK.
- Companion app hand-off now uses an Android Intent instead of a `com.tapaya.accept://` deep link.

### Fixed

[Unreleased]: https://github.com/tapayadot/accept-android/compare/1.4.3...HEAD
[1.4.3]: https://github.com/tapayadot/accept-android/compare/1.4.2...1.4.3
[1.4.2]: https://github.com/tapayadot/accept-android/compare/1.4.1...1.4.2
[1.4.1]: https://github.com/tapayadot/accept-android/compare/1.4.0...1.4.1
[1.4.0]: https://github.com/tapayadot/accept-android/compare/1.3.2...1.4.0
[1.3.2]: https://github.com/tapayadot/accept-android/compare/1.3.1...1.3.2
[1.3.1]: https://github.com/tapayadot/accept-android/compare/1.3.0...1.3.1
[1.3.0]: https://github.com/tapayadot/accept-android/compare/1.2.4...1.3.0
