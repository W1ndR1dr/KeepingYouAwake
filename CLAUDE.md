# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

KeepingYouAwake is a macOS menu bar utility that prevents sleep mode by wrapping Apple's `caffeinate` command. Written in Objective-C with Swift Package Manager for modular code organization.

- **Minimum macOS:** 10.13
- **Language:** Objective-C
- **Build System:** Xcode workspace with SPM local packages

## Build Commands

```bash
# Run tests
make test

# Run tests with sanitizers
make test-with-sanitizers

# Clean build artifacts
make clean

# Create distribution package
make dist

# Localization management
make l10n-export    # Export strings for translation
make l10n-import    # Import translated XLIFF files
make l10n-update    # Full update cycle
```

Direct xcodebuild commands:
```bash
xcodebuild test -workspace KeepingYouAwake.xcworkspace -scheme "KeepingYouAwake"
xcodebuild build -workspace KeepingYouAwake.xcworkspace -scheme "KeepingYouAwake"
```

## Architecture

### Package Structure (Packages/)

The app is modularized into local Swift packages (all Objective-C):

- **KYASleepWakeTimer** - Core functionality: controls the `caffeinate` process via NSTask with optional fire dates
- **KYADeviceInfo** - Battery monitoring (KYABatteryMonitor), Low Power Mode detection (KYALowPowerModeMonitor), KYADevice singleton
- **KYAApplicationSupport** - NSUserDefaults extensions, launch-at-login, bundle version utilities
- **KYAStatusItemUI** - Menu bar status item UI and image provider
- **KYAUserNotifications** - User notification center management (macOS 11+)
- **KYAApplicationEvents** - Application lifecycle and workspace session events
- **KYAActivationDurations** - Preset activation durations management
- **KYACommon** - Logging (os_log), type aliases (Auto, AutoWeak, AutoVar)

### Main App (KeepingYouAwake/)

- **KYAAppController** - Central orchestrator integrating all packages
- **KYAAppDelegate** - App lifecycle and window management
- **Settings/** - Settings window with multiple view controllers (General, Advanced, Battery, Duration, Update, About)
- **KYAMainMenu** - Menu bar menu creation

### Helper App

- **KYALauncher** - Separate bundled app for launch-at-login functionality

## Code Style

Uses `.clang-format` with WebKit base style:
- Brace style: Allman
- Indent: 4 spaces
- Pointer alignment: Right
- No column limit

Naming conventions:
- All classes/packages use `KYA` prefix
- Categories named with context (e.g., `NSApplication+KYALaunchAtLogin`)
- Delegate properties are weak

## Testing

Tests exist in packages: KYAActivationDurations, KYAApplicationEvents, KYAApplicationSupport

Test plans:
- `DefaultTestPlan.xctestplan` - Standard test execution
- `SanitizerTestPlan.xctestplan` - With memory sanitizers

## URL Scheme

The app supports automation via URL scheme:
```
keepingyouawake:///activate
keepingyouawake:///activate?seconds=30
keepingyouawake:///activate?minutes=5
keepingyouawake:///activate?hours=2
keepingyouawake:///deactivate
keepingyouawake:///toggle
```

## External Dependencies

- **Sparkle v2.6.4** - Update framework (optional, feature-gated with `KYA_APP_UPDATER_ENABLED`)
