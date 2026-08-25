# Generate Events

Now we're moving into the actual SOC workflow.

### 1. Generate the Failed Logins

Restart your Windows VM and attempt to login 5 times with an incorrect password.

You may come across a challenge question after about 4 failed logins.

After the 5th attempt you may have to restart your device.

<img width="2482" height="1476" alt="image" src="https://github.com/user-attachments/assets/49d8b750-2438-4a45-8eeb-9d46d3b33603" />
<br><br>
<img width="2480" height="1482" alt="image" src="https://github.com/user-attachments/assets/26ccf7a4-f657-4dd3-bf2d-a0afd914efa9" />
<br><br>
<img width="2486" height="1476" alt="image" src="https://github.com/user-attachments/assets/56fd2075-d7ec-4fd1-a90c-6af3ce0e7c18" />

<br>
<br>
<br>
<br>

After restarting your device, login as normal.

Before touching Splunk, let's verify that Windows actually generated the event.
```powershell
Get-WinEvent -FilterHashtable @{
    LogName='Security'
    Id=4625
} -MaxEvents 10 |
Select-Object TimeCreated, Id
```

This command does not require backticks at the end to be separated on to multiple lines. 

The event ID we are looking for is 4625.

<img width="1268" height="694" alt="image" src="https://github.com/user-attachments/assets/08971b21-c647-4ec0-8b11-8890e685b3e0" />

Notice how in my screenshot there are 7 failed logins in rapid succession. Those are the 7 intentional attempts I just performed.

<br>
<br>
<br>
<br>

If you want to see the most recent one in greater detail:
```powershell
Get-WinEvent -FilterHashtable @{
    LogName='Security'
    Id=4625
} -MaxEvents 1 | Format-List *
```
You will see general information at the top and specific details towards the bottom.

<img width="2442" height="1348" alt="image" src="https://github.com/user-attachments/assets/be8892d4-bc8b-48c9-b9f2-7d92823edfc2" />
<br><br>
<img width="2436" height="1342" alt="image" src="https://github.com/user-attachments/assets/e416a5a2-6d04-46c5-a5c4-55761ee7c480" />

<br>
<br>
<br>
<br>

This step confirms that the log generation is working.

Now let's confirm that it is being ingested to Splunk.

# 

#### Troubleshooting Dashboard

If the Splunk dashboard is not responding check two things. In my experience VMware Workstation Pro's NAT service stops running or needs to be restarted after long periods of inactivity, which stops the VMs from receiving internet access.

First, check to see if you have internet access in the bottom right corner

<img width="460" height="214" alt="Screenshot 2026-08-24 160015" src="https://github.com/user-attachments/assets/eaf95451-7c05-4f2f-8e99-1ffeadcf876d" />

<br>
<br>
<br>
<br>

If you do not, on your host computer, open services by searching for it in the start menu. click on any service and type `v`. You should see "VMware NAT Service.

Right click it and choose `Start` if it is not running. If it is running, choose `Restart`.

<img width="2290" height="1178" alt="image" src="https://github.com/user-attachments/assets/105ec8c9-c470-4e19-b82e-c557633c4475" />

<br>
<br>
<br>
<br>

This usually solves the issue for me.

<img width="402" height="216" alt="Screenshot 2026-08-24 155806" src="https://github.com/user-attachments/assets/d150ee3b-d613-430b-88af-00c17a14bed1" />

The Splunk service on the Ubuntu server often suffers from the same problem, ceasing to run after long periods of inactivity.

Make sure the Ubuntu VM is powered on and connect to it via ssh.

Check the status:
```bash
sudo -H -u splunk /opt/splunk/bin/splunk start
```

If it is not running restart it:

```bash
sudo -H -u splunk /opt/splunk/bin/splunk start
```

#

<br>
<br>
<br>
<br>

Open the Splunk dashboard in a web browser and navigate to "Searching & Reporting". Set the timeframe to the last 15 minutes

Start with the simplest possible search:
```spl
index=* 4625
```

<img width="2486" height="1380" alt="image" src="https://github.com/user-attachments/assets/7cc5cc21-1962-4945-86a1-fa9a348c75b9" />

<br>
<br>
<br>
<br>

Now let's run a search that we can later save as a 10-minute detection alert.

In Splunk, set the time range to the Last 15 minutes.

Search for:

```spl
index=* 4625
| bin _time span=10m
| stats count by _time host
| where count >= 5
```

<img width="2482" height="1378" alt="image" src="https://github.com/user-attachments/assets/28840441-0712-4a44-bef9-495c8a1e5bb0" />

<br>
<br>
<br>
<br>

Because that is working it's time to refine the SPL (Search Processing Language) input to be more specific.

Splunk is detecting the string `4625`, but before we turn this into an alert, we want to make sure the search is more specific. 
```spl
index=* sourcetype="XmlWinEventLog:Security" "<EventID>4625</EventID>"
| bin _time span=10m
| stats count by _time host
| where count >= 5
```

I also changed the time frame to the last 60 minutes because some time passed.

<img width="2482" height="1392" alt="image" src="https://github.com/user-attachments/assets/516dda05-dd14-4473-b9c3-c68cc7b092aa" />

<br>
<br>
<br>
<br>

The detection logic is:
```
XmlWinEventLog:Security
        ↓
<EventID>4625</EventID>
        ↓
Group into 10-minute windows
        ↓
Count events
        ↓
5+ failures
        ↓
🚨 Detection
```

Great. It's still working. Now lets modify it slightly more to make it more useful for a SOC analyst to review by changing the `count` field to `failed_logins`. This way we know exactly what is being counted.
```spl
index=* sourcetype="XmlWinEventLog:Security" "<EventID>4625</EventID>"
| bin _time span=10m
| stats count as failed_logins by _time host
| where failed_logins >= 5
```

<img width="2486" height="1386" alt="image" src="https://github.com/user-attachments/assets/567d3fdd-d508-4af8-9859-ddb0302224bf" />

<br>
<br>
<br>
<br>

Perfect. Now we have the SPL we can turn into an automated alert. 

