# Hak5 Bash Bunny Mark II – HID Injection Attack Scripts & Presentation

## EDUCATIONAL & ACADEMIC USE ONLY

This repository contains strictly educational materials, proof-of-concept scripts, and documentation created exclusively for academic assessment. These scripts demonstrate keystroke injection attack vectors (HID spoofing, automated data exfiltration, dynamic URL manipulation, and local Denial of Service) using the Hak5 Bash Bunny Mark II hardware platform. They are intended solely for authorized security testing, academic study, and defensive research. Any use of these tools outside of a controlled, authorized laboratory environment is strictly prohibited. The author does not condone illegal activity.

## Motivation

I decided to choose this project because I wanted to bridge the gap between physical security and cyber defense. While traditional network firewalls and software protections are thoroughly researched, hardware-based trust mechanisms often present a critical blind spot. Using the Hak5 Bash Bunny Mark II provided a hands-on opportunity to explore how systems implicitly trust physical human interface devices (HID). This project allowed me to practically analyze how rapid keystroke automation can bypass standard operating system defenses before a human user can even react.

## What I Learned

Working on this project provided me with a deep, practical understanding of physical endpoint security and OS trust architectures. Key takeaways include:
* **Hardware Trust Exploitation:** I learned how modern operating systems handle Plug and Play architectures and how easily they can be manipulated into trusting a rogue device masquerading as a legitimate system keyboard.
* **Composite Device Simulation:** I gained hands-on experience working with composite hardware modes (`ATTACKMODE`), learning how a single physical device can simultaneously act as a high-speed keyboard (HID) and an unauthenticated flash drive (STORAGE) to steal data in seconds.
* **Advanced Scripting & Logic Control:** Operating within the embedded Linux environment of the Bash Bunny taught me how to combine native Linux Bash features (arrays, random functions, loops) with rapid DuckyScript syntax (`QUACK`) to create adaptive, highly dynamic payloads.
* **Endpoint Hardening Strategies:** Witnessing the devastating speed of keystroke injection highlighted exactly why proactive endpoint protection is vital. I now clearly understand the practical importance of GPO Device ID restrictions, PowerShell Constrained Language Mode, and advanced behavioral EDR analysis for detecting non-human typing speeds.

## Overview

The Bash Bunny Mark II is a versatile, pocket-sized hardware attack platform by Hak5. It sits physically inline within an available USB port of a target machine. This project provides a collection of attack payloads and a presentation demonstrating various offensive automation and defensive mitigation techniques.

All scripts are designed to run on the Bash Bunny Mark II (Debian-based Linux, Quad-Core ARM Cortex-A7 @ 1.3 GHz, 8 GB NAND SSD).

## Repository Structure

.
├── README.md                              # This file
├── Scripts/                               # Directory containing all attack payloads
│   ├── Kopiowanie Plików/                 # Hybrid HID + STORAGE automated data theft
│   ├── Otwieranie stron/                  # Dynamic Bash array loop URL spamming (PG portals)
│   └── Pisanie w notatniku/               # Basic keyboard emulation & text input in Notepad
└── Demonstration
    └── Ataki HID Injection.pdf            # Presentation slides (Polish)

## Attack Scenarios

| # | Name | Type | ATTACKMODE | Description |
|---|---|---|---|---|
| 1 | Keystroke Injection | Basic HID | `HID` | Emulates human keyboard to open notepad and type text |
| 2 | Data Exfiltration | Active Theft | `HID STORAGE` | Opens CMD, copies specific folders to Bunny's SSD silently |
| 3 | Dynamic URL Injection | Local DoS | `HID` | Continuous Bash loop randomly opening web browsers with pre-defined target URLs |

## Key Security Lessons

* **Implicit Trust** – Operating systems trust physical USB connections by default, leaving them open to HID injection.
* **Speed Over Match** – Automated scripts can type at over 10,000 characters per minute, rendering human reaction times irrelevant.
* **EDR Behavioral Analysis** – Traditional antivirus scanners fail against HID attacks; detection relies on EDR process spawning and typing anomalies.
* **ASR & Hardening** – Restricting allowed Device IDs (VID/PID) and enabling PowerShell Constrained Language Mode drastically shrinks the attack surface.
* **Physical Isolation** – Mechanical USB port blockers and enforcing strict workstation locking (`Win + L`) habits are critical starting points.

## Usage

1. Flip the Bash Bunny physical switch to **Arming Mode** (position closest to the USB connector).
2. Connect the Bash Bunny to your configuration computer.
3. Access the storage drive and upload the desired script to `/payloads/switch1/payload.txt` (or `switch2`).
4. Eject the device safely and flip the switch to the corresponding position.
5. Plug the device into the target computer's USB port.
6. Observe the RGB LED indicators: **SETUP** (Flashing/Solid) $\rightarrow$ **ATTACK** (Active injection) $\rightarrow$ **FINISH/SUCCESS** (Safe to unplug).

## Disclaimer

These materials are provided for educational and authorized security testing purposes only. Unauthorized use of these techniques against systems you do not own or have explicit permission to test is illegal. The author and the Gdańsk University of Technology assume no liability for misuse.

## References

* Hak5 Bash Bunny Documentation
* **Authors:** Jakub Bugnacki, Jakub Rachubka, Bartłomiej Chociaj
