# Setup notes

These notes record the setup used in this lab. They are not an unattended installation script. Commands run on the desktop host and inside the guest are separated below.

## Desktop host

Installed the virtualization packages:

```bash
sudo apt update
sudo apt install qemu-system-x86 libvirt-daemon-system libvirt-clients virt-manager ovmf swtpm
sudo usermod -aG libvirt,kvm "$USER"
```

Logged out of the desktop and back in so the new group memberships took effect. Checked with `id -nG` and opened Virtual Machine Manager.

Created `wazuh-server` from an Ubuntu Server 24.04 ISO, with 4 vCPUs, 8192 MiB RAM, 80 GiB disk, and default NAT networking. Installed Ubuntu Server, OpenSSH, and an ext4 root filesystem. No optional server snaps were selected.

Connected from the desktop:

```bash
ssh freddy@192.168.122.243
```

## Ubuntu guest

Checked resources and network information:

```bash
whoami
hostname
free -h
nproc
df -h /
ip -br address
timedatectl status
```

Observed approximately 7.8 GiB usable memory, 4 CPUs, a 79 GiB root filesystem, and `enp1s0` at `192.168.122.243/24`. Before Wazuh installation, the clock reported `System clock synchronized: yes` and `NTP service: active`.

Updated packages and rebooted:

```bash
sudo apt update && sudo apt upgrade -y
sudo reboot
```

Installed Wazuh with the official assistant, following the [quickstart](https://documentation.wazuh.com/current/quickstart.html):

```bash
curl -fLO https://packages.wazuh.com/4.14/wazuh-install.sh &&
sudo bash ./wazuh-install.sh -a
```

The installation reported version **4.14.8** and finished on **October 4, 2026 at 04:54:20 UTC**. The generated passwords and credential archive are not included in this repository.

Opened `https://192.168.122.243` in the desktop browser and signed into the dashboard. The browser showed a certificate warning for the installer-generated certificate. The dashboard address is local to this lab.

## Snapshot troubleshooting

The external snapshot file had an embedded newline, while the configured disk path used a space. Read-only checks on the host were:

```bash
virsh -c qemu:///system domblklist wazuh-server --details
sudo ls -lahb /var/lib/libvirt/images/
```

I also inspected the actual file with `qemu-img info --backing-chain`. It was a qcow2 overlay backed by `wazuh-server.qcow2`, and both images reported `corrupt: false`. The base image had an 80 GiB virtual size but used roughly 3.9 GiB on disk at that point.

With the VM off, I renamed the overlay to the path expected by the VM, then started the domain and successfully connected over SSH. That verified boot and login after the repair. Snapshot reversion has **not** been tested, and the snapshot metadata still needs review after the rename. These notes intentionally do not provide a generic rename command: future repairs need the actual paths and disk chain checked first.

## Pending verification

- Rotate the initial admin credential and test a fresh login.
- Check all Wazuh services after reboot.
- Follow the Wazuh quickstart's package-repository guidance for deliberate component upgrades.
- Configure SSH keys and a stable guest address before endpoint enrollment.
- Validate snapshot recovery before relying on it.
