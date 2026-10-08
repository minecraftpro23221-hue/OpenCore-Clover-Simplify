# ⚡ Clover-OpenCore Simplify

<p align="center">
  <b>An automated, intelligent Hackintosh EFI builder and validator for OpenCore and Clover.</b>
</p>

<p align="center">
  <a href="#license"><img src="https://img.shields.io/badge/License-BSD--3--Clause-blue.svg" alt="BSD-3 License"></a>
  <a href="https://www.python.org/"><img src="https://img.shields.io/badge/Python-3.10%2B-brightgreen.svg" alt="Python Version"></a>
  <img src="https://img.shields.io/badge/Platform-macOS%20%7C%20Linux%20%7C%20Windows-lightgrey.svg" alt="Platform">
  <img src="https://img.shields.io/badge/Bootloader-OpenCore%20%7C%20Clover-orange.svg" alt="Bootloader">
</p>

---

> [!WARNING]
> ### Important Disclaimer & Liability Waiver
> * **No Guarantee of Bootability:** Hackintosh compatibility depends heavily on specific motherboard chipsets, CPU microarchitectures, BIOS revisions, and GPU configurations. This tool **cannot guarantee** that a generated EFI will successfully boot on your exact machine.
> * **Hardware Compatibility Varies:** This builder is designed primarily for mainstream x86 hardware. It **will not work on all devices**, especially non-standard or unsupported OEM laptop hardware.
> * **Test Safely First:** Always test newly generated EFI folders on a FAT32-formatted **external USB drive** before overwriting the primary boot partition on your system drive.
> * **Limitation of Liability:** The repository owner, developers, and contributors assume **no responsibility or liability** for data loss, hardware damage, corrupted system drives, unbootable operating systems, or software conflicts resulting from the use of this project. Use at your own risk.

---

## 📌 Table of Contents
- [Overview](#-overview)
- [Key Features](#-key-features)
- [System Requirements](#-system-requirements)
- [Installation](#-installation)
- [Safe Recommended Workflow](#-safe-recommended-workflow)
- [Interactive Menu Overview](#-interactive-menu-overview)
- [Bootloader Architecture](#-bootloader-architecture)
  - [OpenCore Integration](#opencore-integration)
  - [Clover Integration](#clover-integration)
- [Core Engines & Utilities](#-core-engines--utilities)
  - [ACPI Dump & Compilation](#acpi-dump--compilation)
  - [USB Mapping (USBToolBox)](#usb-mapping-usbtoolbox)
  - [Intel iGPU Framebuffer Tuning](#intel-igpu-framebuffer-tuning)
  - [OCValidate & Auto-Repair Engine](#ocvalidate--auto-repair-engine)
- [Troubleshooting](#-troubleshooting)
- [Known Limitations](#-known-limitations)
- [Credits & Acknowledgments](#-credits--acknowledgments)
- [License](#-license)

---

## 📖 Overview

Building a working Hackintosh EFI from scratch often requires hours of tedious manual labor: sniffing hardware IDs, compiling `.dsl` files into binary `.aml` hotpatches, calculating iGPU framebuffer values, mapping USB ports, and resolving strict XML schema errors.

**Clover-OpenCore Simplify** automates this workflow end-to-end. By detecting your system hardware, extracting real ACPI tables, downloading upstream kexts, and verifying `config.plist` keys against official bootloader schemas, it produces clean, structured EFI trees ready for testing.

---

## ✨ Key Features

- 🔍 **Hardware Sniffer Detection:** Scans system devices and outputs a standardized `SysReport.plist`.
- 📊 **Deep Component Analysis:** Parses CPU family, Intel iGPU / AMD dGPU device IDs, audio codecs, PCI network adapters, NVMe/SATA storage controllers, and USB topologies.
- 🛠️ **Automated ACPI Pipeline:** Dumps raw ACPI tables (`acpidump`), detects required hotpatches, compiles binary `.aml` files using `iasl`, and inserts them into your bootloader configuration.
- 📦 **Automated Kext Dependencies:** Downloads the latest stable versions of required kexts directly from upstream open-source releases.
- 🔌 **Native USB Mapping:** Integrates **USBToolBox** to identify active USB ports and generate a tailored `UTBMap.kext` / `USBMap.kext`.
- ⚙️ **Dual Bootloader Generation:** Builds full, bootable directory structures for **OpenCore** (`EFI/OC`) and **Clover** (`EFI/CLOVER`).
- 🖥️ **Customizable iGPU Framebuffer Tuning:** Interactively configures WhateverGreen properties (`AAPL,ig-platform-id`, stolen memory, unified memory, eDP/HDMI connector overrides).
- 🛡️ **OCValidate Auto-Repair Engine:** Runs official `ocvalidate` binaries against generated configurations. If schema errors occur, it automatically rebuilds and repairs the configuration against the matching OpenCore `Sample.plist`.
- 🔄 **Post-Repair Resource Re-Sync:** Re-copies and registers all ACPI binaries, drivers, kexts, and config keys after any repair cycle.

---

## 📋 System Requirements

| Requirement | Details |
| :--- | :--- |
| **Supported OS** | macOS 10.15+, Linux (x86_64), or Windows 10/11 |
| **Python** | Python `v3.10` or higher |
| **Tool Dependencies** | `git`, `curl` / `wget`, `zip` / `unzip` |

---

## 🚀 Installation

```bash
# 1. Clone the repository
git clone [https://github.com/your-username/Clover-OpenCore-Simplify.git](https://github.com/your-username/Clover-OpenCore-Simplify.git)
cd Clover-OpenCore-Simplify

# 2. Set up Python virtual environment
python3 -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# 3. Install required packages
pip install -r requirements.txt

# 4. Make executable scripts runnable (macOS / Linux)
chmod +x scripts/*.sh tools/*
