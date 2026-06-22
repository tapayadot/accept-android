# Changelog

All notable changes to the Accept SDK for Android are documented here.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added

### Changed

### Fixed

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

[Unreleased]: https://github.com/tapayadot/accept-android/compare/1.3.0...HEAD
[1.3.0]: https://github.com/tapayadot/accept-android/compare/1.2.4...1.3.0
