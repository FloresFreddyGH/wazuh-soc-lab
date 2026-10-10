# Portfolio summary

## Wazuh SOC Home Lab

**Status: In progress — Windows agent connected and first alerts investigated.**

I am building a local security monitoring lab on my Ubuntu desktop using KVM/QEMU and Wazuh. My Ubuntu server and Windows 11 endpoint are running, and the Windows agent is active in the dashboard. I have reviewed the first security baseline and investigated two service-startup changes by reading the underlying Windows event fields.

My first guess was that an alert came from configuring Wazuh to start automatically. The records showed BITS and UCPD instead. That was useful practice in checking an assumption against evidence. I also worked through a broken snapshot filename and a VirtIO driver issue that interrupted copy and paste in the Windows VM.

Next are Sysmon and controlled login, PowerShell, and scheduled-task exercises. The full lab is not finished yet.

**Skills practiced:** Linux administration, virtualization, networking, SSH, troubleshooting, Wazuh deployment, endpoint enrollment, event analysis, and documentation.

**Repository:** [Wazuh SOC Home Lab](https://github.com/FloresFreddyGH/wazuh-soc-lab)

**Suggested image:** [Active Windows endpoint](../screenshots/19-windows-wazuh-agent-active.png)
