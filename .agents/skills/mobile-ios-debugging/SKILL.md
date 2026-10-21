---
name: mobile-ios-debugging
description: >-
  Inspect and debug an iOS app on a simulator or a paired iPhone or iPad. Use
  when the user mentions Xcode, xcodebuild, the iOS Simulator, idevicesyslog,
  libimobiledevice, provisioning, code signing, an iOS crash, or Instruments.
  Read-only until the user approves a build, install, or signing change.
---

# Mobile-iOS-Debugging

## Purpose

Inspect an iOS app on the simulator or on a paired iPhone or iPad, and change the app, device, or signing only after the user approves that change.

Use this skill for `xcodebuild`, simulators, device logs, provisioning and code signing, crash triage, and a first look at performance with Instruments. Device-specific facts live in `data/mobile/<device>.md`. Read that file when the user names a device that already has one, and update it after an inspection or an approved change.

Android devices belong to [Mobile-Android-Debugging](../mobile-android-debugging/SKILL.md). Installing Xcode or libimobiledevice belongs to [software-management](../software-management/SKILL.md). This skill does not jailbreak a device, erase it, or change the Apple Developer account.

## Platforms

macOS with Xcode for builds, the simulator, and Instruments. `xcodebuild` and `xcrun simctl` exist only on macOS. Device logs can also use `idevicesyslog` from libimobiledevice when that tool is already installed.

This repository has no tools. Use the commands already on the Mac. If `xcodebuild` or a requested device tool is missing, stop and hand the install to software-management. Do not install Xcode from this skill.

## Steps

1. Confirm the app and the target: a named simulator, or a paired iPhone or iPad. Read `data/mobile/<device>.md` when it exists. Do not inspect an unstated device.
2. Check the tools the step needs. `xcodebuild -version` and `xcrun simctl list devices` are read-only. For a physical device, `xcrun xctrace list devices` or `idevice_id -l` shows whether it is paired. Stop if the device is not paired. Do not click through a trust prompt for the user.
3. Build only when the user asks. Prefer `xcodebuild -scheme <scheme> -destination <destination> build`. Name the scheme and destination before running it. A build writes DerivedData on the Mac. Installing the app on a simulator or device needs a separate approval.
4. Logs are read-only. Simulator: `xcrun simctl spawn booted log stream`. Paired device: `idevicesyslog` when libimobiledevice is installed. Stop the stream once the relevant lines are captured. Do not record the whole log into `data/`.
5. For a crash, report the exception type, the crashing thread, and the first frames that belong to the app. Leave out accounts, tokens, and user content.
6. For performance, name the Instruments template that fits: Time Profiler for CPU, Allocations for memory growth, Leaks for leaked objects. Describe what to record. Do not start a long Instruments capture unless the user asks.
7. Signing: a device install needs a development team and a provisioning profile that matches the bundle id and device. Report the team and profile name when they are already in the project. Do not create certificates, change the signing team, or download profiles unless the user approves that change.
8. Record the target, what ran, and what is still proposed in `data/mobile/<device>.md`. Do not write secrets, profiles, or tokens.

## Report

Print the [report format](../README.md#report-format). Mode is `read-only` for inspection and logs, `build` or `install` after an approved change.

Host is the Mac plus the simulator name or the device name. Do not print the device UDID in full unless the user asks.

Body is a short list: target, tool used, and the finding. Number a row only when the user can approve a build, install, or signing change.

## Safety

Follow [AGENTS.md](../../../AGENTS.md) and [Skills safety](../README.md#safety).

Log inspection is read-only. Building changes DerivedData on the Mac. Installing an app, erasing a simulator, rebooting a device, and changing code signing need explicit approval. State the effect and whether it is reversible before asking.

A development build is signed for the registered devices only. Never store certificates, private keys, provisioning profiles, or passwords in the skill or in `data/`.
