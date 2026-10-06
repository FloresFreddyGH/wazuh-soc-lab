# Wazuh SOC Home Lab

**In progress — Wazuh server and Windows endpoint are installed; agent enrollment and investigation exercises are next.**

After finishing my [Azure security lab](https://github.com/FloresFreddyGH/azure-security-lab), I wanted to build something I could keep experimenting with on my own computer. This is my next project: a local Wazuh lab where I plan to collect Windows events, investigate controlled activity, and write up what I actually find.

So far, I have built the Ubuntu server VM, connected over SSH, installed Wazuh 4.14.8, and reached the dashboard. Getting the dashboard open was a nice milestone. Getting there also gave me some unexpected practice with Linux permissions and a broken snapshot filename.

![Wazuh dashboard after first login](screenshots/10-wazuh-dashboard-first-login.png)

## What works so far

- KVM/QEMU and Virtual Machine Manager on my Ubuntu desktop.
- Ubuntu Server 24.04.5 LTS VM with 4 vCPUs, 8 GiB RAM, and an 80 GiB virtual disk.
- Default NAT networking and SSH access from the desktop.
- Package updates and a synchronized UTC clock checked before installing Wazuh.
- Wazuh manager, indexer, Filebeat, and dashboard started by the installation assistant.
- Successful browser login to the dashboard.
- Windows 11 Enterprise Evaluation endpoint installed with 4 vCPUs, 8 GiB RAM, an 80 GiB disk, UEFI, TPM 2.0, and default NAT networking.
- Windows endpoint received `192.168.122.37`; TCP connectivity to Wazuh ports 1514 and 1515 succeeded.
- A snapshot-related boot problem diagnosed and repaired without reinstalling Ubuntu.

The dashboard screenshot shows **no registered agents**. It also shows 124 medium and 179 low severity alerts, but I have not investigated those records yet. Those numbers are not evidence of a successful attack simulation or a confirmed compromise.

## Lab setup

| Component | Configuration |
| --- | --- |
| Host | Ubuntu desktop, Intel Core i5-14400F, approximately 32 GB RAM |
| Hypervisor | KVM/QEMU with Virtual Machine Manager |
| Server | `wazuh-server`, Ubuntu Server 24.04.5 LTS |
| VM resources | 4 vCPUs, 8192 MiB RAM, 80 GiB virtual disk |
| Storage | ext4 root filesystem; no LVM in this installation |
| Network | libvirt default NAT; observed guest IP `192.168.122.243` via DHCP |
| Platform | Wazuh 4.14.8, all central components on one VM |
| Administration | SSH from the host; dashboard over HTTPS |
| Endpoint | Windows 11 Enterprise Evaluation VM, `labadmin`, observed IP `192.168.122.37`; Wazuh agent and Sysmon not installed yet |

The address above belongs to my local lab and may change. The host and VM must be running for me to access the dashboard or connect over SSH.

## Next steps

- [x] Rotate the initial dashboard admin password and verify a new login.
- [x] Verify service health after reboot and document the result.
- [ ] Set up deliberate Wazuh package updates and SSH key authentication.
- [ ] Review snapshot metadata after the filename repair and test a recovery procedure before relying on it.
- [x] Create the Windows VM and verify connectivity to Wazuh ports 1514 and 1515.
- [ ] Enroll its Wazuh agent.
- [ ] Install Sysmon and verify that Windows events reach Wazuh.
- [ ] Investigate controlled failed logins, a harmless PowerShell script, and a harmless scheduled task.
- [ ] Write incident timelines, compare expected and unexpected activity, and verify cleanup.

## Notes and evidence

- [Lab journal](docs/lab-journal.md): what I did, what went wrong, and what I learned.
- [Setup notes](docs/setup-notes.md): commands and configuration recorded during the build.
- [Screenshot index](screenshots/README.md): evidence with captions and limits.
- [Portfolio summary](docs/portfolio-summary.md): a short description of the current milestone.

This repository contains documentation and screenshots. VM disks, installation ISOs, private keys, passwords, and installation credential archives stay out of Git.

## References and assistance

- [Wazuh quickstart](https://documentation.wazuh.com/current/quickstart.html)
- [Ubuntu virtualization documentation](https://documentation.ubuntu.com/server/how-to/virtualisation/)
- [Libvirt domain XML documentation](https://libvirt.org/formatdomain.html)

I use official documentation and AI assistance for explanations, troubleshooting, and organizing my notes. I perform the lab steps and keep the claims tied to the outputs and screenshots I have collected.
