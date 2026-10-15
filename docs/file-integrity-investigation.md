# File integrity monitoring: create, modify, delete

## Configuration

On October 14, I found that FIM was already enabled with a 43,200-second scheduled scan interval. I created C:\WazuhLab, backed up ossec.conf, and added this entry inside the existing syscheck block:

```xml
<directories check_all="yes" realtime="yes">C:\WazuhLab</directories>
```

After restarting WazuhSvc, the agent log showed the folder monitored with realtime, the initial scan ending at 17:34:30 Windows local time, and real-time monitoring starting at 17:34:31. It also confirmed connection to the manager. I waited for initialization before creating the file.

[Configuration evidence](../screenshots/41-windows-fim-lab-folder-configured.png). Startup completion was verified from terminal output, not a separate screenshot.

## Controlled actions

I ran these separately in Windows Administrator PowerShell and checked the corresponding alert before proceeding:

```powershell
New-Item -Path "C:\WazuhLab\fim-test.txt" -ItemType File -Value "First version of my Wazuh FIM lab file."
```

```powershell
Add-Content -Path "C:\WazuhLab\fim-test.txt" -Value "Second version: testing file modification detection."
```

```powershell
Remove-Item -LiteralPath "C:\WazuhLab\fim-test.txt"
```

Dashboard search:

```text
agent.id: "001" AND rule.groups: "syscheck"
```

## Results

Times below are the dashboard's October 14, 2026 Eastern display. The index name ending in October 15 is consistent with the UTC date crossing midnight.

| Action | Dashboard time, approximately | Rule | Level | Evidence |
| --- | --- | --- | --- | --- |
| Created | 20:35:38 | 554 | 5 | [File added](../screenshots/42-wazuh-fim-file-created.png) |
| Modified | 20:39:29 | 550 | 7 | [Checksum changed](../screenshots/43-wazuh-fim-file-modified.png) |
| Deleted | 20:44:38 | 553 | 7 | [File deleted](../screenshots/44-wazuh-fim-file-deleted.png) |

All three alerts identified c:\wazuhlab\fim-test.txt and realtime mode. The added file was 39 bytes. The modification changed size, mtime, and the MD5, SHA-1, and SHA-256 hashes. The deletion record retained the last known 93-byte file metadata; that does not mean the deleted file still existed.

## Finding and limits

Real-time monitoring detected all three intentional actions. Changed hashes support that the contents changed; I did not enable report_changes or claim that Wazuh displayed the text differences. This configuration also does not establish who made a change through who-data attribution.

MITRE mappings describe possible security relevance of the rule, not proof of malicious intent. These alerts were expected results of my own commands.

## Cleanup

The test file was deleted and the deletion alert verified. The empty folder and monitoring entry remain for future practice. The configuration backup and custom process rule also remain locally. This completes the planned FIM exercise.

Reference: [Wazuh FIM proof of concept](https://documentation.wazuh.com/current/proof-of-concept-guide/poc-file-integrity-monitoring.html).
