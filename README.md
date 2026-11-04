<div align="center">

# Clover Splash

**A desktop operating system project by Woods Studio**

*Designed around Obsidian, a cohesive desktop environment with a distinctive visual identity, integrated applications, and a security-focused system architecture.*

**Project status:** Early development · **Primary target:** x86-64 UEFI · **Desktop:** Obsidian

</div>

> [!IMPORTANT]
> **Clover Splash is under active development and is not a production-ready operating system.** The currently developed installer and desktop builds are application-level prototypes. They do not install a complete operating system, provide a secure boot chain, or implement the proposed storage and recovery protections.

## Overview

Clover Splash is a Woods Studio operating system initiative focused on a streamlined desktop experience, predictable system updates, and clear separation between operating system components and personal data.

The planned platform combines a minimal Linux-based system with **Obsidian**, a custom desktop interface built around floating controls, translucent surfaces, a centered application dock, and an integrated suite of Woods Studio applications.

The project is being developed incrementally: interface prototypes are tested first, followed by desktop services, operating system integration, installation, security, recovery, and hardware validation.

### Project priorities

- **Distinctive interface:** consistent layouts, motion, typography, window controls, and application iconography.
- **System reliability:** an intended read-only system design with separate data storage and recovery environments.
- **Security by design:** planned authenticated boot, signed updates, and encrypted user data.
- **Practical recovery:** planned recovery environments and rollback paths for unsuccessful updates.
- **Clear user control:** explicit installation confirmation, accessible settings, and transparent system status.

> [!NOTE]
> The architecture described below is a **development target**, not a statement that every subsystem is already implemented. Implementation and verification status are called out separately.

## Obsidian desktop

**Obsidian** is the planned graphical desktop environment for Clover Splash. Its design draws on familiar desktop interaction patterns while establishing its own navigation, visual styling, and application conventions.

### Desktop experience

| Component | Design direction |
| --- | --- |
| Top navigation | Three floating controls for the Clover Splash menu, active application, and application commands |
| System status | Separate status controls for connectivity, sound, power, time, and quick settings |
| Dock | Centered floating dock with official application icons, running indicators, and app switching |
| Windows | Independent movable and resizable windows with custom Obsidian window controls |
| Appearance | Auto, Light, and Dark themes with official Obsidian wallpapers |
| Applications | Consistent window chrome, navigation patterns, and system integration |
| Workspaces | Planned multi-workspace support with per-display behavior |
| Accessibility | Planned keyboard navigation, scalable interfaces, and accessible interaction states |

> [!TIP]
> The desktop prototypes can be evaluated independently of the future bootable operating system. This allows interface and application behavior to be refined without writing to system disks.

### Application ecosystem

The planned first-party application family includes:

| Application | Purpose |
| --- | --- |
| Files | Browse and manage files and folders |
| Preferences | Configure system, display, appearance, and device settings |
| Clover Store | Discover and manage supported applications |
| Terminal | Command-line access and development workflows |
| Activity Monitor | Inspect running processes and resource usage |
| Diagnostics | Review hardware and system health information |
| Disk Utility | Inspect storage and, in future trusted builds, manage supported disk operations |
| Gadgets | Desktop utilities and compact information surfaces |
| Clock | Time, alarms, and related utilities |
| Calculator | Everyday calculations |
| Text Editor | Edit plain-text documents |
| Screenshots | Capture and manage screen images |
| Notes | Organize notes |
| Calendar | View events and schedules |
| Photos | Browse image collections |
| Contacts | Manage contact information |
| Maps | Map and location features |
| Keys | Credential-related user interface |
| Wellbeing | Device-use and wellbeing features |
| Finder | Device discovery; distinct from the Files application |

> [!NOTE]
> This table describes the **application roadmap**. Icons, launchers, and placeholder windows do not imply that the listed applications are functionally complete.

## System architecture

The current architecture direction uses a **minimal Linux-based foundation** with a custom desktop and system integration layer. It does not depend on the unverified claim of a completed, freestanding C++ kernel or custom bootloader.

| Layer | Planned responsibilities |
| --- | --- |
| Firmware and boot | x86-64 UEFI boot, verified boot components, and controlled recovery selection |
| Kernel and hardware | Linux kernel, device drivers, graphics, input, networking, and storage support |
| Base system | Service management, device discovery, networking, audio, and system utilities |
| Graphics | Wayland compositor integration and hardware-accelerated rendering where supported |
| Desktop shell | Obsidian interface, dock, menus, window coordination, and system status |
| System services | Authorized settings changes, update coordination, and device management |
| Applications | Woods Studio applications and future supported third-party software |

Candidate technologies include **Qt 6/QML** for interface development and a **Wayland-based** graphics stack. Final integration choices remain subject to testing.

### Planned storage layout

The target installation design separates boot files, operating system images, user data, and recovery images:

```text
GPT disk
├── EFI System Partition
├── System A                 (read-only system image)
├── System B                 (alternate system image)
├── Data                     (encrypted user data)
├── Recovery A               (recovery environment)
└── Recovery B               (alternate recovery environment)
```

The intended A/B design supports staged updates and fallback to a known-good system image. The Data partition is intended to use LUKS2 encryption, with a recovery key controlled by the device owner.

> [!WARNING]
> **The partition layout is a specification, not an installer capability.** Current development prototypes must not be used to partition, erase, or install to physical disks. Back up important data before testing any future disk-writing installer.

## Security model

