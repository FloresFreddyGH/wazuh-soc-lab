# Controlled failed-logon investigation

## Objective and preparation

On October 14, I checked that WazuhSvc and Sysmon64 were running, Windows time was synchronized, and the Logon audit subcategory recorded Success and Failure. I then used runas with the deliberately nonexistent local account LabNoSuchUser. No account was created and no real password was used for this test.

```powershell
$testStart = Get-Date
runas /user:WIN11-ENDPOINT\LabNoSuchUser "cmd.exe"
```

The expected result was error 1326. I initially reset the search start time and got no matching events. Widening the query to the last 30 minutes found three failures from the test attempts:

```powershell
Get-WinEvent -FilterHashtable @{
    LogName = 'Security'
    Id = 4625
    StartTime = (Get-Date).AddMinutes(-30)
} -MaxEvents 10 | Format-List TimeCreated, Id, Message
```

## Evidence and interpretation

| Field | Observed value | Meaning in this test |
| --- | --- | --- |
| Windows event | 4625 | Failed logon |
| Subject | WIN11-ENDPOINT\labadmin | Account initiating the attempt |
| Target | LabNoSuchUser | Nonexistent account used for the test |
| Status / substatus | 0xC000006D / 0xC0000064 | Authentication failure; target username does not exist |
| Logon type | 2 | Interactive logon |
| Source address | ::1 | IPv6 loopback, local to this endpoint |
| Wazuh rule | 60122, level 5 | Logon Failure - Unknown user or bad password |

The newest local event was displayed at 16:17:49 Pacific. The same event in Wazuh contained systemTime 23:17:49 UTC and appeared around 19:17:50 Eastern. These are consistent after timezone conversion and processing time.

Dashboard search:

```text
agent.id: "001" AND data.win.system.eventID: "4625"
```

I used a window covering the attempts; the captured dashboard shows Last 24 hours. Three records were visible.

## Finding and cleanup

The records match the deliberate local tests. I classified them as expected test activity: the failed authentications were real, but no unauthorized actor was established. Three attempts alone do not establish brute force. No test account existed to delete.

Evidence: [readiness](../screenshots/37-windows-failed-logon-test-readiness.png), [local Windows record](../screenshots/38-windows-failed-logon-event-4625.png), [received alert](../screenshots/39-wazuh-failed-logon-alert-details.png), [rule and level](../screenshots/40-wazuh-failed-logon-rule-and-level.png).

Reference: [Microsoft event 4625](https://learn.microsoft.com/windows/security/threat-protection/auditing/event-4625).
