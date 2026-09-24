# SDR Learning Module — Lesson 0
## Preparing the SDR Development Environment with WSL2 + Ubuntu + GNU Radio

**Course:** Software Defined Radio (SDR)  
**Lesson:** 0 — Environment Preparation  
**Target platform:** Windows 11 + WSL2 + Ubuntu 24.04 LTS  
**GNU Radio:** Install from Ubuntu binary packages  
**Audience:** Beginner-to-intermediate engineer/researcher  
**Goal:** Build a clean GNU Radio environment before studying DSP and real SDR hardware.

---

## 1. Learning Objectives

After completing Lesson 0, you should be able to:

1. Explain what SDR is at a high level.
2. Explain the role of GNU Radio.
3. Explain why WSL2 + Ubuntu is useful for SDR development.
4. Install and verify WSL2.
5. Install and verify Ubuntu.
6. Install GNU Radio from Ubuntu packages.
7. Launch GNU Radio Companion (GRC).
8. Understand the boundary between software-only SDR experiments and real SDR hardware.
9. Know how USB SDR hardware can later be attached to WSL2.

> **Important:** Lesson 0 does not attempt to teach DSP yet. The objective is to create a reliable laboratory environment.

---

# 2. What Is Software Defined Radio?

A traditional radio implements much of the radio chain using dedicated analog/digital hardware.

A simplified traditional receiver is:

```text
Antenna
   ↓
RF Front-End
   ↓
Mixer / Filter
   ↓
Demodulator
   ↓
Data
```

An SDR moves a significant portion of the signal-processing chain into software:

```text
Antenna
   ↓
RF Front-End
   ↓
ADC
   ↓
IQ Samples
   ↓
Software DSP
   ↓
Demodulation
   ↓
Data
```

The central idea is:

> **An SDR represents radio signals as digital samples and processes those samples using software.**

This does not mean that all RF hardware disappears. An SDR still needs RF electronics, ADC/DAC hardware, clocks, interfaces, and often amplifiers/filters.

---

# 3. What Is GNU Radio?

GNU Radio is an open-source framework for building software-defined radio and digital signal-processing systems.

The primary development environment we will use is **GNU Radio Companion (GRC)**.

GRC represents a radio/DSP system as a flowgraph:

```text
[Signal Source]
       ↓
[Filter]
       ↓
[Demodulator]
       ↓
[Data Output]
```

Each block performs a processing operation.

GNU Radio works primarily with streams of digital samples and also supports message-based interfaces for packet/control processing.

The important learning principle is:

> Do not memorize blocks first. Understand the signal flow first.

---

# 4. Why WSL2?

For this course, WSL2 provides a practical Linux environment without requiring a separate Linux installation.

Recommended architecture:

```text
Windows 11
    │
    └── WSL2
          │
          └── Ubuntu 24.04 LTS
                │
                ├── GNU Radio
                ├── Python
                ├── Git
                ├── SoapySDR
                ├── HackRF tools
                └── Other SDR utilities
```

Advantages:

- Linux command-line environment
- easy package management with `apt`
- Python development
- GNU Radio development
- Git and compiler tools
- convenient SDR software ecosystem
- reproducible development environment
- no need to dual-boot Windows/Linux

---

# 5. WSL2 vs Native Windows GNU Radio

| Item | WSL2 + Ubuntu | Native Windows |
|---|---|---|
| Linux tools | Excellent | Limited |
| `apt` packages | Yes | No |
| GNU Radio | Excellent | Available through other distributions |
| Python development | Excellent | Good |
| SDR research workflow | Very good | Good |
| USB SDR hardware | Requires USB/IP setup | Usually direct |
| Linux OOT modules | Excellent | More setup |
| Recommended for this course | **Yes** | Optional |

The goal is not that WSL2 is universally superior. It is that it gives us a Linux-first SDR research environment while keeping Windows as the host OS.

---

# 6. Lesson 0 Architecture

We will use two stages.

### Stage A — Software-only SDR

```text
GNU Radio
   ↓
Virtual Signal
   ↓
DSP
   ↓
Spectrum / Time Display
```

This stage does **not** require an SDR device.

### Stage B — Real SDR hardware

```text
Windows USB
     ↓
usbipd-win
     ↓
WSL2
     ↓
GNU Radio
     ↓
HackRF / RTL-SDR / USRP
     ↓
RF
```

We intentionally start with Stage A.

---

# 7. Step 1 — Check WSL

Open **PowerShell**.

Run:

```powershell
wsl --status
```

Then:

