# Splunk Forwarder - Windows Server 2019

Hi! This is how I set up the log shipper.

## What does it do?

It's a small program that:
1. Reads Windows logs (including Sysmon)
2. Packs them up
3. Sends them over the network to Splunk

Think of it like a delivery truck. Sysmon makes the packages. This truck picks them up and drives them to the warehouse (Splunk).

## Where to download

Windows installer:
https://download.splunk.com/products/universalforwarder/releases/10.4.3/windows/splunkforwarder-10.4.3-4174a2deda5d-windows-x64.msi

## How I installed it

1. Double-click the .msi file
2. Accept license -> Next
3. SSL certificate prompt -> leave blank -> Next
4. Install as: Local System -> Next
5. Windows Event Logs: leave all boxes unchecked -> Next
6. Credentials: username admin, any password -> Next
7. Deployment Server: leave blank -> Next
8. Receiving Indexer:
   - Host: 192.168.122.1
   - Port: 9997
9. Click Install

## Tell it what to send

Open this file in Notepad as Admin:

    notepad "C:\Program Files\SplunkUniversalForwarder\etc\system\local\inputs.conf"

Paste this in:

    [WinEventLog://Microsoft-Windows-Sysmon/Operational]
    disabled = 0
    index = main
    sourcetype = XmlWinEventLog:Microsoft-Windows-Sysmon/Operational
    renderXml = true

Save it.

## Restart the forwarder

    net stop SplunkForwarder
    net start SplunkForwarder

## Check it's running

    sc query SplunkForwarder

Should say: STATE : 4 RUNNING

## Check it's connected

    netstat -an | findstr 9997

You want to see: ESTABLISHED to 192.168.122.1:9997

## Test the connection

    powershell -Command "Test-NetConnection 192.168.122.1 -Port 9997"

Want to see: TcpTestSucceeded : True

## If no events show up

1. Check the service: sc query SplunkForwarder
2. Check network: netstat -an | findstr 9997
3. Check Parrot is listening: sudo ss -tlnp | grep 9997
4. Check inputs.conf content
5. Restart the forwarder
