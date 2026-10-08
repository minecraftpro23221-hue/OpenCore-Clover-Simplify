 Clover-OpenCore SimplifyClover-OpenCore Simplify is an automated, intelligent Hackintosh EFI builder designed to streamline the creation, configuration, and validation of bootable EFI folders for both OpenCore and Clover bootloaders.By analyzing system hardware, extracting ACPI tables, fetching upstream kext dependencies, integrating native USB mapping, and automatically repairing configuration files against OpenCore schemas, Clover-OpenCore Simplify takes the manual friction out of building custom EFI trees.[!WARNING]DISCLAIMER & BOOT LIABILITYNo Guarantee of Bootability: This tool generates configuration files based on automated heuristics and upstream open-source templates. It is not guaranteed to work out-of-the-box on every system or hardware configuration.Hardware Variance: Hackintosh compatibility varies drastically across CPU generations, motherboard chipsets, discrete graphics cards, network cards, and BIOS implementations.User Responsibility: Always test newly generated EFI folders on an external USB flash drive before overwriting your primary boot loader.Limitation of Liability: The repository owner, developers, and contributors are not responsible for data loss, hardware damage, corrupted system partitions, boot failures, or any other issues arising from the use of this software. Use at your own risk.Table of ContentsFeaturesRequirementsInstallationSafe Recommended WorkflowUsage & Interactive MenuOpenCore IntegrationClover IntegrationACPI Processing & PatchingUSB MappingIntel iGPU & Framebuffer PatchingOCValidate & Auto-Repair EngineTroubleshootingLimitations & Device CompatibilityCredits & AcknowledgmentsLicenseFeaturesHardware Sniffer Detection: Leverages hardware detection utilities to generate a comprehensive SysReport.plist detailing system specifications.Deep Hardware Analysis: Automatically categorizes CPU family, Intel iGPU / AMD dGPU models, audio codecs, PCI network adapters, SATA/NVMe storage controllers, USB topologies, and ACPI paths.Automated ACPI/DSDT Table Dump: Dumps raw ACPI tables (acpidump), parses DSDT/SSDTs, and automatically compiles needed .aml patches (SSDT-EC, SSDT-PLUG, SSDT-AWAC, SSDT-PNLF, SSDT-dGPU-Dis, etc.) using iasl.Dynamic Kext Management: Downloads and integrates the latest stable releases of required kexts and their prerequisites directly from upstream repositories.Native USB Mapping: Integrates USBToolBox to generate a custom USB mapping kext based on active physical ports and port types.Dual Bootloader Architecture: Builds complete, bootable directory structures for both OpenCore (EFI/OC/) and Clover (EFI/CLOVER/), including required drivers, resources, tools, and config.plist configurations.Customizable Framebuffer Engine: Offers fine-grained control over Intel iGPU framebuffer patching (WhateverGreen), stolen memory allocation, connector overrides, and device spoofing.OpenCore Schema Auto-Repair: Validates generated OpenCore config.plist files using ocvalidate. If schema mismatches or missing keys are detected, it automatically rebuilds and fixes the configuration against the matching OpenCore Sample.plist.Post-Repair Resource Re-Sync: Re-copies and updates all required kexts, ACPI binaries, drivers, and configuration entries following any schema rebuild or repair cycle.RequirementsOperating SystemmacOS 10.15 (Catalina) or newer (Recommended for full toolchain support)Linux (x86_64) or Windows 10/11 (Python CLI mode with system tool dependencies)DependenciesPython: v3.10 or higherRequired System Utilities: git, curl / wget, zip / unzipPython Packages:Bashpip install -r requirements.txt
InstallationClone the Repository:Bashgit clone https://github.com/your-username/Clover-OpenCore-Simplify.git
cd Clover-OpenCore-Simplify
Set Up Python Environment:Bashpython3 -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
pip install --upgrade pip
pip install -r requirements.txt
Verify Tool Permissions (macOS/Linux):Bashchmod +x scripts/*.sh tools/*
Safe Recommended WorkflowTo ensure a smooth creation process and avoid boot issues, follow this step-by-step workflow: ┌────────────────────────┐      ┌────────────────────────┐      ┌────────────────────────┐
 │ 1. Run Python Program  │ ───► │ 2. Detect Hardware     │ ───► │ 3. Generate SysReport   │
 └────────────────────────┘      └────────────────────────┘      └────────────────────────┘
                                                                             │
 ┌────────────────────────┐      ┌────────────────────────┐                  ▼
 │ 6. Map USB Ports       │ ◄─── │ 5. Configure iGPU      │ ◄─── ┌────────────────────────┐
 └────────────────────────┘      └────────────────────────┘      │ 4. Dump & Parse ACPI   │
             │                                                   └────────────────────────┘
             ▼
 ┌────────────────────────┐      ┌────────────────────────┐      ┌────────────────────────┐
 │ 7. Select OC / Clover  │ ───► │ 8. Generate EFI Folder │ ───► │ 9. Run OCValidate      │
 └────────────────────────┘      └────────────────────────┘      └────────────────────────┘
                                                                             │
                                                                             ▼
                                                                 ┌────────────────────────┐
                                                                 │ 10. Test on USB Drive  │
                                                                 └────────────────────────┘
Run the Program: Launch the main python interface (python3 main.py).Detect Hardware: Execute the automated hardware scan.Generate SysReport.plist: Save and inspect the hardware summary generated by Hardware Sniffer.Dump ACPI Tables: Extract system ACPI tables and let the builder analyze required SSDT hotpatches.Configure Framebuffer Settings: Customize iGPU settings, layout-ids, and dGPU disable flags.Map USB Ports: Run the USBToolBox integration step to discover ports and generate USBMap.kext / UTBMap.kext.Select Bootloader: Choose OpenCore, Clover, or Both.Generate EFI: Build the complete target directory structure and config.plist.Run Schema Validation: Perform an ocvalidate check to verify structural integrity.Review and Test: Inspect logs, review warnings, and test the generated EFI on an external USB flash drive before placing it on your primary boot drive.Usage & Interactive MenuLaunch the application interactively:Bashpython3 main.py
Interactive Menu OverviewPlaintext====================================================================
                  Clover-OpenCore Simplify v1.0
====================================================================
 [1]  Run Full Hardware Detection (Hardware Sniffer -> SysReport.plist)
 [2]  Dump & Analyze ACPI / DSDT / SSDT
 [3]  Configure iGPU & WhateverGreen Framebuffer Patches
 [4]  Map USB Ports (USBToolBox Integration)
 [5]  Fetch & Sync Required Kexts / Dependencies
 [6]  Generate OpenCore EFI Structure
 [7]  Generate Clover EFI Structure
 [8]  Generate Both (OpenCore + Clover)
 [9]  Validate EFI Configuration (ocvalidate Engine)
 [10] Run Auto-Repair & Schema Alignment (Sample.plist Rebuild)
 [11] Display Hardware Diagnostic Recap
 [0]  Exit
====================================================================
Select an option: 
OpenCore IntegrationWhen targeting OpenCore, the application constructs an OpenCore-compliant directory tree:EFI/
└── OC/
    ├── ACPI/              # Compiled .aml hotpatches
    ├── Drivers/           # OpenRuntime.efi, OpenCanopy.efi, ResetNvramEntry.efi, etc.
    ├── Kexts/             # Lilu, VirtualSMC, WhateverGreen, AppleALC, etc.
    ├── Resources/         # OpenCanopy icons and audio resources
    ├── Tools/             # OpenShell.efi
    └── config.plist       # Auto-generated & validated OpenCore config
Automatically maps entries into ACPI -> Add, Kernel -> Add, and UEFI -> Drivers.Configures proper quirks based on CPU architecture (Nehalem through Raptor Lake, AMD Ryzentosh).Sets appropriate Booter and Kernel quirks (RebuildAppleMemoryMap, EnableWriteUnprotector, XhciPortLimit, etc.).Clover IntegrationWhen targeting Clover, the application generates a structure compatible with recent Clover revisions:EFI/
└── CLOVER/
    ├── ACPI/
    │   └── patched/      # Compiled .aml hotpatches
    ├── drivers/
    │   └── UEFI/          # OpenRuntime.efi, FSInject.efi, HfsPlus.efi
    ├── kexts/
    │   └── Other/         # Lilu, VirtualSMC, WhateverGreen, AppleALC, etc.
    └── config.plist       # Auto-generated Clover config
Inject Kexts configured under SystemParameters -> InjectKexts.Graphics settings populated under Graphics -> Inject and ig-platform-id.Automatic ACPI fix additions mapped to Clover's ACPI patch array.ACPI Processing & PatchingThe application handles ACPI patching automatically:Extraction: Invokes native acpidump tools to pull system tables.Analysis: Scans DSDT for missing devices, conflicting timers, and power targets.Compilation: Compiles DSL hotpatches into binary .aml using iasl:SSDT-EC-USBX: Embedded Controller fix and USB power properties.SSDT-PLUG: CPU plugin-type power management.SSDT-AWAC: Real-Time Clock fix for 300-series and newer chipsets.SSDT-PNLF: Display backlight control for laptops.SSDT-dGPU-Dis: Disables unsupported discrete GPUs on dual-GPU laptops.Registration: Inserts generated binary .aml file paths directly into the target config.plist.USB MappingProper USB mapping prevents sleep/wake crashes, Bluetooth disconnects, and performance issues.                  ┌──────────────────────────────┐
                  │    Discover Physical Ports   │
                  └──────────────┬───────────────┘
                                 │
                                 ▼
                  ┌──────────────────────────────┐
                  │   Assign USB Connector Types │
                  │  (USB 2, USB 3, Type-C, Internal)
                  └──────────────┬───────────────┘
                                 │
                                 ▼
                  ┌──────────────────────────────┐
                  │   Generate UTBMap.kext +     │
                  │   USBToolBox.kext Injection  │
                  └──────────────────────────────┘
The script bridges USBToolBox, identifying active controller ports, setting correct connector types (Type-A, Type-C, Internal / Bluetooth), enforcing the 15-port limit per controller, and bundling the output kext into EFI/OC/Kexts or EFI/CLOVER/kexts/Other.Intel iGPU & Framebuffer PatchingThe interactive framebuffer editor configures Intel HD/UHD/Iris Graphics using WhateverGreen properties:GenerationCPU Code NameRecommended AAPL,ig-platform-idDevice-ID SpoofHaswell4th Gen Core0000160A / 0600260A-Broadwell5th Gen Core00001616-Skylake6th Gen Core00001619 / 00001219-Kaby Lake7th Gen Core00001659 / 00008A59-Coffee Lake8th/9th Gen Core00009B3E / 0000A53E-Comet Lake10th Gen Core00009B3E / 0300C89B-Supported Framebuffer Propertiesframebuffer-patch-enable (01000000)framebuffer-stolenmem (DVMT pre-alloc fixes)framebuffer-fbmem (Framebuffer memory allocations)framebuffer-conX-enable / framebuffer-conX-type (eDP, HDMI, DisplayPort overrides)disable-external-gpu (Disables unsupported dGPUs)OCValidate & Auto-Repair EngineWhen building OpenCore configurations, schema errors can prevent booting or cause black screens. Clover-OpenCore Simplify includes a validation and repair loop:Validation Check: Executes ocvalidate against the generated config.plist.Error Parsing: Logs schema mismatches, missing keys, outdated syntax, or invalid array structures.Automated Schema Repair:Downloads the exact matching Sample.plist corresponding to the target OpenCore version.Re-populates user settings into a clean schema structure.Cleans deprecated keys and injects required missing properties.Resource Sync: Re-copies all kexts, ACPI files, and drivers to ensure file references match the newly repaired configuration.Troubleshooting[!NOTE]Below are common issues and their recommended remedies when configuring or booting an EFI.1. Stuck on [EB|#LOG:EXITBS:START]Cause: Incorrect memory alignment, boot-args, or CPU quirks.Fix: Verify DevirtualiseMmio, EnableWriteUnprotector vs RebuildAppleMemoryMap / SyncRuntimePermissions for your CPU architecture.2. Black Screen After Verbose OutputCause: iGPU framebuffer misconfiguration or dGPU power state conflict.Fix: Re-check AAPL,ig-platform-id, add agdpmod=pikera (for AMD RX 5000/6000 series), or ensure SSDT-dGPU-Dis is enabled for unsupported laptop dGPUs.3. Kernel Panic: "Waiting for Root Device"Cause: Missing USB controller drivers or invalid USB mapping.Fix: Ensure USBToolBox.kext + UTBMap.kext (or USBInjectAll.kext) are injected in config.plist. Try plugging the boot drive into a USB 2.0 port.Limitations & Device CompatibilityNot Universal: Hardware configurations vary widely. Certain proprietary vendor laptop components (e.g., custom Microsoft Surface hardware, exotic EC controllers) may require manual DSDT edits beyond what automated tools can patch.Unsupported Hardware:Intel CPUs: Intel Atom, Celeron, and Pentium CPUs lacking full iGPU support are not natively supported by macOS graphics drivers.NVIDIA GPUs: NVIDIA Maxwell, Pascal, Turing, Ampere, and Ada Lovelace GPUs are unsupported on modern macOS versions.Wi-Fi / Bluetooth: Qualcomm, Realtek, and MediaTek Wi-Fi chipsets have limited or no macOS driver support. Intel cards require itlwm / AirportItlwm, while supported Broadcom cards require OpenCore-Legacy-Patcher root patches on macOS Sonoma and newer.Credits & AcknowledgmentsThis project relies on the open-source Hackintosh ecosystem. Gratitude and credit go to the developers and maintainers of these projects:Acidanthera: Developers of OpenCorePkg, Lilu, WhateverGreen, AppleALC, VirtualSMC, BrightnessKeys, CPUFriend, and FeatureUnlock.CloverHACK Team: Maintainers of the Clover bootloader.USBToolBox Team: Developers of the USBToolBox framework and mapping tool.VoodooI2C Team: Developers of VoodooI2C touchpad drivers.ACPICA / Intel: Developers of the iasl ACPI compiler and utilities.LicenseThis project is licensed under the MIT License. Third-party kexts, tools, and binaries fetched or packaged by this application remain under their respective open-source licenses provided by their original authors.
