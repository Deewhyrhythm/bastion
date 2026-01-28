[← home](README.md) · [host security map](host-security-map.md)

## 101-Point Security Hardening Checklist

## Ring 3 - User Space

### Network

| # | What | Tools |
| ---: | --- | --- |
| 1 | Firewall with default deny inbound | nftables, ufw |
| 2 | Egress control (outbound policy) | nftables sets |
| 3 | DNS encryption (DoH/DoT) | dnscrypt-proxy, systemd-resolved |
| 4 | DNSSEC validation | unbound, systemd-resolved |
| 5 | VPN | WireGuard |
| 6 | MAC randomization | NetworkManager conf |
| 7 | Network namespaces per app | firejail --net, netns |
| 8 | Tor (selective) | torsocks, whonix |
| 9 | Home network segmentation (VLAN) | managed switch, router ACLs |
| 10 | WPA3 + 802.1X (Wi-Fi hardening) | hostapd, RADIUS |

### Application Isolation

| # | What | Tools |
| ---: | --- | --- |
| 11 | Firejail default profiles (multi-app) | firejail |
| 12 | Browser profile isolation (dev/personal/finance) | Firefox containers |
| 13 | High-risk app throwaway sandbox | bubblewrap, disposable container |
| 14 | Rootless Docker | systemd user service |
| 15 | Docker socket + capability hardening | seccomp, AppArmor, no --privileged |
| 16 | User separation (dev/personal/finance) | useradd + polkit + sudo policy |

### Desktop & Display

| # | What | Tools |
| ---: | --- | --- |
| 17 | Wayland compositor (X11 keylogger isolation) | sway, Wayland |
| 18 | Lock on lid close + idle timeout | swaylock + swayidle |

### Browser

| # | What | Tools |
| ---: | --- | --- |
| 19 | arkenfox user.js hardening | arkenfox |
| 20 | uBlock Origin + strict settings | extension config |

### Crypto Identity & Code Integrity

| # | What | Tools |
| ---: | --- | --- |
| 21 | GPG commit signing | git config signingkey |
| 22 | GPG-signed ACK workflow | GPG + review script |
| 23 | Pre-commit hooks (lint/fmt/secrets) | git hooks, husky |
| 24 | Directory hashing (SHA256+) | custom script |

### Secrets Management

| # | What | Tools |
| ---: | --- | --- |
| 25 | Password manager | KeePassXC, Bitwarden |
| 26 | 2FA / FIDO2 everywhere | TOTP + security keys |
| 27 | Hardware-backed GPG keys | YubiKey, smart card |
| 28 | Key rotation + expiry policy | GPG subkeys, cron reminder |
| 29 | Token scoping (minimal permissions) | fine-grained PAT |
| 30 | Secrets scanning (git) | gitleaks, trufflehog |
| 31 | Secret leakage hygiene (history/env/logs) | HISTIGNORE, env audit, log redaction |
| 32 | Clipboard auto-clear + hijack protection | KeePassXC timeout, wl-copy --clear |
| 33 | gh auth git-credential helper | git credential config |

### Data Safety

| # | What | Tools |
| ---: | --- | --- |
| 34 | rm to rmtrash (accidental delete guard) | alias |
| 35 | tmpfs for sensitive paths | /tmp, build caches, secret dirs |
| 36 | Core dump disable / hardening | systemd coredump.conf, ulimit |
| 37 | Secure deletion / data sanitization | blkdiscard --secure, LUKS header destroy |

### Supply Chain

| # | What | Tools |
| ---: | --- | --- |
| 38 | Dependency pinning + lockfiles | package-lock, Cargo.lock |
| 39 | Plugin/extension audit | manual review, minimalism |
| 40 | Binary/source verification | signatures, checksums |
| 41 | Update hygiene (CVE-aware cadence) | unattended-upgrades, manual policy |
| 42 | Reproducible builds | diffoscope, guix |
| 43 | Certificate transparency monitoring | crt.sh, certspotter |

### Monitoring & Detection

| # | What | Tools |
| ---: | --- | --- |
| 44 | auditd baselines | auditd + aureport |
| 45 | File integrity monitoring | AIDE, Tripwire |
| 46 | Rootkit detection | rkhunter, chkrootkit, lynis |
| 47 | fail2ban | fail2ban |
| 48 | Log protection / remote forwarding | remote syslog, journald seal |
| 49 | Alerting / health checks | custom scripts, healthchecks.io |
| 50 | Canary tokens / honeypots | canarytokens.org, fake credentials |
| 51 | Host inventory + drift detection | osquery, ansible-cmdb |

