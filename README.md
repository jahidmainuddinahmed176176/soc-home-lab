# SOC Home Lab

A hands-on Security Operations Center (SOC) lab built from scratch to practice detection engineering and incident analysis.

## Architecture
┌─────────────────────┐ ┌─────────────────────┐
│ Windows Server │ │ Parrot OS │
│ 2019 (Victim VM) │ │ (Analyst Host) │
│ │ │ │
│ ┌───────────────┐ │ │ ┌───────────────┐ │
│ │ Sysmon │ │ │ │ Splunk │ │
│ │ (Endpoint │ │ │ │ Enterprise │ │
│ │ telemetry) │ │ │ │ (SIEM) │ │
│ └───────┬───────┘ │ │ └───────▲───────┘ │
│ │ │ │ │ │
│ ┌───────▼───────┐ │ TCP │ │ │
│ │ Splunk UF │──┼─9997───▶│──────────┘ │
│ │ (Forwarder) │ │ │ │
│ └───────────────┘ │ │ │
└─────────────────────┘ └─────────────────────┘


## What This Lab Does

1. **Endpoint telemetry** — Sysmon logs every process, network connection, file change on Windows
2. **Log shipping** — Splunk Universal Forwarder sends Sysmon events to Splunk over TCP 9997
3. **SIEM ingestion** — Splunk indexes and stores all events
4. **Attack simulation** — Manual adversary techniques run in the VM
5. **Detection** — SPL searches and alerts identify malicious patterns

## Stack

| Component | Purpose | Host |
|---|---|---|
| KVM/QEMU + virt-manager | Hypervisor | Parrot OS |
| Windows Server 2019 | Victim endpoint | VM |
| Sysmon v15.x | Endpoint telemetry | Windows VM |
| Splunk Universal Forwarder 10.4.3 | Log shipping | Windows VM |
| Splunk Enterprise 10.4.3 | SIEM | Parrot OS |

## Attack Simulated

**T1059.001 — PowerShell with Encoded Command**

Command run on Windows:
```powershell
$cmd = "whoami"
$bytes = [System.Text.Encoding]::Unicode.GetBytes($cmd)
$encoded = [Convert]::ToBase64String($bytes)
powershell.exe -EncodedCommand $encoded
