# Changelog

All notable changes to the Accept SDK for Android are documented here.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added

### Changed

### Fixed

## [1.11.0] - 2026-09-05

### Added
- `NfcPositionConfig.ExternalReader` for setups where the antenna is on a separate reader that is
  not above the device — a counter reader, a side-mounted pinpad, a customer-facing dock. There is
  no point on the device to place and no direction to point at, so the companion drops its tap
  indicator entirely and shows the amount with a prompt underneath it instead. Its optional
  `message` replaces the companion's own localized prompt for setups the default wording doesn't
  describe; you own its localization, blank counts as unset, and the companion trims and clamps it.
  Companion app 1.5.2 or newer is required — older builds treat the new mode as no override at all
  and keep resolving antenna position themselves, so nothing breaks.

### Changed

### Fixed

## [1.10.0] - 2026-09-03

### Added
- `AcceptPlugin.activateTerminal()` now throws `NoTidsAvailableForMerchant` when the merchant has
  no unassigned terminal (TID) left to activate, instead of a generic `Unknown` error.
- Dual-sided ("double-sided") device support: `Accept.displays` (`all()`, `customerFacing()`,
  `isDualScreen`) reports the device's screens, and the new `PaymentDisplay` option on
  `AcceptPayments.setOptions(display = ...)` / `pay(display = ...)` runs the plugin's payment UI
  on the customer-facing panel while the host app keeps the merchant one. A target that can't be
  honored falls back to the default display, so single-screen behavior is unchanged. The terminal
  warm-up fired by `authenticate()` follows the same target. New `:dualscreen` sample app shows
  the full two-panel checkout.

### Changed
- `PayHostActivity` now declares an empty `taskAffinity`, so a payment always starts a fresh task
  instead of stacking onto the host app's. Required for the cross-display launch above; on a
  single-screen device the only visible difference is that the invisible host no longer joins the
  host app's back stack.
- The SDK's remote diagnostics now identify the device behind a log line: device id, brand, model,
  OS version, and the host app's package name, plus a one-time record of the device's display
  layout at `initialize()`. Internal diagnostics only — nothing about the merchant's customers is
  collected, and the sink stays off entirely in debug builds.

### Fixed

## [1.9.1] - 2026-08-27

### Fixed
- Location fixes now fall back to the OS's last-known location when a fresh fix times out or the
  live request reports unavailable, instead of failing outright. On Android 11 (API 30),
  `LocationManager.getCurrentLocation()` is no longer used — it's unreliable on that API level —
  in favor of a one-shot `requestLocationUpdates()` request. Also guards against duplicate/late
  location callbacks and prefers the network provider before GPS.

## [1.9.0] - 2026-08-25

### Added
- `AcceptPlugin.updateInfo()` reports whether a newer version of the plugin app is available on
  Play (`PluginUpdateInfo`: `UpToDate` / `UpdateAvailable(priority)` / `Unknown`). The plugin app
  checks itself via Play Core's in-app update API and reports the result over the existing status
  channel — the SDK never queries Play directly. Requires a plugin app build that sets the new
  `update_available`/`update_priority` status fields; older plugin builds report `Unknown`.

## [1.8.0] - 2026-08-13

### Added
- `ReceiptDetails` on `PayResult.Success`/`PayResult.Declined` exposes structured card-receipt
  data reported by the terminal — merchant ID, terminal ID, card brand, EMV application
  label/AID, masked PAN, response code, and an ISO-8601 transaction timestamp — for callers that
  print their own official card receipt instead of relying solely on
  `PaymentStatus.receiptUrl`'s hosted HTML page. Every field is optional/best-effort.

## [1.7.0] - 2026-08-10

### Added
- Optional `transactionTimeout` (`Duration`) on `AcceptPayments.pay()` and `setOptions()` controls
  how long the companion waits for a card tap. Validated to 0..120 seconds; out-of-range surfaces
  as `TransactionTimeoutOutOfRange` via `PaymentEvent.CreationFailed` (`pay()` never throws
  synchronously).

### Changed
- `AcceptPayments.refund()` is now public (previously internal and unreachable by SDK consumers),
  matching `cancel()`'s KDoc/throws style.

### Fixed
- Consumer ProGuard/R8 rules: `AcceptException` subtypes not ending in `Exception` are now kept by
  inheritance instead of name-suffix matching, so future subtypes can't fall through the cracks.
  Also keeps `AcceptLogLevel`, and scopes the Parcelable `CREATOR` keep rule to the SDK's own
  package instead of the whole app.

## [1.6.0] - 2026-08-05

### Added
- Optional `tip` (Long, minor units) on `AcceptPayments.pay()` — when set, the plugin skips its
  own interactive tip screen and charges `amount + tip`.
- `PayResult.Success.authCode` exposes the processor authorization code reported by the terminal.
  Nullable/informational; may be absent depending on the processor.
- `ReceiptPrintBehavior` on `PaymentReceiptConfig.Show` (`Automatic`, `OnDemand`, `Disabled`)
  controls when the companion prints the physical receipt. Defaults to `OnDemand`.
- `nfcPosition` payment config hint (`NfcPositionConfig`) tells the companion where the device's
  NFC antenna is, to place the tap animation correctly. Defaults to `Unspecified`.
- Optional `metadata` param on `AcceptPayments.pay()`, threaded through to the plugin and echoed
  back on all `PayResult` variants.

### Changed
- Payment extras are no longer logged in full (`extras=...`) to avoid leaking metadata values;
  only extra keys are logged now.

### Fixed
- Removed dead `AcceptService`/`AcceptConfigProvider` manifest entries.

## [1.5.0] - 2026-07-24

### Added
- `Accept.sdk.version`, `Accept.sdk.isProduction`, and `Accept.sdk.deviceId()` for diagnostics
  and support — version is available before `initialize()`; `isProduction` reflects the active
  environment; `deviceId()` is a stable, DataStore-backed identifier available after
  `authenticate()`.
- `PaymentReceiptConfig` controls the receipt screen the companion shows after an *approved*
  payment: `DismissImmediately`, or `Show(showQrCodeReceipt, dismissBehavior)` with
  `DismissBehavior.Manual` or `DismissBehavior.Delayed(duration)`. Set a default via
  `AcceptPayments.setOptions(receiptConfig)`, or override per call via `pay(..., receiptConfig)`.
  Has no effect on declined, reversed, or pending-reversal outcomes.

### Changed
- The plugin's terminal now pre-warms in the background. Pure optimization — silent no-op until the
  companion app supports it; the pay path is unaffected either way.

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

[Unreleased]: https://github.com/tapayadot/accept-android/compare/1.10.0...HEAD
[1.10.0]: https://github.com/tapayadot/accept-android/compare/1.9.1...1.10.0
[1.5.0]: https://github.com/tapayadot/accept-android/compare/1.4.3...1.5.0
[1.4.3]: https://github.com/tapayadot/accept-android/compare/1.4.2...1.4.3
[1.4.2]: https://github.com/tapayadot/accept-android/compare/1.4.1...1.4.2
[1.4.1]: https://github.com/tapayadot/accept-android/compare/1.4.0...1.4.1
[1.4.0]: https://github.com/tapayadot/accept-android/compare/1.3.2...1.4.0
[1.3.2]: https://github.com/tapayadot/accept-android/compare/1.3.1...1.3.2
[1.3.1]: https://github.com/tapayadot/accept-android/compare/1.3.0...1.3.1
[1.3.0]: https://github.com/tapayadot/accept-android/compare/1.2.4...1.3.0