### Privilege Hardening

| # | What | Tools |
| ---: | --- | --- |
| 52 | Sudoers hardening (least privilege) | visudo, timestamp_timeout, logging |
| 53 | Polkit policy hardening | /etc/polkit-1/rules.d/ |
| 54 | Strict umask + secret file permissions | umask 077, chmod 600/700 |

### Filesystem

| # | What | Tools |
| ---: | --- | --- |
| 55 | System-wide mount hardening | noexec,nodev,nosuid on /tmp, /var/tmp |
| 56 | Removable media noexec | fstab, udisks2 policy |

---

## Ring 2-1 - Drivers & Privileged Services

| # | What | Tools |
| ---: | --- | --- |
| 57 | AppArmor / SELinux profiles | apparmor, selinux |
| 58 | Kernel module blacklist/whitelist | modprobe.d |
| 59 | Signed kernel modules only | CONFIG_MODULE_SIG_FORCE |
| 60 | Disable unused services | systemctl mask/disable |
| 61 | Disable unused drivers | blacklist.conf |

---

## Ring 0 - Kernel

| # | What | Tools |
| ---: | --- | --- |
| 62 | sysctl hardening | /etc/sysctl.d/ |
| 63 | Kernel lockdown mode | lockdown=confidentiality |
| 64 | eBPF restriction | sysctl net.core.bpf_jit_harden |
| 65 | KSPP / grsecurity patches | KSPP config, grsecurity |
| 66 | Rapid kernel update cadence | patch policy |
| 67 | RAM encryption | AMD SME/SEV, Intel TME |
| 68 | Encrypted swap / zram | LUKS swap, zram |

---

## Ring -1 - Hypervisor

| # | What | Tools |
| ---: | --- | --- |
| 69 | VM compartmentalization | Qubes OS, Xen, KVM |
| 70 | Minimal host (host ≠ workload) | thin hypervisor |
| 71 | VM-per-risk-level (dev/banking/untrusted) | Qubes domains, libvirt |

---

## Ring -2 - Firmware & UEFI

| # | What | Tools |
| ---: | --- | --- |
| 72 | Full disk encryption | LUKS2 |
| 73 | Secure Boot (signed) | sbctl, distro tooling |
| 74 | Measured Boot / TPM integration | systemd-cryptenroll, tpm2-tools |
| 75 | Discrete TPM preference (dTPM > fTPM) | hardware selection |
| 76 | Intel ME neutralize | me_cleaner |
| 77 | AMD PSP control | firmware settings |
| 78 | coreboot / Heads firmware | coreboot, Heads |
| 79 | BIOS password + boot order lock | BIOS/UEFI settings |
| 80 | Firmware update policy | fwupd |
| 81 | Thunderbolt / DMA control | boltctl, BIOS policy |

---

## Ring -3 - Out-of-Band Management

| # | What | Tools |
| ---: | --- | --- |
| 82 | Intel AMT disable | BIOS, firmware |
| 83 | Remote management disable | BIOS settings |
| 84 | JTAG / debug port disable | hardware/firmware |
| 85 | Supply-chain-aware hardware selection | System76, Purism, NovaCustom |

---

## Physical Security

| # | What | Tools |
| ---: | --- | --- |
| 86 | Separate devices per identity | hardware separation |
| 87 | Air-gapped signing machine | offline device |
| 88 | Faraday bag | RF isolation |
| 89 | Tamper-evident seals | nail polish, glitter |
| 90 | Screen privacy filter | 3M privacy screen |
| 91 | Dead man's switch / remote wipe | custom, Prey |
| 92 | Cold boot mitigation | RAM wipe on shutdown |
| 93 | USB device allowlisting | usbguard |

---

## Backups

| # | What | Tools |
| ---: | --- | --- |
| 94 | Encrypted backups | restic, borg |
| 95 | Immutable / append-only + offline rotation | restic + S3 Object Lock, offline media |
| 96 | Restore drills | scheduled test restores |

---

## OpSec

| # | What | Tools |
| ---: | --- | --- |
| 97 | Threat model document | STRIDE, personal threat matrix |
| 98 | Incident response plan | runbook template |
| 99 | SSH hardening | key-only, fail2ban, sshd_config |
| 100 | Secure communication channels | Signal, Matrix (E2EE) |
| 101 | Social engineering awareness | verification habits, phishing training |
