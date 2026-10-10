# First alert investigation: Windows service startup changes

I wanted to understand an actual alert after connecting my Windows endpoint. I started with “Service startup type was changed” and checked the details instead of assuming the title explained the cause.

## Scope and evidence

- Endpoint: `WIN11-ENDPOINT`, agent `001`, observed IP `192.168.122.37`.
- Source: Windows System channel, Service Control Manager, event ID `7040`.
- Wazuh rule: `61104`, level `3`.
- Search: `rule.id: "61104"`, with the endpoint filter retained.
- Two matching records were visible in the selected 24-hour window.

| Service | Previous startup setting | New startup setting | Windows record ID | Evidence |
| --- | --- | --- | --- | --- |
| Background Intelligent Transfer Service (BITS) | auto start | demand start | 1140 | [BITS details](../screenshots/23-windows-service-startup-change-details.png) |
| UCPD | auto start | system start | 1145 | [UCPD details](../screenshots/24-windows-ucpd-startup-change-details.png) |

## What I learned

My first hypothesis was that the change enabled Wazuh to start with Windows. Neither record identified Wazuh as the changed service, so that explanation did not fit the evidence.

BITS supports background file transfers. Learning what a component does gives me context, but its name alone does not tell me whether a particular change was expected. The rule detected startup configuration changes; it did not establish malicious activity.

**Finding:** BITS and UCPD startup settings changed. No evidence of malicious activity was identified in these two records, and the cause remains unconfirmed. I have not classified them as confirmed benign changes or false positives.

## Follow-up

I would check related Windows update, installation, and administrative activity to establish the cause. I also noticed a timestamp discrepancy: the dashboard lists October 9 around 20:27–20:31, while the raw Windows `systemTime` fields show October 10 around 03:26–03:31 UTC. I need to verify guest/server clocks and dashboard timezone before constructing a precise incident timeline.

This was an investigation of existing activity, not a controlled attack simulation. Sysmon and deliberate detection exercises are still ahead.

## References

- [Microsoft: Background Intelligent Transfer Service](https://learn.microsoft.com/en-us/windows/win32/bits/background-intelligent-transfer-service-portal)
- [Wazuh Windows System rules](https://github.com/wazuh/wazuh/blob/v4.14.8/ruleset/rules/0590-win-system_rules.xml)
