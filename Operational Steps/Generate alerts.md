# Title here

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
} -MaxEvents 10
```

This command does not require backticks at the end to be separated on to multiple lines. 

The event ID we are looking for is 4625.

<img width="1732" height="608" alt="image" src="https://github.com/user-attachments/assets/ef229b42-13d5-4f17-8e3c-15c04d2c1f32" />

Notice how in my screenshot there are 5 failed logins in rapid succession. Those are the 5 intentional attempts we just performed.

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

<img width="2446" height="1336" alt="image" src="https://github.com/user-attachments/assets/edcde720-d64c-4085-9654-0be2bd00aa0d" />
<br><br>
<img width="2446" height="1338" alt="image" src="https://github.com/user-attachments/assets/f75e76a6-477d-44e1-845d-b62679f2a749" />

<br>
<br>
<br>
<br>

This step confirms that the log generation is working.

Now let's confirm that it is being ingested to Splunk.

# 

#### Troubleshooting Dashboard

If the Splunk dashboard is not responding check two things. In my experience VMware Workstation Pro's NAT service stops running or needs to be restarted after long periods of inactivity, which stops the VMs from receiving internet access.

First, make sure you have internet access in the bottom right corner

<img width="460" height="214" alt="Screenshot 2026-08-24 160015" src="https://github.com/user-attachments/assets/eaf95451-7c05-4f2f-8e99-1ffeadcf876d" />

<br>
<br>
<br>
<br>

If you do not, open services by searching for it in the start menu. click on any service and type `v`. You should see "VMware NAT Service.

Right click it and choose `Start` if it is not running. If it is running, choose `Restart`.

<img width="2290" height="1178" alt="image" src="https://github.com/user-attachments/assets/105ec8c9-c470-4e19-b82e-c557633c4475" />

<br>
<br>
<br>
<br>

This usually solves the issue for me.

<img width="402" height="216" alt="Screenshot 2026-08-24 155806" src="https://github.com/user-attachments/assets/d150ee3b-d613-430b-88af-00c17a14bed1" />

The Splunk service on the Ubuntu server offten suffers from the same problem, ceasing to run after long periods of inactivity.

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
```
index=* EventCode=4625
```