```powershell
wsl --list --verbose
```

Expected example:

```text
NAME      STATE      VERSION
Ubuntu    Running    2
```

The important requirement is:

```text
VERSION = 2
```

If WSL is not installed:

```powershell
wsl --install
```

Restart Windows if requested.

After reboot:

```powershell
wsl --status
```

Then:

```powershell
wsl --list --verbose
```

---

# 8. Step 2 — Install Ubuntu

If Ubuntu is not installed, first see available distributions:

```powershell
wsl --list --online
```

Install Ubuntu:

```powershell
wsl --install -d Ubuntu
```

If you specifically want a distribution version exposed as Ubuntu 24.04 in your environment, use the available distribution name shown by `wsl --list --online`.

After installation, launch Ubuntu.

Create:

- Linux username
- Linux password

The Linux password is separate from the Windows password.

---

# 9. Step 3 — Verify Ubuntu

Inside Ubuntu:

```bash
lsb_release -a
```

Expected:

```text
Distributor ID: Ubuntu
Description: Ubuntu 24.04 LTS
Release: 24.04
```

Check the kernel:

```bash
uname -a
```

Check Python:

```bash
python3 --version
```

Update the package database and installed packages:

```bash
sudo apt update
sudo apt upgrade -y
```

---

# 10. Step 4 — Install GNU Radio

For beginners, use the Ubuntu binary package.

GNU Radio's current Linux installation guidance recommends binary packages for most users. Ubuntu 24.04 is listed with GNU Radio 3.10.9.2 in the current GNU Radio documentation.

Install:

```bash
sudo apt install gnuradio -y
```

Do **not** compile GNU Radio from source for Lesson 0.

Source compilation is useful later when you need development/customization, but it adds unnecessary complexity to the initial learning environment.

---

# 11. Step 5 — Verify GNU Radio

Check GNU Radio Companion:

```bash
gnuradio-companion --version
```

Then check the Python module:

```bash
python3 -c "import gnuradio; print(gnuradio.__version__)"
```

You can also locate the executable:

```bash
which gnuradio-companion
```

Example:

```text
/usr/bin/gnuradio-companion
```

---

# 12. Step 6 — Launch GNU Radio Companion

Run:

```bash
gnuradio-companion
```

GRC should open.

If it opens successfully, Lesson 0 has reached its primary milestone.

You should be able to see the GNU Radio Companion graphical interface and create a new flowgraph.

---

# 13. Step 7 — First Flowgraph

Do not start with a real RF signal.

Create a simple software-only test:

```text
Signal Source
      ↓
   Throttle
      ↓
QT GUI Frequency Sink
```

Suggested parameters:

```text
Sample Rate = 1 MHz
Signal Frequency = 100 kHz
Amplitude = 1
```

The Frequency Sink should show a spectral peak around 100 kHz.

This experiment introduces:

- sample rate
- signal frequency
- amplitude
- FFT
- frequency-domain visualization
- GNU Radio blocks
- stream connections

We will study the mathematics in later lessons.

---

# 14. Step 8 — Understand the First Flowgraph

### Signal Source

Generates a digital signal.

For a sinusoid:

\[
x(t)=A\cos(2\pi f t)
\]

GNU Radio represents the signal as discrete samples.

### Throttle

Controls the processing rate when no hardware source/sink determines the rate.

It is mainly useful in simulation flowgraphs.

### QT GUI Frequency Sink

Displays the signal spectrum using an FFT-based visualization.

Conceptually:

```text
Time-domain samples
        ↓
       FFT
        ↓
Frequency-domain display
```

---

# 15. Real SDR Hardware — Later

When we introduce hardware, the flowgraph becomes:

```text
Antenna
   ↓
SDR Receiver
   ↓
IQ Samples
   ↓
GNU Radio
   ↓
Filter
   ↓
Demodulator
   ↓
Decoder
   ↓
Data
```

For transmission:

```text
Data
 ↓
Encoder
 ↓
Modulator
 ↓
Pulse Shaping
 ↓
IQ Samples
 ↓
SDR Transmitter
 ↓
RF
```

---

# 16. USB SDR Hardware in WSL2

WSL2 does not provide ordinary USB device access in exactly the same way as native Linux.

For compatible systems, Microsoft documents using **usbipd-win** to attach USB devices to WSL2.

Typical workflow:

### PowerShell

```powershell
usbipd list
```

Share the selected device:

```powershell
usbipd bind --busid <BUSID>
```

Attach it to WSL:

```powershell
usbipd attach --wsl --busid <BUSID>
```

