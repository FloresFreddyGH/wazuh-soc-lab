# Screenshot evidence

These captures document server setup, Windows agent enrollment, and the first alert investigation. Filenames have been organized for this repository; the image contents are unchanged. Some captures show an intermediate step, so the captions explain what each one does and does not establish.

| Screenshot | What it shows |
| --- | --- |
| [01 — Virtual Machine Manager ready](01-virtual-machine-manager-ready.png) | Connected QEMU/KVM entry and host group membership. |
| [01a — Initial connection error](01a-virt-manager-connection-error.png) | The initial libvirt connection failure, before logging out and back in. |
| [02a — CPUs](02a-vm-cpus.png) | Four virtual CPUs allocated. |
| [02b — Memory](02b-vm-memory.png) | 8192 MiB allocated to the VM. |
| [02c — Disk](02c-vm-disk.png) | 80 GiB VirtIO disk before the external snapshot was created. |
| [02d — Network](02d-vm-network.png) | Default NAT network, VirtIO NIC, and observed guest address. |
| [03 — Installer](03-wazuh-vm-installer-boot.png) | Ubuntu installer at keyboard configuration, showing it booted successfully. |
| [04 — Guest network](04-wazuh-server-network.png) | DHCP address assigned during installation. |
| [05 — Mirror check](05-ubuntu-mirror-check-passed.png) | Ubuntu package mirror passed its connectivity test. |
| [06 — Storage layout](06-wazuh-server-storage-layout.png) | Proposed ext4 root filesystem on the 80 GiB virtual disk. |
| [07 — SSH configuration screen](07-wazuh-server-ssh-configuration.png) | Captured before OpenSSH was selected; this screen alone does not prove installation. See 08 and 09a. |
| [08 — Installation complete](08-ubuntu-server-installation-complete.png) | Completed Ubuntu installation, including OpenSSH installation in the log. |
| [08a — Installation media message](08a-installation-media-unmount.png) | CD-ROM unmount failure and request to remove installation media during reboot. |
| [09 — Resource verification](09-wazuh-server-first-login.png) | Logged-in account, hostname, memory, CPU count, root disk, and IP address. |
| [09a — SSH connection](09a-ssh-connection.png) | Successful SSH login from the desktop before package updates. No password value is visible. |
| [10 — Dashboard overview](10-wazuh-dashboard-first-login.png) | Successful dashboard login with no registered endpoint agents yet. |
| [10a — Health check in progress](10a-dashboard-health-check-in-progress.png) | API checks passed; the alerts index-pattern check was still loading at capture time. The overview is shown in 10. |
| [11 — Services and Filebeat](11-wazuh-services-and-filebeat-check.png) | All four central services active and Filebeat output test successful. |
| [12 — Dashboard health checks](12-wazuh-post-reboot-health-check.png) | API checks passed; the alerts index-pattern check was still loading. |
| [13 — Windows installer boot](13-windows11-installer-boot.png) | Windows 11 Setup keyboard screen after the endpoint VM booted from the official evaluation ISO. |
| [14 — Windows installation disk](14-windows11-installation-disk.png) | Windows Setup detected the empty 80 GiB virtual disk. |
| [15 — Windows first desktop](15-windows11-first-desktop.png) | Windows 11 Enterprise Evaluation reached the desktop after installation. |
| [16 — Windows Wazuh port check](16-windows-first-wazuh-port-check.png) | PowerShell TCP tests from `192.168.122.37` to Wazuh `192.168.122.243`; ports 1514 and 1515 both succeeded. This does not prove an agent is enrolled. |
| [17 — Enrollment port check](17-windows-wazuh-enrollment-port-check.png) | TCP 1515 reachable from Windows; not enrollment evidence by itself. |
| [18 — Agent service running](18-windows-wazuh-agent-service-running.png) | Installation commands and WazuhSvc Running in Windows PowerShell. |
| [19 — Agent active](19-windows-wazuh-agent-active.png) | WIN11-ENDPOINT, agent 001, version 4.14.8, Active. |
| [20 — First endpoint baseline](20-windows-wazuh-first-security-baseline.png) | Inventory, configuration score 26%, and 19 vulnerability findings; no confirmed compromise established. |
| [21 — Threat Hunting overview](21-windows-threat-hunting-overview.png) | 488 alerts in the selected window, largely SCA, with no level 12+ alerts shown. |
| [22 — First alerts list](22-windows-first-alerts-list.png) | 489 records at capture time, including agent lifecycle, SCA, and service-change events. |
| [23 — BITS event details](23-windows-service-startup-change-details.png) | Event 7040: BITS changed from auto start to demand start. |
| [24 — UCPD event details](24-windows-ucpd-startup-change-details.png) | Event 7040: UCPD changed from auto start to system start. |

Duplicate and mislabeled intermediate captures were left in the original local folder. The repository uses the actual completed-installation screen and browser dashboard capture. The snapshot filename repair and synchronized-clock check are described in the journal from the terminal outputs; no additional screenshots are claimed for them.

The source folder had the service-running image labeled 19 and the active dashboard labeled 19 with a suffix. Repository copies use 18 and 19 respectively. Screenshot 25 is reserved for the next milestone.
