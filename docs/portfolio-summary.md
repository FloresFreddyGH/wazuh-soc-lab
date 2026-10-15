# Wazuh SOC Home Lab

**Status: Completed learning project**

I built a local security monitoring lab with an Ubuntu Wazuh server and a Windows 11 endpoint using KVM/QEMU. I connected Sysmon and Windows Security logs, created a custom whoami detection rule, and verified alerts for failed logons and file creation, modification, and deletion.

One of the biggest lessons was learning to check what an alert actually proves. I traced a PowerShell event back to the Wazuh agent, investigated why a locally recorded process did not appear in my alert search, and fixed a clock discrepancy before comparing timestamps again. I documented both the successful tests and the troubleshooting along the way.

**Skills practiced:** virtualization, Linux and Windows administration, log analysis, Sysmon, Wazuh rules, authentication investigation, file integrity monitoring, NTP troubleshooting, and evidence-based documentation.

**Scope:** harmless controlled tests in my own VMs; no claim of production deployment or real attack response.

**Repository:** [Wazuh SOC Home Lab](https://github.com/FloresFreddyGH/wazuh-soc-lab)

**Suggested image:** [File integrity monitoring results](../screenshots/44-wazuh-fim-file-deleted.png)

**Interview talking point:** “I followed activity from Windows into Wazuh, built and verified a custom process-detection rule, and investigated failed logons and file changes. When timestamps did not agree, I checked UTC and synchronization status, fixed the endpoint clock, and verified a fresh event.”
