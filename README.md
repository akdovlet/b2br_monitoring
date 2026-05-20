# monitoring.sh

A system monitoring script written as part of the **Born2beRoot** project at [42 School](https://42.fr). It collects key system metrics and broadcasts them to all connected terminals using `wall`.

> **No AI was used during the production of this script or any associated research. AI was used solely for the redaction of this README.**

---

## What it does

Every time the script runs, it gathers a snapshot of the machine's current state and broadcasts it system-wide. It is meant to be executed automatically every 10 minutes via a `cron` job, so any logged-in user always has a recent overview of the server's health.

The broadcast message contains the following information:

| Field | Source | Description |
|---|---|---|
| **System Architecture** | `uname -a` | Kernel version, hostname, hardware platform, and OS |
| **CPU physical cores** | `/proc/cpuinfo` (`cpu cores`) | Number of physical processor cores |
| **CPU virtual cores** | `/proc/cpuinfo` (`siblings`) | Number of logical threads (includes hyperthreading) |
| **RAM Usage** | `free --mega` | Used / total RAM in MB with percentage |
| **Disk Usage** | `df -h --total` | Used / total disk space with percentage |
| **CPU Usage** | `top -bn1` | Combined user + system CPU load percentage |
| **Last Reboot** | `who -b` | Date and time of the last system boot |
| **LVM** | `lsblk` | Whether any LVM volumes are present (Active / Inactive) |
| **TCP** | `/proc/net/sockstat` | Number of currently established TCP connections |
| **Users logged** | `who` | Number of users currently logged into the system |
| **Network** | `hostname -I` / `ip link` | Primary IPv4 address and MAC address of the machine |
| **Sudo calls** | `/var/log/auth.log` | Total number of commands run with `sudo` since log rotation |

---

## Requirements

- A Debian-based Linux system (the script reads Debian-specific paths such as `/var/log/auth.log` and `/proc/net/sockstat`)
- Standard GNU/Linux utilities: `uname`, `grep`, `awk`, `free`, `df`, `top`, `who`, `lsblk`, `ip`, `hostname`, `wall`
- Root or sudo privileges are not required to run the script itself, but reading `/var/log/auth.log` may require appropriate permissions depending on system configuration

---

## Usage

### Run manually

```bash
bash monitoring.sh
```

All terminals of currently logged-in users will receive the broadcast.

### Schedule with cron (recommended)

To run the script every 10 minutes as required by the Born2beRoot subject:

```bash
sudo crontab -e
```

Add the following line:

```
*/10 * * * * bash /path/to/monitoring.sh
```

The `-n` flag passed to `wall` suppresses the banner line that would otherwise prepend the message, keeping the output clean.

---

## Project context

Born2beRoot is a system administration project at 42 School. Students set up a virtual machine running a minimal Debian or Rocky Linux installation, configure it according to a strict set of rules (partitioning with LVM, firewall, SSH hardening, password policies, etc.), and write this monitoring script to demonstrate they understand the system they have built.

---

## Author

**akdovlet** — akdovlet@student.42.fr  
Created: 2023-12-29
