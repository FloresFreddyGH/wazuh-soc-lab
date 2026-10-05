# Website portfolio copy

## Wazuh SOC Home Lab

**Status: In progress — server setup complete, endpoint investigations planned.**

I am building a local security monitoring lab on my Ubuntu desktop using KVM/QEMU and Wazuh. So far, I have configured an Ubuntu Server VM, enabled SSH administration, installed Wazuh 4.14.8, and accessed its dashboard. Along the way, I diagnosed a snapshot filename mismatch that prevented the VM from starting and restored boot access without reinstalling the system.

Next, I will connect a Windows endpoint with the Wazuh agent and Sysmon, then investigate controlled login, PowerShell, and scheduled-task activity. Those detection exercises are not complete yet.

**Skills practiced so far:** Linux administration, virtualization, NAT networking, SSH, troubleshooting, SIEM deployment, and technical documentation.

**Repository:** https://github.com/FloresFreddyGH/wazuh-soc-lab

**Suggested image:** `screenshots/10-wazuh-dashboard-first-login.png`

**Image caption:** First Wazuh dashboard login after deployment; no endpoint agents registered yet.
