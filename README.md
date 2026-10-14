# 🐧 Tanix TX6-H (Allwinner H6) — The Ultimate Mainline Linux Guide

> A complete, battle-tested, step-by-step guide to installing a mainline Linux OS (**Armbian / Debian**) directly to the internal memory (eMMC) of the **Tanix TX6-H Android TV Box** — and turning a ₹300 "broken" e-waste box into a working desktop PC.
>
> This guide was **not** written from a clean room. It was built from real-world troubleshooting — the hardware crashes, the mystery freezes, the setup-menu bugs, and the dead ends that actually happen when you install Linux on this exact device. Every screenshot below is from the real machine.

[![Armbian](https://img.shields.io/badge/Armbian-fc8db5?logo=armbian&logoColor=white)](https://www.armbian.com/)
[![Debian](https://img.shields.io/badge/OS-Debian%20%2B%20GNOME-A80000?logo=debian&logoColor=white)](https://www.debian.org/)
[![Allwinner H6](https://img.shields.io/badge/SoC-Allwinner%20H6-orange?style=flat-square)](https://wiki.librecomputing.org/Allwinner_H6)
[![Mainline Kernel](https://img.shields.io/badge/kernel-mainline-4F5D95)](https://kernel.org/)
[![No hardware mod](https://img.shields.io/badge/hardware%20modification-NONE-success)](https://github.com/topics/tanix)
[![License](https://img.shields.io/badge/License-MIT-blue)](LICENSE)
[![Made with ❤️](https://img.shields.io/badge/made%20with-%E2%9D%A4%20by%20Ashwin-ff69b4)](https://github.com/topics/tanix)

---

## 📸 The Machine

<p align="center">
  <img src="assets/tx6h-outside.jpg" alt="The Tanix TX6-H Android TV box, black case with WiFi antenna, HDMI and power cable connected" width="420">
</p>

That's it. A black plastic box the size of a deck of cards, with a WiFi antenna and two cables. The seller called it **dead**. It cost **₹300**.

Six hours later it is a **fully working Linux desktop computer**.

### 🔍 Inside — and no hardware modification required

<p align="center">
  <img src="assets/board-teardown.jpg" alt="Opened Tanix TX6-H showing the Allwinner H6 PCB, WiFi antenna, heatsink, HDMI, Ethernet, two USB ports and microSD slot" width="760">
</p>

| What you see | Why it matters |
|---|---|
| **Allwinner H6** SoC (under the aluminium heatsink) | Quad-core ARM Cortex-A53 — the brain of the whole thing |
| **WiFi antenna** (2.4 GHz) + PCIe module | ⚠️ **2.4 GHz only** — this causes the #1 setup problem, see [Phase 4](#-phase-4--network-connection-the-5-ghz-bug) |
| **HDMI port** | Your monitor's life line |
| **2× USB ports** | Keyboard, mouse, USB stick for offline updates |
| **Ethernet port** | A wired fallback if WiFi gives you grief |
| **MicroSD slot** | Where the bootable Linux image lives |
| **No shorting, no soldering, no eMMC pin work** | ✅ **This whole conversion is 100% software.** The box is opened only to satisfy curiosity. |

> ✅ **Nothing inside was modified.** No "short the eMMC pins" trick, no UART header soldering, no pinout risk. The TX6-H's boot ROM is fully unlocked by design — that's what makes this guide work at all.

---

## 📖 My Story — Why This Guide Exists

My family shares **one old, slow laptop**. My father uses it for his office work during the day, so there was never a second machine for my sister to study on.

Meanwhile, I was scrolling a second-hand marketplace when I found a listing for a **Tanix TX6-H Android TV Box for ₹300**. The seller said *"it doesn't work"* — and to prove it, they handed over **only the bare box**. No remote. No power adapter. Not even the original packaging.

That was exactly the deal I wanted.

### The "broken" box that wasn't

- ❌ **12V adapter** (the one the seller implied): dead on arrival
- ❌ **Spare 12V adapter** I already had at home: plugged it in → **completely dead**
- 🔎 Then I actually *looked* at the box and found a small **5V label** printed in the fine print on the underside
- ✅ Bought a cheap **5V adapter** → **it booted straight into Android**

> 💡 **The single most important 60-second check in this entire guide:** look for the voltage label on the box *before* you assume the device is dead. These TX6-H units expect **5V**, not the 12V most people assume from "TV box." A completely dead box that still has a working Android on it = **wrong power supply, not broken hardware.**

I got a working Android TV box for the price of a pizza. Then I did something slightly unreasonable: **I installed a full desktop Linux on it and turned it into my sister's study PC.**

---

## 💸 Why This Is Worth Doing

| | 📱 The old shared laptop | 🐧 The TX6-H Linux PC |
|---|---|---|
| **Cost** | Already paid for — and shared | **₹300** (₹250 box + ₹50 5V adapter) |
| **Availability** | Booked around dad's office hours | **Always free, anytime** |
| **OS** | Windows, bloatware, forced updates, ads | Clean Debian + GNOME — **no ads, no telemetry** |
| **Updates** | Slow, breaks over time | `apt update` and forget about it |
| **Distraction** | Tabs, notifications, Edge pop-ups | Task switching, multiple real windows |
| **Longevity** | Years old, thermal throttling | Runs **cool and completely silent** |
| **Repairability** | Effectively none | Full Linux: `apt`, logs, shell, `dmesg` |

The result: a real, honest, affordable desktop computer built from **discarded Android hardware** — for a student who needed one.

---

## 📊 Verified Specs (read straight off the running machine)

These are not marketing numbers. These are what `System Monitor` actually reported on the box after the install completed.

| Spec | Actual | Notes |
|---|---|---|
| **CPU** | **4 cores** | Allwinner H6, quad-core ARM Cortex-A53 |
| **RAM** | **3.1 GB** (~2.90 GB usable) | Enough for GNOME + a browser + LibreOffice |
| **Internal storage (eMMC)** | **60 GB ext4** | Linux root filesystem after install |
| **Boot partition** | **534.8 MB vfat** | Separate `/boot`, 28% used |
| **MicroSD card used** | **62 GB** (M0S002) | Only needed for the *initial* install |
| **WiFi** | 2.4 GHz only | No 5 GHz support |
| **Desktop installed** | **GNOME** (Wayland session) | Running via GDM3 |
| **Display server** | **Wayland** (via `gdm-wayland-session`) | Hardware-accelerated where drivers allow |

---

## 📦 What You'll Need

- [ ] **Tanix TX6-H** box (Allwinner H6) — the "dead" ₹300 find
- [ ] **5V power adapter** ⚠️ *not 12V — check the label on the box first*
- [ ] **MicroSD card** — 16 GB minimum, 32 GB+ recommended, Class 10 / A1
- [ ] **MicroSD card reader**
- [ ] **HDMI cable**
- [ ] **USB keyboard** (strongly recommended for first boot)
- [ ] **A monitor or TV** with a free HDMI port
- [ ] **Balena Etcher** (to write the boot image)
- [ ] A PC with internet — ideally a **USB stick** so you can carry updates to the box

### 📸 Getting the correct image

![Finding the correct Tanix TX6-H Armbian release on the Ophub releases page](assets/download-armbian-release.png)

<p align="center"><sub>👉 Search the release assets for <code>tanix-tx6</code> — download the <code>allwinner_tanix-tx6</code> image.</sub></p>

---

## 🚀 Phase 1 — Download & Flash

1. Go to the **[Ophub Amlogic-s9xxx-Armbian Releases](https://github.com/ophub/amlogic-s9xxx-armbian/releases)** page.
2. Take the **latest** release and scroll to the bottom of the release notes.
3. Click **"Show all assets"**.
4. Search the page for **`tanix-tx6`**.
5. Download the matching image — e.g. `Armbian_..._allwinner_tanix-tx6_...img.gz`.
6. Download and install **[Balena Etcher](https://etcher.balena.io/)**.
7. Insert your MicroSD card.
8. **Flash the `.img.gz` straight to the SD card** — Etcher extracts it automatically. There is no need to gunzip it yourself.

### 📸 Flashing with Balena Etcher

![Selecting the Armbian image and MicroSD card in Balena Etcher and clicking Flash](assets/flash-balena-etcher.png)

> ⚠️ **Do NOT install a separate U-Boot for the TX6-H.** This is the mistake that cost me the most time — see [Troubleshooting](#-troubleshooting-the-mistakes-that-cost-me-days). The TX6-H's bootloader is **already inside the Armbian image**. Flashing a TX6 (non-H) U-Boot onto a TX6-H **will** give you a black screen and zero output.

---

## 🖥️ Phase 2 — First Boot & User Creation

1. **Unplug** the power cable from the TV Box.
2. Insert the freshly flashed MicroSD card into the TX6-H.
3. Connect the **HDMI cable** and a **USB keyboard**.
4. Plug the power back in.
5. ⏱️ **The Hardware Check:** within about **5 seconds**, the front green LED clock should light up. **This proves the embedded U-Boot initialised your RAM successfully** — it is the single best sign that everything is working.
6. Wait for the boot text to stop scrolling.

Now log in and set up the system:

### Initial login

```
Username: root
Password: 1234      (or: armbian)
```

> 🔒 **Passwords are invisible as you type.** No dots, no asterisks, no cursor movement. This is normal. Type it and press Enter — do not try to "fix" it.

### Creating the everyday user account

The system will ask you to create a normal user account. This becomes the `~/home/<yourname>` folder you'll use every day.

> 🚨 **THE CAPITAL LETTER BUG**
> You **must** use **lowercase letters only** — e.g. `ashwin`.
> If you type a capital letter like `Ashwin`, Linux throws an **`illegal characters in username`** error and **aborts the whole setup**. When it aborts, you must start user creation again from scratch.

---

## 🌍 Phase 3 — Time, Location & Locales

Immediately after creating your user, the system asks for language and location.

### The Massive List of Numbers (Locales)

The screen floods with an enormous list of **numbered choices (1 to 300+)**. It is waiting for you to **type a number and press Enter**.

| What you want | Type |
|---|---|
| 🇺🇸 **US English (`en_US.UTF-8`)** | `98` → Enter |
| ⏭️ **Skip it entirely** | `337` → Enter, or press `Ctrl+C` |

### Setting the timezone

After the wizard drops you to the shell (`root@armbian:~#`):

```bash
sudo armbian-config
```

Navigate to: **Personal → Timezone** → pick your exact region and city to sync the system clock.

> 🌏 **Getting the timezone wrong is sneakier than it sounds.** A wrong clock breaks TLS certificate validation, which makes `apt update` fail with confusing certificate errors that look exactly like "broken internet" but are actually just "the box thinks it's 2019." If `apt` suddenly cannot reach anything, **check your clock first.**

---

## 🛜 Phase 4 — Network Connection (The 5 GHz Bug)

Before we can download a desktop UI, the box needs internet. Stay inside `armbian-config` and go to **Network → WiFi**.

> 🚨 **THE 5 GHz BUG**
> The Wi-Fi chip inside the **TX6-H does not support 5 GHz networks** — confirmed in the teardown photo above.
>
> If you are on a phone hotspot or a dual-band router, and **your network simply does not appear in the list**, the fix is almost always: **change your router / phone AP band to 2.4 GHz**.
>
> This is the most common "the internet is broken" moment with this device. Nothing is broken. The radio just cannot see 5 GHz.

Select your **2.4 GHz** network, enter the password, and press `Esc` until you're back at the command prompt.

---

## 🛑 Phase 5 — The eMMC Freeze Fix (⚠️ CRITICAL & DESTRUCTIVE)

To install Linux permanently, we must run `armbian-install`.

But on the TX6-H, the Linux storage driver frequently **crashes while trying to read the factory Android partition table**. The installer then hangs **forever** at:

```
[ INFO ] Start backing up the default bootloader.
```

…and never progresses. It is not downloading. It is not slow. **It is wedged.**

The fix is to **force-clear the Android partition layout by hand** before running the installer.

> 🔴 **⚠️ STOP — READ THIS FIRST**
> **This step permanently erases the internal eMMC storage of the box.**
> Everything currently on it — **your Android installation, all apps, settings and stored files — will be destroyed and is NOT recoverable.**
>
> ✅ Before typing the command, make sure you have **nothing you care about** on the TV box, and that your **bootable MicroSD card is still inserted** — after this step the box can no longer boot Android.
>
> **If you only want to try Linux without risking anything, stop after Phase 4.** The box runs perfectly well from the SD card with zero commitment.

### Wipe the Android layout

At the `root@armbian:~#` prompt, type this **exact** command:

```bash
dd if=/dev/zero of=/dev/mmcblk2 bs=1M count=100
```

Press Enter. It takes 1–3 seconds and prints a scrolling wall of dots ending in `100+0 records in`. Your internal memory is now wiped.

### Common typos — learn from my mistakes

| ❌ Wrong | ✅ Right | Why it fails |
|---|---|---|
| `if=dev/zero` | `if=/dev/zero` | **Missing slash** → `No such file or directory` |
| `dd if=/dev/zero of=/dev/mmcblk2 bs=1M` | `... bs=1M count=100` | **Missing `count=100`** → invalid number argument |
| `of=/dev/mmcblk1` | `of=/dev/mmcblk2` | **Wrong device** → wipes the wrong storage |

> 💡 **Sanity check:** a correctly typed command returns in a few seconds and dumps a wall of dots plus `100+0 records in`. If it returns **instantly with an error**, you have a typo — check the table above.
>
> ✅ **Why `/dev/mmcblk2` and not `mmcblk0`?** On the TX6-H, `mmcblk0` is the MicroSD card and `mmcblk1` is the WiFi/SDIO device. The **internal eMMC is `mmcblk2`** — confirmed by the `[eMMC]` partitions shown in the [verification screenshot](#-phase-8--proof-it-worked).

---

## 📥 Phase 6 — Install to Internal Memory

1. With the eMMC now wiped, launch the installer:

   ```bash
   armbian-install
   ```

2. Select the option to **Boot from eMMC — system on eMMC**.
3. Choose **`ext4`** as the filesystem format.
4. **This time it will safely pass the formatting stage.** Let it copy the OS — this takes **3 to 10 minutes** depending on your SD card and USB media.
5. When it returns to the prompt, shut down cleanly:

   ```bash
   poweroff
   ```

   > ⚠️ **Do not just yank the power.** Wait for the activity LED to stop and the HDMI signal to drop. Pulling power mid-copy is how you brick the eMMC.

6. **Unplug the power.**
7. **Completely remove the MicroSD card.**
8. **Plug the power back in.**

🎉 **Your TV box is now booting Linux directly from internal memory.** Log in with your `root` credentials.

---

## 🖥️ Phase 7 — Installing the Desktop UI

You are now on a command-line server. Time for a graphical desktop.

### Step 1 — Update package lists (do NOT skip)

```bash
apt update
```

> 🚨 If you try to install a desktop immediately you will get a wall of **`Unable to locate package`** errors. You **must** refresh the app directory first. This is the most common Phase-7 error.

### Step 2 — Install the desktop environment

**Option A — GNOME** *(this is what my machine actually runs — see screenshot below)*

```bash
apt install gnome-core xorg gdm3 -y
```

**Option B — XFCE** *(lighter — more free RAM for a browser-heavy workflow)*

```bash
apt install xfce4 xfce4-goodies xorg lightdm -y
```

### Step 3 — Choose the display manager

If a **blue screen** appears asking you to pick a display manager:

| Desktop you installed | Choose |
|---|---|
| GNOME | `gdm3` |
| XFCE | `lightdm` |

### Step 4 — Wait, then reboot

The download and install takes **10–20 minutes**. Be patient.

```bash
reboot
```

### 🎉 The result

<p align="center">
  <img src="assets/debian-home.png" alt="The Debian GNOME desktop running on the Tanix TX6-H, with WiFi, volume and power indicators in the top bar" width="820">
</p>

**A fully working Debian + GNOME desktop, running on a box from a second-hand marketplace.**

Network, sound, power menu — all present in the top-right corner. A real computer.

---

## 📈 Performance Expectations (Measured, Not Guessed)

The H6 is a 2016-era quad-core A53. Here is what you should **actually** expect on this specific box, with 4 cores and 3.1 GB of RAM:

| Task | Expectation |
|---|---|
| GNOME desktop, window switching, menus | ✅ Solid — my screenshots are all live GNOME |
| Web browser (5–8 tabs, moderate sites) | ✅ Usable with a lightweight browser |
| LibreOffice (Docs, Sheets, PDF export) | ✅ Smooth |
| Coding / terminal / `git` / VS Code (OSS build) | ✅ **Genuinely great** — far better than most old laptops |
| YouTube playback | ✅ 720p comfortable; 1080p depends on codec/driver |
| Zoom / Google Meet | ⚠️ Workable at 480p–720p — it is CPU-bound |
| Modern games | ❌ Not realistic. This is a study machine, not a gaming rig |
| Video editing | ❌ Not practical on 4 A53 cores |

**What the monitoring showed on a real session:**

| Metric | Observed | Reading |
|---|---|---|
| Memory usage, GNOME + apps open | **1.5 GB of 3.1 GB (~49%)** | Comfortable headroom — you can keep several apps open |
| Memory, bare terminal session | **1.35 GB of 2.90 GB** | The desktop itself costs ~1 GB |
| `gnome-shell` process | **~173 MB RAM** | Reasonable for GNOME on this hardware |
| Swap | **Not available** | ⚠️ **Disable heavy browser tabs — there is no swap** |
| Storage used after a full install | **4.2 GB of 60 GB (6%)** | Tons of room for documents and projects |

> ⚠️ **⚠️ Important: there is no swap partition.** If you open too many browser tabs, GNOME will simply freeze rather than swapping to disk. Keep it to **5–8 tabs** max and use `htop` to watch your RAM.

> 💡 **Tips that genuinely helped:**
> - Prefer **GNOME on Wayland** (the default here) — it is smoother on the H6 than forcing X11.
> - Run **Extensions** → disable animations for an instant perceived speedup.
> - Add a lightweight browser (`falkon` or `epiphany`) instead of Chromium if RAM gets tight.
> - `htop` is already installed and is the best tool for spotting what's eating your RAM.

---

## 📸 Phase 8 — Proof It Worked

This is the part most guides skip. **Here is the actual evidence** that the eMMC install completed and the machine is running natively — no SD card in the slot.

### 💾 The eMMC is really mounted

<p align="center">
  <img src="assets/file-system.png" alt="GNOME System Monitor File Systems view showing a 60 GB ext4 root filesystem at 6% used and a 534.8 MB vfat boot partition at 28% used" width="560">
</p>

| Device | Directory | Type | Total | Available | Used |
|---|---|---|---|---|---|
| `/dev/mmblk2` | `/` | **ext4** | **60.0 GB** | 55.8 GB | 4.2 GB (6%) |
| `/dev/mmblk2` | `/boot` | **vfat** | **534.8 MB** | 382.0 MB | 152.8 MB (28%) |

✅ **This is the exact layout `armbian-install` creates on eMMC.** A separate FAT boot partition plus a large ext4 root — and it is reading from `/dev/mmcblk2`, the internal storage. **The SD card is not involved.** Android is gone; Linux owns the box.

### 🌐 The whole system, alive at once

<p align="center">
  <img src="assets/system-log.png" alt="GNOME System Monitor and Logs showing four CPU cores at full load, 1.5 GB of 3.1 GB memory used, the eMMC BOOT_EMMC and ROOTFS_EMMC partitions, Debian NTP log entries and htop in a terminal" width="900">
</p>

Everything a working system shows, all visible at once:

- **4 CPU graphs** — CPU1 through CPU4 all tracked and responding
- **Memory and Swap** — `1.5 GB (49.1%) of 3.1 GB`, Cache 1.3 GB
- **Network** — interface up and accounting bytes
- **Disk** — live read/write graph
- **Devices & Locations** — `Home Folder /home/ashwin`, root `armbian /` with **55.8 GB available of 60.0 GB**
- **SD Card Reader** — your 62 GB M0S002 card, showing `Free Space · BOOT_EMMC (536 MB FAT) · ROOTFS_EMMC (61 GB ext4)`
- **Logs** — live `systemd` service activations, Debian mirror contact, no boot errors
- **Terminal with `htop`** — `Load average: 2.39 1.77 1.01` · `Uptime: 00:09:40` · `Mem: 1.35G/2.90G`

### ⚙️ Real process list

<p align="center">
  <img src="assets/system-processes.png" alt="GNOME System Monitor Processes tab listing gnome-shell, gdm-wayland-session, pipewire, wireplumber, dbus-daemon and systemd processes with CPU and memory usage" width="500">
</p>

This is the process table that proves the desktop stack is fully alive:

- **`gnome-shell`** (PID 1528, 8.0% CPU, 172.8 MB) — the graphical session is rendering
- **`gdm-wayland-session`** (PID 1423) — ✅ **it booted into Wayland**, the modern path
- **`wireplumber` + `pipewire` + `pipewire-pulse`** (PIDs 1398 / 1396 / 1399) — audio stack fully up
- **`dbus-daemon`**, **`systemd`**, **`at-spi-bus-launcher`** — core services healthy
- **A full GNOME service tree** — `gnome-session-binary`, `evolution-calendar-factory`, `goa-daemon`, `gsd-media-keys`, `gsd-power`, `gcr-ssh-agent`, and the full accessibility stack

> ✅ **Every single core service is present and consuming sane amounts of memory.** Nothing is crash-looping. This is a healthy Linux system, not a demo.

---

## 🔧 Troubleshooting — The Mistakes That Cost Me Days

This is the part no other guide writes down. Every one of these actually happened to me.

### 🧊 "It boots Android but shows nothing after I plug in the SD card"

**Symptom:** With the SD card inserted, no HDMI output — just a black screen. **Remove the SD card → Android boots perfectly.**

**Diagnosis (confirmed):** The **installation method was wrong, not the hardware.** The box was never broken.

**What I tried first, and why it failed:**

1. ❌ **Armbian Imager** (`imager.armbian.com`) from a YouTube video → flashed SD, plugged in, **black screen**
2. ❌ **Pressing RESET after power-up** (a suggestion from a forum post) → held the button, **still no display**
3. ❌ **Remove SD → Android boots** → proof the hardware is 100% fine
4. ❌ **U-Boot method** — flashed a separate U-Boot as well → **still black screen**
5. 🔎 **Realised I was flashing a `tx6` U-Boot onto a `tx6h`** — wrong hardware revision entirely
6. 🔎 **Searched again** → the community was explicit: *"the TX6-H needs no separate U-Boot — it is already inside the Armbian image itself"*
7. ✅ **Reflashed the SD card with the Ophub image alone** → **booted on the first try**

**The fix:** Don't use the Armbian Imager. Don't flash any external U-Boot. **Flash the Ophub `allwinner_tanix-tx6` image with Balena Etcher and stop.**

### ⛔ "I flashed a separate U-Boot for the TX6 (not the H)"

**Symptom:** Still black screen, still no output.

**Diagnosis:** **TX6 and TX6-H are different hardware revisions.** A TX6 U-Boot flashed onto a TX6-H **will not work**, and the TX6-H U-Boot simply **isn't published separately.**

**Fix:** Reflash the SD card with the Ophub image. Nothing else.

### 🧊 "armbian-install hangs forever at `Start backing up the default bootloader`"

**Diagnosis:** The installer is wedged reading the factory Android partition table.

**Fix:** Go to [Phase 5](#-phase-5--the-emmc-freeze-fix-️-critical--destructive) and wipe it first.

### 📶 "My 5 GHz network doesn't appear in the WiFi list"

**Fix:** Switch the router or hotspot to **2.4 GHz**. The TX6-H Wi-Fi chip is 2.4 GHz only.

### 🖥️ "Black screen, but the front LED clock lit up"

**Good news:** the LED clock means **U-Boot initialised your RAM successfully.** The hardware is fine — the problem is downstream. Usually a wrong image variant. Press `Ctrl+Alt+F2` to get a TTY and log in there.

### 🧑 "illegal characters in username"

**Fix:** Type your username in **lowercase only.**

### 📦 "Unable to locate package" when installing the desktop

**Fix:** Run `apt update` first. Always.

### ⏰ "`apt update` fails with certificate errors"

**Fix:** Your **system clock is wrong.** Go back to [Phase 3](#-phase-3--time-location--locales) and set the timezone properly.

### 🐌 "The desktop is sluggish and the box keeps freezing"

**Fix:** You have **no swap.** Check RAM in `htop`, close browser tabs, and disable GNOME animations in **Settings → Appearance**.

### 💀 "I pulled the power during `armbian-install`"

**Symptom:** Dead boot.

**Fix:** Reflash the MicroSD with the known-good Ophub image and **redo the install** — this time let `poweroff` finish on its own. This is exactly why Phase 6 step 5 exists.

---

## 🔌 Quick Reference — Copy/Paste Cheatsheet

```bash
# Wi-Fi + timezone + general system setup
sudo armbian-config

# Refresh package lists (ALWAYS before installing anything)
apt update

# Full system upgrade
apt upgrade -y

# Install a desktop (GNOME — what this machine runs)
apt install gnome-core xorg gdm3 -y

# Install a lighter desktop (XFCE)
apt install xfce4 xfce4-goodies xorg lightdm -y

# Watch CPU / RAM / load in real time
htop

# ⚠️ DESTRUCTIVE — wipes internal eMMC (Android is gone forever)
dd if=/dev/zero of=/dev/mmcblk2 bs=1M count=100

# Install to internal memory
armbian-install

# Clean shutdown (never yank the power)
poweroff

# Reboot
reboot
```

---

## 🛡️ Important Disclaimers

- ⚠️ **You will void any remaining warranty.** These are cheap, obsolete Android TV boxes — there is usually nothing left to void.
- ⚠️ **Phase 5 is destructive.** It erases internal memory completely. Back up anything you care about first.
- ⚠️ **There is no swap.** RAM is 3.1 GB. Heavy browser use will freeze the desktop rather than slow it.
- ⚠️ **Video acceleration is immature.** Mainline GPU support for the Allwinner H6 is incomplete — this is a study and productivity machine, not a media center.
- ⚠️ **YouTube may stutter at 1080p.** Hardware decode is unreliable on mainline.
- ✅ **Nothing was modified in hardware.** No soldering, no pin shorts, no eMMC pinout work. It is 100% a software conversion.
- ✅ **Everything here was actually done.** Not theoretical, not copied. This is a transcript of a real install — including the mistakes and the dead ends.

---

## 🙏 Thank You — The Armbian Community

This project **would not exist** without the open-source Linux community. A genuine, heartfelt thank you to:

- **🌐 The [Armbian](https://www.armbian.com/) project** — for making a Debian/Ubuntu-based Linux for ARM hardware possible at all, and for making it free for everyone.
- **👤 [ophub](https://github.com/ophub)** and the maintainers of **amlogic-s9xxx-armbian** — for maintaining the prebuilt, continuously updated images that made this install possible without hand-assembling a kernel and a bootloader. That work saves ordinary people *weeks*.
- **💬 Every person on the Armbian forums, r/linux, and the TX6/TX6-H community threads** who answered a beginner's obvious question with patience — and who documented the eMMC freeze bug, the 5 GHz limitation, and the **TX6 vs TX6-H U-Boot trap** before I ever hit them. I found those posts at 2 AM and they saved me an entire weekend.
- **🔧 Everyone who maintains mainline Linux support for Allwinner / Amlogic hardware** — often unpaid, often in their spare time.
- **👩‍💻 My sister** — who was patient with the reboots, and who now has a computer of her own because of this.

> 🙏 **If this guide saved you time, star the repo, share it, and — if you can — contribute what *you* learned.** Someone else's 2 AM forum post is the only reason any of this works. The next beginner you help will never know your name, and that is exactly the point.

---

## 📌 License

**MIT License** — use it, share it, translate it, teach from it. Just keep the credit and don't sell it.

**Tanix**, **Armbian**, **Ophub** and **Debian** are the properties of their respective owners. This repo is an independent, community-written guide — **not** affiliated with or endorsed by Tanix or Armbian.

---

## 🔖 Keywords & Hashtags

Copy-paste these when sharing on LinkedIn, X, Reddit, or Facebook:

```
#TanixTX6 #TX6H #AllwinnerH6 #Armbian #ArmbianLinux #Ophub #MainlineLinux
#Amlogic #AndroidTVBox #LinuxOnTVBox #LinuxOnAndroidBox #Debian #Ubuntu
#GNOME #Wayland #GDM #LinuxDesktop #Linux #LinuxTips #LinuxForHackers
#TanixTX6H #ConvertAndroidBoxToLinux #NoMoreAndroid #FreeSoftware
#SelfHosted #Homelab #DiyTech #DiyComputer #BudgetPC #AffordablePC
#StudentPC #StudySetup #LearnLinux #CodingSetup #OpenSource #LinuxCoding
#SBC #SingleBoardComputer #HardwareHacking #EmbeddedLinux #ARM64 #AArch64
#RetroComputing #Upcycle #Upcycling #E-waste #ElectronicWaste #E-wasteReduction
#TechForGood #Sustainability #GreenTech #Repairable #RightToRepair
#Privacy #NoAds #NoTelemetry #DigitalFreedom #DigitalIndependence
#SiblingGoals #AffordableTech #BudgetTech #BudgetComputing #HomeLab
#LaptopAlternative #StudentLife #StudySetup #OnlineClasses #CollegeLife
```

**Suggested caption:**

> Bought a "dead" ₹300 Android TV box → gave my sister a real study PC running Debian + GNOME. Here's every mistake that broke on the way. 🐧📺

---

<div align="center">

### ⭐ Star this repo if it saved you an evening of troubleshooting ⭐

**Made with 🐧, 🔧, ₹300, and far too many failed boot attempts.**

*Built at 2 AM, powered by YouTube tutorials and community forum posts.*

</div>
