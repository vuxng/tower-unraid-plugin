# Tower Extension for Unraid

The server extension that adds Docker app installation and Linux VM creation to Tower, the native Unraid client for iOS and macOS.

This repository distributes installer files and release information. The Tower app is developed in a separate repository.

## Download

- [Tower Extension v0.2.4 — preview](https://github.com/vuxng/tower-unraid-plugin/releases/tag/v0.2.4)
- [Download tower.plg](https://github.com/vuxng/tower-unraid-plugin/releases/download/v0.2.4/tower.plg)
- [Download the SHA-256 checksum](https://github.com/vuxng/tower-unraid-plugin/releases/download/v0.2.4/tower.plg.sha256)

Version 0.2.4 is a preview. Automated tests cover packaging, permissions and simulated resource creation. A live upgrade from a failed legacy installation was verified on Unraid 7.3.2 / API 4.35.1 on 2026-09-12: Tower confirmed the loaded 0.2.4 module after API restart and loaded the Docker app catalog. Reboot persistence, live removal and resource creation remain unverified; the VM options read on that host still reported an operation failure.

This release also retains the fix for 0.2.1's rejection of compatible `reflect-metadata@0.1.14` hosts. It supports `^0.1.13 || ^0.2.0` without upgrading the host library; tests run with 0.1.14.

Version 0.2.4 installs and removes only Tower's module directory and manifest/configuration entries. It no longer runs npm in Unraid's API directory. Reproduction on the official API 4.35.1 package found that the old npm installer pruned four existing packages, including `passport`, causing fresh CLI processes to fail while the already-running API remained reachable.

For affected API **4.35.1+a9625ae2** hosts with Tower 0.2.1–0.2.3 and missing `passport`, this release restores only absent `passport`, `pause`, `@types/inquirer` and `@types/through` packages. Recovery uses the official Unraid 4.35.1 TXZ, verified against SHA-256 `111c7ed887abe6e5cb31f73595929a397c5d6db1b8741a7a7518a006301ad40f`. It uses Unraid's cached installer when available; otherwise it downloads that exact release from `unraid/api` on GitHub. Existing packages are not overwritten. Other API versions require manual diagnosis.

CLI startup is checked before Tower installation and again after registration. A failed check reports the underlying error and schedules no API restart.

## Install

In a Tower build configured for this release, open **Docker → Install App** or **VMs → Create VM**, then choose **Install Tower Extension**. Confirm the target server and download source. The server downloads the installer and restarts its API; Tower reconnects and checks that the extension is ready.

Alternatively, open **Plugins → Install Plugin** in the Unraid WebGUI and paste:

```text
https://github.com/vuxng/tower-unraid-plugin/releases/download/v0.2.4/tower.plg
```

The installer contains the extension and its XML parser dependencies in one file. It verifies the embedded archive before installation. The API restart is intended to leave existing VMs and containers running.

If 0.2.1 failed specifically with `Unsupported Unraid API dependency: reflect-metadata@0.1.14`, it stopped before installing the module. After reviewing the failed result, select **Prepare New Installation** and confirm the review in Tower and install 0.2.4 using a build configured for this release.

## Requirements

- Unraid 7.2 or later with its built-in API and official API plugin loader.
- Node.js 22 or later and compatible host dependencies: Nest 11, Nest GraphQL 13, GraphQL 16 and nest-authz 2. The installer checks these before changing the server.
- To install from Tower, an API key with `CONFIG:READ_ANY` and `CONFIG:UPDATE_ANY`, or the administrator role. Docker and VM creation permissions are separate.
- Docker app installation requires Docker to be enabled and access to the Community Applications catalog and public image registries.
- VM creation requires VM Manager, KVM, mounted storage, `isos` and `domains` shares, and a Linux ISO already available in the ISO share.

## Removal and releases

Remove the extension from **Unraid → Plugins → Tower → Remove** after pending Tower operations finish. Removal preserves VMs, containers, disks, templates and operation records.

Each release has its own download URL. Published installer assets are not replaced; changes receive a new version. This extension is maintained by Tower and is not an official Lime Technology plugin.
