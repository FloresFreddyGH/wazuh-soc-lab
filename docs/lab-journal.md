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

## October 5, 2026 — First Windows endpoint

I created the Windows 11 Enterprise Evaluation VM with 4 vCPUs, 8 GiB of RAM, an 80 GiB SATA disk, UEFI firmware, and an emulated TPM 2.0 device. The installer reached the desktop successfully. I created the local `labadmin` account and kept the privacy choices limited to required diagnostics.

The endpoint received `192.168.122.37` from the same default NAT network as the Wazuh server. From an Administrator PowerShell window, I tested TCP ports 1514 and 1515 on `192.168.122.243`; both returned `TcpTestSucceeded: True`. This is the first evidence that the endpoint can reach the manager before the agent is installed.

This was a good stopping point for the day. The next step is installing and enrolling the Wazuh agent, then adding Sysmon so I can investigate endpoint events instead of only validating infrastructure.

Evidence: [Windows installer boot](../screenshots/13-windows11-installer-boot.png), [installation disk](../screenshots/14-windows11-installation-disk.png), [first desktop](../screenshots/15-windows11-first-desktop.png), and [Wazuh port checks](../screenshots/16-windows-first-wazuh-port-check.png).

## October 8, 2026 — Troubleshooting copy and paste

I had trouble copying text from my Ubuntu host into the Windows VM. This slowed down the Wazuh agent setup, so I worked through the clipboard problem first.

The SPICE service was initially stopped. Starting it alone did not solve the problem: its logs showed repeated agent restarts and a failure to open `com.redhat.spice.0`. I checked the VM's SPICE display and channel settings, then found a warning on the VirtIO Serial Driver in Windows Device Manager. Its properties reported Code 52, meaning Windows could not verify the driver's digital signature.

I downloaded and mounted the VirtIO driver ISO. The manual driver file picker repeatedly reported that `vioser` could not be found, so I used Administrator PowerShell:

```powershell
pnputil /add-driver "E:\vioserial\w11\amd64\vioser.inf" /install
```

Windows reported that the driver package already existed and was up to date. After restarting Windows, the warning on the VirtIO Serial Driver was gone. I resolved the copy-and-paste problem during this session.

I learned that a service being installed or briefly showing as running does not prove the whole feature works. Checking the logs and the underlying driver helped me find the problem. The next lab step is installing the Wazuh agent and confirming that the Windows endpoint appears in the dashboard.


## October 9, 2026 — My first connected endpoint and alert investigation

Today I got `WIN11-ENDPOINT` connected to Wazuh. I checked port 1515 again, installed the Windows agent, started `WazuhSvc`, and then saw the endpoint appear as Active with agent ID `001`. Seeing my Windows VM show up in the dashboard was exciting. Now I could start looking at what it was actually reporting.

I also asked what it meant to “point the agent at the server.” The agent is the program on Windows, and the manager address tells it where to send the information it collects. That helped the installation command make more sense instead of just being something to copy and paste.

The first baseline showed 123 configuration checks passed, 350 failed, and 9 not applicable, with a score of 26%. There were also 5 High and 14 Medium vulnerability findings listed against QEMU guest agent. I learned that these numbers need context: the score is not my lab progress, and a finding does not automatically mean someone attacked the machine.

In Threat Hunting, I opened an alert saying a service startup type had changed. My first guess was that Windows had been configured to start Wazuh automatically. After opening the records, I found that the two services were actually BITS and UCPD. BITS changed from auto start to demand start; UCPD changed from auto start to system start. Both were Windows event 7040, matched by Wazuh rule 61104.

I did a little research on what those components do, then asked why normal Windows changes would show up in a security tool. The useful lesson was that Wazuh can flag a change worth reviewing without proving it was malicious. These records show what changed, but they do not establish who made the changes or why. I have not confirmed the cause yet.

Also, I had to find a way to put the dashboard in dark mode because the bright screen was burning my eyes, haha. Much better for reading through events.

This lab is still in progress. Next is Sysmon, followed by controlled login, PowerShell, and scheduled-task exercises. I want to understand what I am seeing and explain my findings, not just collect screenshots of a working dashboard.

Evidence: [port test](../screenshots/17-windows-wazuh-enrollment-port-check.png), [running service](../screenshots/18-windows-wazuh-agent-service-running.png), [active endpoint](../screenshots/19-windows-wazuh-agent-active.png), [first baseline](../screenshots/20-windows-wazuh-first-security-baseline.png), and [investigation write-up](first-alert-investigation.md).
