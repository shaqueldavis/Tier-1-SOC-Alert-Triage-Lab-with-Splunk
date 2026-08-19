# Install Linux [Ubuntu](https://releases.ubuntu.com/24.04.4/) Server

### 1. Configure VM With ISO
<a href="https://releases.ubuntu.com/24.04.4/"><img src="https://img.shields.io/badge/-Ubuntu-F37021?&style=for-the-badge&logo=Ubuntu&logoColor=white" /></a>

<img width="2877" height="1621" alt="image" src="https://github.com/user-attachments/assets/37a2ec8a-abac-493c-b3d4-5b5b9f6e7679" />
<br><br>
<img width="1977" height="381" alt="image" src="https://github.com/user-attachments/assets/8cfac32a-8efd-4a83-b4f6-b30a1e7a8fa1" />

<br>
<br>
<br>
<br>

[Click here](https://github.com/shaqueldavis/SOC_Automation/blob/main/Project%20Phases/04.%20VM%20Set%20Up.md/) for thorough guidance on setting up a VM from an ISO file. Get familiar with lines 7-109 but do not take action on the steps. Return here when finished.  The following will cover key points only.

Use the following system configuration.
- RAM:              4 GB
- Processors:       1
- Cores:            4
- Disk:             64 GB

<img width="852" height="827" alt="image" src="https://github.com/user-attachments/assets/d9a1b508-5fb6-4fc0-bc0e-89385246569d" />
<br><br>
<img width="1490" height="1388" alt="Screenshot 2026-08-18 220849" src="https://github.com/user-attachments/assets/62c21f65-2528-431b-bac4-4cfcb07b0d05" />

<br>
<br>
<br>
<br>

### 2. Configure Ubuntu

Use `enter` to make a selection unless otherwise specified.

Select `Try or Install Ubuntu Server`.
<img width="2473" height="1360" alt="image" src="https://github.com/user-attachments/assets/e751cc45-3957-4ae4-ab1c-a8c553ac9e1c" />

<br>
<br>
<br>
<br>

Select `English`.

<img width="2480" height="1351" alt="image" src="https://github.com/user-attachments/assets/2267720b-1b51-429e-aee1-fbdf59a1afcc" />

<br>
<br>
<br>
<br>

Select `Update to the new installer`.

<img width="2479" height="1363" alt="image" src="https://github.com/user-attachments/assets/bfc1bd5f-4605-49d4-af3f-546226e1036b" />

<br>
<br>
<br>
<br>

As long as `Layout:` and `Variant:` are set to `English (US)`, select `Done`.

<img width="2475" height="1369" alt="image" src="https://github.com/user-attachments/assets/1a939e6d-4d2c-407a-b067-6ef2299a989e" />

<br>
<br>
<br>
<br>

`Ubuntu Server` should be marked selected with an `x`. If so, select `Done`.

<img width="2484" height="1367" alt="Screenshot 2026-08-18 222423" src="https://github.com/user-attachments/assets/c712863f-f280-48b2-b6b9-efc80e680f07" />

<br>
<br>
<br>
<br>

VMware’s DHCP service should automatically assigned the Ubuntu VM an IP address on your VMware virtual network. Take note of these settings for future reference. Then select `Done`.

- Network interface: ens33
- Address configuration: DHCPv4
- Installation-time IP address: 192.168.113.159/24
- Network mode: VMware NAT
  
[Yours may differ]

<img width="2482" height="1366" alt="Screenshot 2026-08-18 222517" src="https://github.com/user-attachments/assets/022f033e-ecfc-4923-97e8-77264e2c2fb3" />

<br>
<br>
<br>
<br>

Your home network and VMware NAT connection do not require an HTTP proxy. <br>
The Ubuntu VM already received a private IP address through VMware DHCP, so it should access the internet through VMware NAT without a proxy.

<img width="2482" height="1357" alt="image" src="https://github.com/user-attachments/assets/adbb58ef-828b-4f73-aff6-453d12c0c0c0" />

<br>
<br>
<br>
<br>

[REVISIT]

<img width="2470" height="1356" alt="image" src="https://github.com/user-attachments/assets/13bd25d5-7403-4486-b84a-84a6de92ec7c" />

<br>
<br>
<br>
<br>

Leave these settings as shown with `Use an entire disk` and `Set up this disk as an LVM group` select. Encrypting would be useful for a production environment bun is not practical for this lab. <br>
Select `Done`.

<img width="2477" height="1360" alt="image" src="https://github.com/user-attachments/assets/e187c454-60f0-48ab-a425-b5a4f886b6c1" />

<br>
<br>
<br>
<br>

Expand the root logical volume. 

Use the arrow keys to highlight this line under Used Devices:
```
ubuntu-lv   new, to be formatted as ext4, mounted at /
```
Press Enter.
Choose Edit.

Change its size from approximately:
```
30.996G
```
to the maximum permitted value—approximately:
```
61.996G
```
Choose Save.

Select `Done` and then `Continue`.

<img width="2478" height="1362" alt="image" src="https://github.com/user-attachments/assets/5b9b3f89-4fbc-4263-8501-47c2274f9828" />
<br><br>
<img width="1149" height="499" alt="image" src="https://github.com/user-attachments/assets/a4c88916-8e2d-4ad2-b8d1-3c878eb4c139" />
<br><br>
<img width="2468" height="1357" alt="image" src="https://github.com/user-attachments/assets/ca749294-2efc-4814-9e4d-64c751ffc206" />
<br><br>
<img width="1174" height="402" alt="image" src="https://github.com/user-attachments/assets/44385147-aea0-4ea0-b685-a6e893e69a0d" />

<br>
<br>
<br>
<br>

<img width="2481" height="1356" alt="image" src="https://github.com/user-attachments/assets/0a795b2d-044c-4585-a7e1-45eab4b07ef6" />

<img width="2476" height="1358" alt="image" src="https://github.com/user-attachments/assets/88258f5a-8a27-4280-97ce-9a0ae163ebd6" />

Hit space bar on `Install OpenSSH server`. Then select done.

<img width="2483" height="1349" alt="image" src="https://github.com/user-attachments/assets/0dafc477-ad53-4b26-875c-4dad024542dd" />

<img width="2478" height="1359" alt="image" src="https://github.com/user-attachments/assets/a49e0503-61fe-46c8-8004-3d87ebedaee1" />

This will take a while.

<img width="2479" height="1356" alt="image" src="https://github.com/user-attachments/assets/24d3028e-e48c-4e56-b783-e5b97f2c51c3" />

<img width="2480" height="1361" alt="image" src="https://github.com/user-attachments/assets/8bdda3a4-37f8-4ea8-9611-7f4857ae477b" />

<img width="983" height="127" alt="Screenshot 2026-08-18 230508" src="https://github.com/user-attachments/assets/d474a966-4aff-46b8-a799-97332616fff5" />

The “failed unmounting” message does not mean the Ubuntu installation failed.

💿 Disconnect the ISO in VMware

While the VM remains on this screen:

In VMware’s top menu, select VM.
Select Removable Devices.
Select CD/DVD (SATA).
Choose Remove.

Then click inside the VM and press Enter.

<img width="1569" height="697" alt="image" src="https://github.com/user-attachments/assets/402d2e71-e954-4fbd-b0c7-0a65e74bef74" />

<img width="1493" height="1431" alt="image" src="https://github.com/user-attachments/assets/5b3e9700-bc2b-4d7a-894d-2c4d0f825877" />

<img width="692" height="138" alt="image" src="https://github.com/user-attachments/assets/9a3865a0-8e23-4bdd-9869-24a96871d052" />

👤 Enter your username first

Enter the username you created during Ubuntu installation, then press Enter.

Ubuntu will then display:

Password:

Enter the corresponding password and press Enter again.

<img width="1367" height="1197" alt="image" src="https://github.com/user-attachments/assets/8923473d-2c75-4902-a840-51557951be04" />

update
```
sudo apt update
sudo apt full-upgrade -y
```
<img width="1168" height="253" alt="image" src="https://github.com/user-attachments/assets/553be234-b824-4595-9ace-4cb9bcdb4493" />

When the updates finish, reboot:

sudo reboot

Log back in and then shut down cleanly:

sudo shutdown -h now

Wait until VMware shows the VM as powered off.

📸 Create the VMware snapshot
