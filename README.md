# FLIPPER ONE SIMULATOR

**A standalone, offline, browser-based Flipper One hardware and software simulator.**

[![HTML](https://img.shields.io/badge/HTML5-Standalone-E34F26?logo=html5&logoColor=white)](https://github.com/kai9987kai/FLIPPER-ONE-SIMULATOR)
![Offline](https://img.shields.io/badge/Offline-Ready-159957)
![JavaScript](https://img.shields.io/badge/JavaScript-Vanilla-F7DF1E?logo=javascript&logoColor=black)
![Type](https://img.shields.io/badge/Type-Behavioural%20Simulator-ff9829)

FLIPPER ONE SIMULATOR recreates key parts of the [Flipper One](https://docs.flipper.net/one) concept as an interactive virtual device. It combines a clickable handheld interface, a **256 × 144 framebuffer**, a behavioural model of the **RP2350 MCU ↔ RK3576 Linux CPU** connection, simulated power and thermal management, a GPIO workbench, an M.2 module bay, a virtual Linux console, and an isolated networking lab.

**No installation, server, login, build tools, external JavaScript libraries, or internet connection are required for the core simulator.** The complete application—including its styling, scripts and embedded device artwork—is contained in one HTML file.

> [!IMPORTANT]
> This is an **independent educational project**, not an official Flipper Devices product or a cycle-accurate emulator. It **does not execute the official Flipper One firmware, Linux kernel, ARM instructions, RF circuitry, PCIe hardware or physical GPIO**. Network scans and ordinary hardware readings are synthetic. Optional browser device APIs require explicit permission.

## Quick start

**[Download the standalone HTML file](https://raw.githubusercontent.com/kai9987kai/FLIPPER-ONE-SIMULATOR/main/Flipper_One_Standalone_Simulator.html)** and open it in a modern desktop browser such as Chrome or Edge.

Alternatively, clone the repository:

```bash
git clone https://github.com/kai9987kai/FLIPPER-ONE-SIMULATOR.git
cd FLIPPER-ONE-SIMULATOR
```

Then open `Flipper_One_Standalone_Simulator.html` in your browser. On Windows you can also use:

```powershell
Start-Process .\Flipper_One_Standalone_Simulator.html
```

No `npm install`, build process, Python packages or backend services are needed.

> Browser-backed features such as microphone input and Web Serial depend on browser support, security context and permissions. They may work best from `http://localhost` instead of a local `file://` URL. The virtual simulator itself works offline.

## Features

### Interactive handheld interface

- Flipper One-inspired device rendering with clickable controls.
- Native-resolution **256 × 144 canvas LCD** with pixel-style output.
- D-pad, centre/select, touchpad, five soft keys, Back, App Switcher, Power and Push-to-Talk.
- Simulated application menu and stateful navigation.
- Network activity indicators, battery/charge information and a device event log.
- Optional second browser window that mirrors the framebuffer as a **virtual HDMI display**.

### Dual-processor behavioural simulation

The simulation follows the separation described in Flipper One's published engineering documentation:

| Subsystem | Modelled role |
| --- | --- |
| Raspberry Pi RP2350 | Low-power MCU, controls, LCD, events and power states |
| Rockchip RK3576 | Linux applications, terminal, networking and peripheral state |
| SPI | Conceptual Linux framebuffer transfer to MCU |
| I²C + INT | Linux-master command/event exchange and MCU interrupt notification |
| UART | Virtual Linux-console and diagnostic messages |
| BOOT0 / BOOT1 | Virtual recovery and boot-source selection |

You can transition between **Linux running**, **MCU-only operation**, **full shutdown** and **virtual MaskROM recovery**. The Interconnect panel records bus/event counters and allows individual I²C, SPI, UART and interrupt actions.

The **UF2 inspector** can load a local `.uf2` file, check its structural magic values and block metadata, and report file information. It **does not run or flash the binary** and is not a full firmware authenticity or compatibility validator.

### Power and thermal lab

- Virtual **22.9 Wh** two-cell Li-ion battery.
- Adjustable CPU workload, ambient temperature and USB-C1 power input.
- Simulated charging/discharging, estimated runtime, heat accumulation and thermal throttling.
- MCU-only power-bank mode and M.2 power-rail toggle.
- Live power and temperature graphs.
- Synthetic battery-gauge and power-monitor telemetry inspired by the documented power subsystem.

**All power and temperature results are simplified estimates**, not measurements or a validated electrical/thermal design tool. Published port limits must not be treated as simultaneously available power or guaranteed cooling capacity.

### GPIO laboratory

The GPIO view presents the documented **20-position expansion header** concept, including power/ground positions, MCU pins `M40` and `M41`, CPU-labelled `A*` and `B*` pins, and USB D+/D−.

- Select virtual pins.
- Choose permitted alternate modes such as GPIO input/output, PWM, ADC, PIO, UART, SPI, I²C and CAN where listed.
- Read or change simulated output values.
- Watch GPIO actions appear in the event log.

The simulator cannot drive real electrical pins and is **not a substitute for checking the actual pin voltage, mux configuration, pin direction or current limits**.

### M.2 Key-B expansion bay

Install or remove **virtual module profiles** and inspect their interface, physical format and indicative power consumption.

| Profile | Format | Simulated interface | Example power |
| --- | --- | --- | ---: |
| 4G modem | 3042 | USB 2.0 + SIM | 4.5 W |
| GNSS receiver | 2242 | USB 2.0 | 1.2 W |
| B+M NVMe storage | 2242 | PCIe 2.1 ×1 | 3.5 W |
| B+M AI accelerator | 2242 | PCIe 2.1 ×1 | 9.5 W |
| Conceptual NTN modem | 3052 | USB 3.1 | 10 W |
| Incompatible M-key NVMe | 2280 | PCIe ×4 | Rejected |

Profiles are **synthetic teaching examples, not specific verified commercial devices**. The bay rejects its incompatible demonstration profile. It does not enumerate real PCIe/USB devices, activate satellite or cellular radios, or perform NPU inference.

### Isolated networking lab

The network lab operates entirely on an **invented four-host 192.168.88.0/24 network**.

- Toggle simulated Wi-Fi, Ethernet 0, Ethernet 1 and VPN states.
- Switch virtual Wi-Fi role between station, access point and logical repeater.
- Run an Nmap-inspired virtual scan with host/port results.
- Generate synthetic Ethernet/IPv4/UDP traffic.
- Download a real-format `.pcap` file containing **synthetic packets** for inspection with Wireshark.

**The simulator does not probe, discover, intercept or capture traffic from your actual network.** VPN and router settings are visual/logical simulation states, not host operating-system routing or security controls.

### Virtual Linux terminal and filesystem

An embedded command interpreter supports a curated set of **simulated** Linux-style commands:

```text
help
uname -a
whoami
pwd
date
uptime
free -h
sensors
top
ip addr
ifconfig
nmcli
lsusb
lspci
dmesg
ls
cat /etc/os-release
m2 list
m2 attach gnss
m2 detach
gpio list
gpio read M40
gpio write M40 1
wifi mode ap
nmap -sT 192.168.88.0/24
tcpdump -c 5
vpn on
vpn off
hdmi on
hdmi off
reboot
shutdown
clear
```

These commands are handled by JavaScript; **they are not executed in your computer's shell or a real Linux virtual machine**.

The Storage panel lets you edit a small virtual filesystem saved in your browser, including example files under `/home/flipper/` and `/etc/`. The simulator does not access your system files.

## Controls

Click the buttons on the device, or use these keyboard shortcuts when focus is not in an editable field:

| Keyboard | Virtual control |
| --- | --- |
| Arrow keys | D-pad navigation |
| Enter | Centre / select |
| Escape | ESC |
| Backspace | Back |
| Tab | App Switcher |
| Space | Run |

The on-screen controls also include View, Edit, Power, the touchpad and Push-to-Talk. The top navigation exposes the full engineering workbench even when the virtual LCD is displaying a different app.

## Simulator panels

| Panel | Purpose |
| --- | --- |
| **Overview** | Device status, shortcuts and self-test |
| **Power & thermal** | Battery, charging, CPU load, temperature and throttling |
| **Network lab** | Synthetic scanning, interfaces, VPN state and PCAP |
| **GPIO** | Pin selection, virtual mux modes and pin values |
| **M.2 bay** | Module installation, compatibility and rail power |
| **Interconnect** | I²C/SPI/UART/INT, boot modes and UF2 inspection |
| **Linux shell** | Virtual terminal and supported commands |
| **Storage** | Editable virtual files |
| **Reference** | Fidelity notes and source documentation |

Use **Run self-test** on the Overview page to check six basic simulator invariants. These are application consistency checks, not validation against real Flipper One hardware.

## Data, privacy and exports

The project is designed for local use.

- Persistent settings and virtual files are stored using browser `localStorage` under the key `flipper-one-state-v1`.
- **Export state** downloads the virtual device configuration and stored files as JSON.
- **Import state** restores a previously exported state.
- **Factory reset** clears the current virtual device state.
- **Download PCAP** creates a standard PCAP container filled with generated lab packets.
- **UF2 inspection** reads a user-selected local file into the browser for format inspection.

No account, telemetry service or network backend is required by the simulator. Standard external documentation links are available for users who choose to open them. If granted, microphone and Web Serial APIs interact with browser-authorised devices; they do **not** automatically communicate using Flipper One's real firmware protocol.

## Project architecture

```text
Flipper_One_Standalone_Simulator.html
  ├── Embedded CSS and responsive workbench
  ├── Embedded device image
  ├── Canvas 256 × 144 virtual LCD
  ├── Physical-control hit regions and keyboard mapper
  ├── RP2350 / RK3576 behavioural state machine
  │    ├── Linux / MCU / OFF / MaskROM modes
  │    ├── I²C, SPI, UART, INT event accounting
  │    └── Power and thermal approximation
  ├── GPIO and M.2 model profiles
  ├── Synthetic network hosts and PCAP writer
  ├── Virtual Linux command interpreter
  ├── Browser-local filesystem and JSON import/export
  └── Optional microphone, Web Serial and framebuffer mirror
```

The application is intentionally distributed as **one self-contained HTML file** with no package manager or framework.

## Accuracy, limitations and scope

| Feature | Fidelity |
| --- | --- |
| Clickable UI / framebuffer | Functional browser implementation |
| State changes, GPIO model, M.2 checks | Interactive behavioural simulation |
| Interprocessor buses and interrupts | Event-level conceptual model |
| Power, battery and temperature | Approximate synthetic model |
| Network hosts and command results | Synthetic offline lab |
| PCAP file format | Actual downloadable PCAP; packet contents synthetic |
| UF2 reading | Structural inspection only |
| RP2350 / RK3576 instruction execution | **Not implemented** |
| Official MCU firmware, FreeRTOS, Linux kernel | **Not executed** |
| Physical RF, GPIO, USB PD, PCIe, Ethernet | **Not emulated electrically** |
| Actual GPU/NPU or hardware HDMI output | **Not implemented** |

Flipper One is an evolving hardware project. This simulation is based on publicly described interfaces and illustrative device references, not a claim that every preliminary feature is available on final production hardware.

## Troubleshooting

**The HTML opens but a panel does not respond.** Use a current desktop Chrome or Edge build, enable JavaScript and reload. Check the browser developer console for errors.

**My virtual settings are missing.** Local storage is browser- and origin-specific, and private browsing or privacy settings may clear it. Export JSON if you want a portable backup.

**The terminal command says “not implemented.”** The terminal is a small explicit command interpreter. Run `help` to see the supported subset.

**The scan cannot find my router or computer.** This is intentional: only the isolated synthetic lab subnet exists.

**Web Serial or microphone is unavailable.** Confirm browser support, user permission and secure-context requirements. Try serving the HTML from localhost for optional APIs.

**A UF2 was accepted but firmware did not run.** UF2 inspection only validates parts of the container. This simulator cannot execute or flash RP2350 firmware.

## References

The architecture and interface design are informed by Flipper Devices' public documentation:

- [Flipper One documentation](https://docs.flipper.net/one)
- [Technical specifications](https://docs.flipper.net/one/general/tech-specs)
- [MCU firmware architecture](https://docs.flipper.net/one/mcu-firmware/architecture)
- [MCU ↔ CPU interconnect](https://docs.flipper.net/one/mcu-firmware/mcu-cpu-interconnect)
- [Power subsystem](https://docs.flipper.net/one/hardware/power-subsystem)
- [Official Flipper One MCU firmware repository](https://github.com/flipperdevices/flipperone-mcu-firmware)

## Contributing

Issues and pull requests for bug fixes, accessibility improvements, UI enhancements, documentation, and better-defined simulation models are welcome. Please explain whether a proposed feature is **behaviourally simulated**, **browser-backed**, or **verified against real hardware**. If a change affects electrical or timing fidelity, include its public technical reference and assumptions.

## Attribution and disclaimer

Created by [kai9987kai](https://github.com/kai9987kai).

Flipper One, its product design, and associated trademarks belong to their respective owners. This repository is an **unofficial, independent simulator** and is not affiliated with, endorsed by, or maintained by Flipper Devices.

No licence is declared in this README. Refer to the repository's actual licence information, if and when one is added, before assuming redistribution or reuse rights.
