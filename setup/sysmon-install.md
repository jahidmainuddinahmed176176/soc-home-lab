# Sysmon Installation - Windows Server 2019

Hi! This is how I installed Sysmon on my Windows VM.

## What is Sysmon?

Think of Sysmon like a security camera for Windows. It records:
- Every program that starts
- Every network connection
- Every file that gets created
- Every registry change

Windows has its own logs, but they're weak. Sysmon fills the gaps.

## Where to download

**Sysmon tool:**
https://download.sysinternals.com/files/Sysmon.zip

**Config file (SwiftOnSecurity - best one):**
https://raw.githubusercontent.com/SwiftOnSecurity/sysmon-config/master/sysmonconfig-export.xml

## How I installed it

Inside the Windows VM, open CMD as Administrator. Then:

    cd C:\Users\Administrator\Downloads
    Sysmon64.exe -accepteula -i sysmonconfig-export.xml

You should see something like:
    System Monitor v15.x
    Sysmon installed.
    SysmonDrv installed.
    Starting SysmonDrv.
    SysmonDrv started.
    Starting Sysmon64..
    Sysmon64 started.

## Check it's running

    sc query sysmon64

Should say: STATE : 4 RUNNING

## Where to see the events

1. Open Event Viewer
2. Go to: Applications and Services Logs -> Microsoft -> Windows -> Sysmon -> Operational
3. You'll see events appearing as things happen on Windows

## Important Event IDs I learned

| ID | What it means |
|---|---|
| 1 | A program started |
| 3 | A network connection was made |
| 7 | A DLL was loaded |
| 8 | A remote thread was created (process injection) |
| 11 | A file was created |
| 13 | A registry value was set |
| 22 | A DNS query was made |

These are the ones I check most often.

## Why the SwiftOnSecurity config

It does 3 things:
- Removes noise (Windows does lots of boring stuff)
- Adds rules for suspicious patterns
- Tags events with MITRE ATT&CK IDs

The RuleName field shows the technique (e.g., T1042 = file association change).
