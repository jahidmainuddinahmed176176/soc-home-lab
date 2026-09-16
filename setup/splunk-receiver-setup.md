# Splunk Enterprise - Parrot OS

Hi! This is how I set up Splunk on my Parrot machine.

## Install the .deb

    sudo dpkg -i ~/Downloads/splunk-10.4.3-4174a2deda5d-linux-amd64.deb
    sudo apt install -f -y

## Create admin user

Splunk needs a user. Easiest way:

    sudo tee /opt/splunk/etc/system/local/user-seed.conf > /dev/null <<'EOF'
    [user_info]
    USERNAME = admin
    PASSWORD = Soc2026Lab!
    EOF

    sudo /opt/splunk/bin/splunk stop --run-as-root
    sudo /opt/splunk/bin/splunk start --accept-license --run-as-root

Splunk auto-creates the user, then deletes the seed file.

## Login

Open browser: http://localhost:8000

- User: admin
- Pass: Soc2026Lab!

## Turn on the receiver

Splunk needs to be told to listen for incoming logs:

    sudo /opt/splunk/bin/splunk enable listen 9997 -auth admin:Soc2026Lab! --run-as-root

You'll see: Listening for Splunk data on TCP port 9997.

## Check it's listening

    sudo ss -tlnp | grep 9997

Should show:
    LISTEN 0  128  0.0.0.0:9997  0.0.0.0:*  users:(("splunkd",...))

## Splunk commands I use

| What | Command |
|---|---|
| Start | sudo /opt/splunk/bin/splunk start --accept-license --run-as-root |
| Stop | sudo /opt/splunk/bin/splunk stop --run-as-root |
| Status | sudo /opt/splunk/bin/splunk status --run-as-root |
| Restart | sudo /opt/splunk/bin/splunk restart --run-as-root |

## Auto-start on boot (optional)

    sudo /opt/splunk/bin/splunk enable boot-start -user znx

## Check logs arrive

In Splunk web -> Search & Reporting:

    index=main sourcetype=XmlWinEventLog:Microsoft-Windows-Sysmon/Operational

You should see events from the Windows VM.