Clover Splash is being designed around authenticated system components, least-privilege services, and recoverable updates.

### Intended controls

1. **Verified boot path:** validate boot-critical components using an appropriate signed boot chain.
2. **Immutable system images:** protect operating system content using read-only images and integrity verification.
3. **Signed updates:** accept update artifacts only after cryptographic validation.
4. **Encrypted user data:** protect persistent personal data with LUKS2 and a user-held recovery mechanism.
5. **Service authorization:** restrict privileged operations to authenticated, narrowly scoped system interfaces.
6. **Recovery and rollback:** maintain independently verifiable recovery paths and a fallback system image.

> [!IMPORTANT]
> **Network access should not be the sole condition for a trusted boot.** A device must be able to validate locally trusted boot components without depending on a live CDN or GitHub API response. Online services may be used for update discovery and delivery, but should not replace signature and integrity verification.

### Update and recovery approach

The planned update process stages a signed system image into the inactive system slot, validates it, and activates it on a subsequent boot. If activation or health checks fail, the design calls for a controlled rollback or recovery path.

System repair should avoid modifying immutable images in place. Recovery operations must preserve user data wherever feasible, clearly disclose destructive actions, and require appropriate authorization.

> [!CAUTION]
> Recovery operations can carry data-loss risk. No implementation should promise that user data can *never* be lost. Recovery keys must not be silently uploaded or escrowed, and recovery workflows must not bypass authentication or integrity checks.

## Installer

The planned distribution format is a **bootable installer image** rather than a general-purpose live desktop release. The initial installation model is intended to support a clearly disclosed, full-disk installation path.

The proposed installer flow is:

1. Welcome and compatibility information.
2. Hardware and firmware checks.
3. Explicit destination disk selection.
4. Review of the planned partition layout and destructive operations.
5. Encryption configuration.
6. Recovery-key generation and user confirmation.
7. Installation, verification, and detailed progress reporting.
8. Completion with an explicit user-controlled restart.

> [!WARNING]
> A future full-disk installation will erase data on the selected destination. The existing installer prototypes are **safe simulations** and do not perform these operations.

## Development status

| Workstream | Current state |
| --- | --- |
| Obsidian interface | Desktop prototypes in development |
| Official visual assets | Wallpapers, logo, and application icons incorporated into prototypes |
| Installer interface | Multi-step Qt/QML simulation available in development |
| Application windows | Prototype launch, navigation, and window behavior |
| Functional first-party apps | Planned / incremental implementation |
| Native Wayland shell integration | Planned |
| Bootable installer image | Not yet validated |
| Signed boot and immutable system images | Architecture target; not implemented or verified |
| Encryption and recovery | Architecture target; not implemented or verified |
| Physical hardware installation | Not supported by the current prototypes |

### Development milestones

- **Desktop foundation:** establish Obsidian navigation, window management, themes, dock, and app framework.
- **Core applications:** implement Files, Preferences, Terminal, and essential utilities.
- **System integration:** connect the desktop to real services with defined authorization boundaries.
- **OS image:** assemble and boot the base system in a virtual machine.
- **Installation and recovery:** implement and test the storage, encryption, update, and recovery design.
- **Hardware validation:** test graphics, networking, input, suspend/resume, and installation on supported devices.

Milestones are directional and do not represent a release schedule.

## Development environment

Current desktop and installer prototypes use **Python, PyQt6, and QML** and are being tested in **Ubuntu under WSL2**. These prototypes run as ordinary graphical applications; they do not replace the host desktop.

A typical local prototype run uses:

```bash
# From a prototype directory containing main.py and Main.qml
python3 main.py
```

On WSL2 systems that require software rendering:

```bash
QT_QUICK_BACKEND=software LIBGL_ALWAYS_SOFTWARE=1 python3 main.py
```

> [!NOTE]
> Commands and dependencies vary by prototype. Refer to the README and launcher included with the specific development snapshot. The commands above are **not** instructions for building a complete bootable Clover Splash OS.

## Repository organization

As the project grows, its source is expected to be organized around these areas:

```text
clover-splash/
├── desktop/           # Obsidian desktop and shell components
├── applications/      # First-party application projects
├── installer/         # Installer interface and installation engine
├── system/            # OS integration and system services
├── assets/            # Approved branding, wallpapers, and icons
├── docs/              # Architecture, design, and development documentation
└── tests/             # Automated and manual test resources
```

> [!NOTE]
> This is a **proposed repository structure**, not a verified listing of the current repository. Individual prototypes may be stored in separate directories while development continues.

## Contributing and reporting issues

When reporting an issue, include the affected prototype or build identifier, your operating environment, the steps needed to reproduce the problem, the expected behavior, and the observed behavior. Include relevant terminal output where possible, but remove passwords, recovery keys, tokens, and other private information.

Changes affecting storage, boot, authentication, or update verification require additional scrutiny and should not be considered safe based solely on a successful graphical test.

## Project information

**Project:** Clover Splash  
**Desktop environment:** Obsidian  
**Developer:** Woods Studio  
**Status:** Early development  
**Target platform:** x86-64 UEFI

> [!NOTE]
> Licensing terms, supported hardware, release downloads, and contribution policies should be published only when confirmed by the project maintainers. No license or production support commitment is implied by this README.

---

<div align="center">

**Clover Splash Obsidian**

Designed with <3 by Woods Studio in Lyon, France. © Woods Studio. All Rights Reserved

</div>
