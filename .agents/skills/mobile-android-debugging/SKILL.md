---
name: mobile-android-debugging
description: >-
  Inspect an Android phone or tablet over USB debugging with adb, inventory
  apps, and disable or uninstall bloat only after approval. Use when the user
  mentions USB debugging, adb, Android debugging, an Android phone or tablet,
  or removing unused apps or bloatware from an Android device. Read-only until
  the user approves a numbered change.
---

# Mobile-Android-Debugging

## Purpose

Inspect an Android device attached to the computer the user is using, and change its apps only after the user approves the exact rows.

Use this skill for USB debugging, `adb`, device identity, package inventory, and disabling or removing unused apps and preinstalled bloat. Device-specific facts and in-progress decisions live in `data/mobile/<device>.md`. Read that file when the user names a device that already has one, and update it after an inspection or an approved change.

iOS apps and simulators belong to [Mobile-iOS-Debugging](../mobile-ios-debugging/SKILL.md). Host packages, including installing `adb`, belong to [software-management](../software-management/SKILL.md). Operating system updates on the computer belong to [system-updates](../system-updates/SKILL.md). This skill does not root a device, unlock a bootloader, or factory-reset it.

## Platforms

An Android device attached by USB to macOS, Linux, or Windows. `adb` commands are the same on each computer. Do not copy the workflow per computer operating system.

This repository has no tools. Use `adb` when it is already installed. If `adb` is missing, stop and hand off the install to software-management. Do not install it from this skill.

## Steps

1. Confirm which device the user means. Read `data/mobile/<device>.md` when it exists. Do not inspect an unstated device.
2. Check that `adb` is on the computer. Run `adb devices -l`. Continue only when the target shows `device`. `unauthorized` means the device has not accepted this computer. `offline` means the session is not usable. Stop and say which state came back.
3. Read identity with `getprop`: `ro.product.model`, `ro.product.device`, `ro.build.version.release`, `ro.build.version.sdk`, `ro.build.version.security_patch`, and `ro.build.display.id`.
4. Inventory only what the request needs. User apps: `adb shell pm list packages -3`. Disabled: `adb shell pm list packages -d`. System apps: `adb shell pm list packages -s`.
5. For a cleanup or performance check, read `/data` free space, `MemTotal` from `/proc/meminfo`, and these settings: `window_animation_scale`, `transition_animation_scale`, `animator_duration_scale`, `app_process_limit`, and `always_finish_activities`. Leave the background-process limit and "don't keep activities" at the default unless the user asks to change them. Do not change battery optimization unless the user asks.
6. Before any WebView or browser change, run `adb shell dumpsys webviewupdate`. Leave the current WebView package enabled.
7. Propose changes as numbered rows. Separate disable from uninstall. Wait for the user to approve indexes. Re-check that each approved package is still installed before changing it.
8. Record the device, what ran, and what is still proposed in `data/mobile/<device>.md`. Do not write secrets, accounts, or tokens.

Read-only commands: `adb devices -l`, `adb shell getprop <name>`, `adb shell pm list packages`, `adb shell dumpsys webviewupdate`, `adb shell df -h /data`, `adb shell cat /proc/meminfo`, `adb shell settings get global <name>`.

Mutating commands, only after approval:

| Action | Command | Reverse |
| --- | --- | --- |
| Disable for the current user | `adb shell pm disable-user --user 0 <package>` | `adb shell pm enable <package>` |
| Uninstall a preinstalled app for the current user | `adb shell pm uninstall --user 0 <package>` | `adb shell cmd package install-existing <package>` on Android 7 and newer, while the copy remains in the system image |
| Uninstall an app the user installed | `adb uninstall <package>` | Reinstall from its store. This does not keep a system copy. |

`pm uninstall --user 0` removes the app for the current user only. Do not add `-k`, and do not delete the APK from the system image.

Do not disable or uninstall these unless the user names the package and the consequence:

- `android`, `com.android.systemui`, `com.android.settings`, `com.android.providers.settings`
- `com.google.android.gms`, `com.google.android.gsf`, `com.android.vending`
- `com.android.packageinstaller`, `com.google.android.packageinstaller`
- The default launcher and the enabled keyboard
- The active WebView package
- The telephony and in-call packages
- Vendor device-admin, work-profile, and enterprise-policy packages
- The vendor software-update agent
- The vendor device-care or device-maintenance app

Do not enable OEM unlocking. Do not run root, recovery, or wipe commands. Reboot only when the user asks. After a reboot, confirm `sys.boot_completed` is `1` before reporting success.

## Report

Print the [report format](../README.md#report-format). Mode is `read-only` for inspection, `disable` or `uninstall` after an approved change.

Host is the computer plus the device model and serial from `adb devices -l`.

Body rows:

| Index | Package | Kind | Action |
| --- | --- | --- | --- |
| 1 | Package name and a short label | User or system | Proposed, disabled, or uninstalled |

Number the rows when asking for approval. A presence or identity check leaves Index off.

## Safety

Follow [AGENTS.md](../../../AGENTS.md) and [Skills safety](../README.md#safety).

Inspection is read-only. Disable and uninstall modify the device and need explicit approval by index. State the effect, the device, and whether it is reversible before asking. A disable is reversible. Uninstalling a user-installed app removes that app. Uninstalling a preinstalled app for the current user is reversible only while the system image still contains it.

USB debugging grants this computer a shell on the device. Turn it off when the task is finished. Never store passwords, keys, tokens, or other secrets in the skill or in `data/`.
