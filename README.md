# Wazuh-SIEM-Monitoring-Lab

## 📌 1. Overview
Wazuh is a free, open-source security platform used for:
- Log analysis  
- File integrity monitoring  
- Intrusion detection  
- Vulnerability detection  
- Real-time alerting  

## 🧩 2. Lab Architecture

| Component         | Host                  |       Role                                      |
|-------------------|-----------------------|-------------------------------------------------|
| **Wazuh Manager** | Ubuntu (VirtualBox)   | Collects, analyzes, and stores agent data       |
| **Wazuh Agent**   | Windows (Host)        | Sends logs and system events to Wazuh Manager   |

### 🔌 Network Setup
Use **Bridged Adapter** in VirtualBox → allows Ubuntu VM and Windows host to communicate on the same network.


## 🛠 3. Prerequisites
- VirtualBox installed  
- Ubuntu Server 20.04+ installed  
- Internet access in Ubuntu VM  
- Windows admin access  
- Basic Linux knowledge (optional)  

## 🚀 4. Installing the Wazuh Manager (Ubuntu)

### 4.1 Add the Wazuh GPG Key
```
  curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | sudo gpg --dearmor -o /usr/share/keyrings/wazuh-archive-keyring.gpg
```
## Command Explanation:

  curl -s → silently downloads the GPG key
  gpg --dearmor → converts key into a trusted format
  /usr/share/keyrings/ → stores trusted keys for package verification
  This ensures the Wazuh packages you install are legitimate.

### 4.2 Download & Execute the Wazuh Installation Script
```
  curl -sO https://packages.wazuh.com/4.12/wazuh-install.sh && sudo bash ./wazuh-install.sh -a -i
```

### Command Explanation:

  curl -sO → downloads the installation script
  bash ./wazuh-install.sh → runs the script
  -a → installs all components (Manager + Indexer + Dashboard)
  -i → interactive mode

This script automatically installs the Wazuh platform.

### 🌐 5. Access the Wazuh Dashboard

Check the VM IP:
```
ifconfig
```
### Command Explanation:

  Shows your Ubuntu network IP (usually 192.168.x.x)
  
Open a browser and go to:
```
        https://<ubuntu-vm-ip>
```
1.​ Accept any browser security warning due to the self-signed certificate.​
2.​ Log in using the credentials displayed at the end of the installation script.​

### 🖥 6. Installing the Wazuh Agent (Windows Host)
1.​ Download the latest Wazuh agent MSI installer from the official documentation:​
```
     https://documentation.wazuh.com/current/installation-guide/wazuh-agent/wazuh-agent-package-windows.html
```
2.​ Install the MSI package on your Windows system using the default settings.​

### 7. Registering the Agent with the Manager

### 7.1 Generate Agent Key on Ubuntu Manager
  Run the agent management utility:
  ```
    sudo /var/ossec/bin/manage_agents
```
  ●​ Select A to add an agent.​
  ●​ Assign a name (e.g., WindowsHost).​
  ●​ Leave IP address blank unless static assignment is needed.​
  ●​ After creation, select E to extract the key.​
  ●​ Copy the key output.​

### Command Explanation:

  manage_agents → registers agents, generates keys, removes agents
  
  The generated key links the Windows agent → Wazuh Manager securely
  
### 7.2 Apply Key in the Windows Agent
  1.​ Open Wazuh Agent Manager GUI from the Start Menu.
  2.​ Paste the copied key into the appropriate field.
  3.​ Save and apply the key.
  4.​ Add the manager's IP address (IP address of your Ubuntu manager).
  5.​ Restart the agent service.
You can then go to the WAZUH dashboard and see the agent onboarded.

### 8. File Integrity Monitoring on Windows
  Wazuh supports real-time monitoring of file and folder changes using Syscheck.
### 8.1 Edit Agent Configuration
  Open the following configuration file:
  ```
    C:\Program Files (x86)\ossec-agent\ossec.conf
```
  Add the following entry inside the Directory block:
  ```
    <directories realtime="yes">C:\Users\abc\Test</directories>
```
  This monitors the specified folder in real-time.

### Explanation:

  realtime="yes" → detect file changes instantly
  
  Folder path = the target you want to monitor

### 8.2 Restart the Agent
  After saving the changes, restart the Wazuh agent service to apply the configuration.
  ```
    Restart-Service -Name "Wazuh"
```

### 9. Verifying Setup

  1.​ Open the Wazuh Dashboard in your browser.​
  2.​ Navigate to Agents → ensure the Windows agent is listed and status is Active.​
  3.​ Go to the Integrity Monitoring section.​
  4.​ Perform actions (create/modify/delete files) in the monitored folder.​
  5.​ Confirm that alerts appear in the dashboard.
  