### Ubuntu

```bash
lsusb
```

The SDR should then appear as a USB device.

When finished:

```powershell
usbipd detach --busid <BUSID>
```

Do not configure this until the software-only GNU Radio environment is working.

---

# 17. Recommended SDR Software Stack

Our eventual environment:

```text
                 SDR APPLICATION
                       │
                 GNU Radio
                       │
             ┌─────────┴─────────┐
             │                   │
         SoapySDR              UHD
             │                   │
      ┌──────┴──────┐       USRP devices
      │             │
   HackRF       RTL-SDR
```

Other tools will be introduced when needed.

---

# 18. Troubleshooting

## Problem A — `wsl` is not recognized

Open PowerShell as Administrator and check Windows updates.

Then:

```powershell
wsl --install
```

Restart Windows if requested.

---

## Problem B — Ubuntu is WSL1

Check:

```powershell
wsl --list --verbose
```

Convert it:

```powershell
wsl --set-version Ubuntu 2
```

Verify again:

```powershell
wsl --list --verbose
```

---

## Problem C — GNU Radio package not found

Run:

```bash
sudo apt update
```

Then:

```bash
apt search gnuradio
```

Try:

```bash
sudo apt install gnuradio
```

Avoid immediately switching to a source build.

---

## Problem D — `gnuradio-companion` cannot start

Check:

```bash
gnuradio-companion --version
```

Then:

```bash
python3 -c "import gnuradio; print(gnuradio.__version__)"
```

If Python imports correctly but the GUI does not start, capture the terminal error. The error message is more useful than reinstalling everything.

---

## Problem E — GUI problems under WSL

WSL2 supports Linux GUI applications through WSLg on supported Windows configurations.

First test whether a simple Linux GUI application works.

Do not modify the GNU Radio installation until the actual error is identified.

---

# 19. Lesson 0 Completion Checklist

Mark each item when complete:

```text
[ ] WSL2 installed
[ ] Ubuntu installed
[ ] Ubuntu is running as WSL2
[ ] Ubuntu version checked
[ ] Linux kernel checked
[ ] Python 3 checked
[ ] apt updated
[ ] GNU Radio installed
[ ] GNU Radio version verified
[ ] GNU Radio Python module verified
[ ] GNU Radio Companion launched
[ ] First software-only flowgraph created
[ ] Frequency Sink displays a 100 kHz tone
```

---

# 20. What You Should Understand Before Lesson 1

You should be able to explain:

### What is SDR?

> A radio architecture in which significant signal-processing functions are implemented digitally/software-based rather than entirely with dedicated analog hardware.

### What is GNU Radio?

> A framework for constructing DSP and SDR processing systems using connected processing blocks.

### What is GNU Radio Companion?

> A graphical flowgraph development environment for GNU Radio.

### Why WSL2?

> It provides a Linux environment suitable for GNU Radio and SDR development while retaining Windows as the host operating system.

### What is an IQ sample?

We will answer this rigorously in **Lesson 1/2**. Do not worry if it is still unclear.

---

# 21. Lesson 1 Preview

Next we will study:

## GNU Radio Fundamentals

Topics:

1. Flowgraph
2. Block
3. Input/output ports
4. Stream
5. Vector
6. Message
7. Data types
8. Sample rate
9. Complex numbers
10. IQ representation
11. Source and Sink
12. Runtime
13. Connections

Then we will build increasingly complex flowgraphs.

---

# 22. References

### GNU Radio

GNU Radio — Linux Installation:
https://wiki.gnuradio.org/index.php/LinuxInstall

GNU Radio — Installing GNU Radio:
https://wiki.gnuradio.org/index.php/InstallingGR

GNU Radio — Ubuntu Installation:
https://wiki.gnuradio.org/index.php/UbuntuInstall

### Microsoft WSL

WSL installation:
https://learn.microsoft.com/windows/wsl/install

WSL basic commands:
https://learn.microsoft.com/en-us/windows/wsl/basic-commands

Connecting USB devices to WSL:
https://learn.microsoft.com/en-us/windows/wsl/connect-usb

---

## Instructor Note

This course should progress from **concept → mathematics → GNU Radio implementation → measurement → real hardware**.

Do not use GNU Radio as a black box.

Every flowgraph should eventually be explainable mathematically and physically:

```text
Physical RF
     ↕
Mathematical model
     ↕
Digital samples
     ↕
GNU Radio blocks
     ↕
Measured result
```

That is the foundation for using SDR professionally in communications, satellite systems, LoRa, AIS, and other RF applications.
