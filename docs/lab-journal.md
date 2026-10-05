# Lab journal

## October 2, 2026 — Building the server

I started this lab on my Ubuntu desktop. My computer had enough memory and disk space for the plan, so I went with local VMs. I gave the Wazuh server 4 CPUs, 8 GiB of RAM, and an 80 GiB disk.

The first speed bump happened before I even had a VM. The package name `qemu-kvm` did not have an installation candidate on my host, so I used `qemu-system-x86` with the libvirt tools and Virtual Machine Manager. After adding my user to the `libvirt` and `kvm` groups, I needed to log out and back in before the manager connected successfully.

Ubuntu Server installed on the virtual disk. I used the default NAT network, and the guest received `192.168.122.243`. The installer could reach the Ubuntu package mirror. I selected a regular ext4 layout without LVM and installed OpenSSH.

I also learned the difference between the VM console and connecting through SSH. Pasting commands became much easier once I could use my desktop terminal. I checked the VM resources, updated packages, rebooted, and connected again.

I created an external disk snapshot before installing Wazuh. At the time, seeing it listed looked like enough confirmation. The next session showed why I needed to check more than that.

Evidence: [hardware settings](../screenshots/README.md), [installation completed](../screenshots/08-ubuntu-server-installation-complete.png), and [resource check](../screenshots/09-wazuh-server-first-login.png).

## October 4, 2026 — The filename problem, then Wazuh

I came back to the lab and SSH could not reach the server. When I tried to start the VM, Virtual Machine Manager reported that its storage file did not exist.

The file actually existed, but the snapshot filename contained a line break before `6.`. The VM configuration expected a space in that spot. That tiny difference stopped the whole VM from booting.

With help, I compared the configured disk path with the real filenames using `virsh domblklist` and `ls -lahb`. Then I used `qemu-img info --backing-chain` to check that the snapshot still pointed to the original Ubuntu disk. Both images reported `corrupt: false`; that was useful metadata, not a full filesystem integrity test.

I renamed the snapshot file to match the configured path while the VM was off. The VM started, and SSH worked again. No reinstall needed. I still need to review the snapshot metadata and test recovery before treating that snapshot as a verified restore point.

Before installing Wazuh, I checked the network address and time. The server was using UTC, with NTP active and the system clock synchronized.

The official installation assistant installed Wazuh 4.14.8. The output reported the manager, indexer, Filebeat, and dashboard services starting successfully. I then opened the dashboard in my desktop browser and logged in. I had initially taken a screenshot of the Ubuntu console instead, so I replaced the portfolio evidence with the actual dashboard view.

The dashboard has no endpoint agents registered yet. It already displays some alerts, but I have not opened those records and investigated their origin. The Windows tests are still ahead of me.

One thing to improve in my workflow: keep generated credentials out of shared troubleshooting output and screenshots. Password rotation is still on my checklist.

Evidence: [first dashboard login](../screenshots/10-wazuh-dashboard-first-login.png). Snapshot repair and clock details above were recorded from terminal output during the session; there is not a separate screenshot for every step.

## October 5, 2026 — Saving the progress

For this short session, I am organizing the setup notes and screenshots into a separate GitHub project. I want the repository to show the build as it happens, including the troubleshooting, rather than wait until every exercise is finished.

The next small task is changing the initial dashboard password and verifying that I can log back in. After the server checks, I will start building the Windows endpoint. That is where I will begin practicing the detection and investigation side of the lab.
