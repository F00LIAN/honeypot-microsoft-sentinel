<p align="center">
<img src="https://github.com/user-attachments/assets/757ba505-315f-431e-bfc5-c320405709a7" height="50%" width="50%" alt="Microsoft Active Directory Logo"/>
</p>
# Setting Up a Honeypot for Public Attackers using Azure

## Introduction
Cybersecurity threats are on the rise, and organizations must continuously evolve their defensive strategies. One effective method for understanding potential attackers is by deploying a **honeypot**—a system designed to attract malicious actors and monitor their behavior. In this project, we leverage **Microsoft Azure** to create a controlled environment that logs attack attempts, identifies threat actors, and visualizes malicious activity using **Microsoft Sentinel**. 

By following this guide, you will:
- Set up an **Azure-based honeypot** to analyze real-world cyber threats.
- Configure **Active Directory, Log Analytics, and Microsoft Sentinel** for security monitoring.
- Use **Kusto Query Language (KQL)** to investigate attack patterns.
- Visualize attacker geolocations on a **geospatial map**.

This tutorial is designed for cybersecurity enthusiasts, IT professionals, and anyone looking to strengthen their security skills by **actively engaging with live security threats** in a safe environment.

---

## Familiar Use Case
You are **a cybersecurity analyst** or **an IT professional** looking to understand how attackers operate. By setting up this honeypot, you can gain hands-on experience in threat detection, log analysis, and security monitoring using industry-standard tools like **Microsoft Sentinel**.

## Video Demonstration
- ### [YouTube: How to Create an At-Home Security Operations Center Lab](https://www.youtube.com)

## Environments and Technologies Used
- **Microsoft Azure** (Virtual Machines/VNet/Log Analytics/Sentinel)
- **Remote Desktop Protocol (RDP)**
- **Active Directory Domain Services (ADDS)**
- **PowerShell**

## Operating Systems Used
- **Windows 10 (21H2)**

## High-Level Deployment and Configuration Steps
<p>
<img src="https://github.com/user-attachments/assets/7ab59036-9be8-4609-9df5-043481597aef" height="60%" width="60%" alt="High Level Overview"/>
</p>

1. **Create Azure Resources**
2. **Configure Network and Firewall Settings**
3. **Install and Configure Active Directory Domain Services**
4. **Join the Client to the Domain and Manage Objects**
5. **Configure Security Logs and Monitor Attacker Activity**
6. **Visualize Attacks in Sentinel with Geospatial Mapping**

<h2>Configuration Steps</h2>

### 1. Create Resource Group, Virtual Network, and VM in Azure
<p>
<img src="https://github.com/user-attachments/assets/4d18d3e3-d73b-4561-8b10-505968de5c82" height="60%" width="60%" alt="High Level Overview"/>
</p>
<p>

- Set up an **Azure Resource Group** for project organization.
- Create a **Virtual Network (VNet)** and ensure it’s in the same region as the VM.
- Deploy a **Windows 10 Virtual Machine** in Azure.

</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/cd1f8698-5f5f-4438-bb90-e89c92f25d20" height="60%" width="60%" alt="Azure VM image"/>
</p>
<p>

- Open all ports in our Network Security Group for our lab (Dangerous in real life).

- Create an inbound security rule that allows any traffic, from any source, any protocol, any action, etc.

</p>
<br />

### 2. Login to VM and Drop VM Firewall Rules in Windows 10.
<p>
<img src="https://github.com/user-attachments/assets/d956d59c-c87b-4918-82d6-e8bf8a146c09" height="60%" width="60%" alt="Turning Off Firewal"/>
</p>
<p>

- Login to VM and navigate to start menu and enter "wf.msc"

- Navigate to "Windows Defender FIrewall Properties" and turn off "Firewall State" for Domain Profile, Private Profile, Public Profile, and IPsec Settings.

- Go back to powershell on your local device and ping the ip-address of the VM. The result should have a successful connectivity as now anyone can reach the VM. 

</p>
<br />

### 3. Viewing Logs in the VM and Configure a Log Repository in Azure
<p>
<img src="https://github.com/user-attachments/assets/f99af185-e30d-40f1-be0e-048fa2cb2801" height="60%" width="60%" alt="Event Viewer"/>
</p>
<p>

- Log back into the VM and go to 'Event Viewer' in the VM.

- Navigate to the 'Windows Logs' folder and 'security' to witness the security events on the laptop.

- Notice Event ID represents the type of event that has taken place. 4625 = A failure to login to windows machine.

- After awhile you will notice a plethora of failed login attempts to the VM.

</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/f826be0b-8bdf-48c0-890a-35aab232a8d9" height="60%" width="60%" alt="Create LAW"/>
</p>
<p>

- On Azure, go to Log Analytics Workspace and create one under the same resource group and region.

</p>
<br />

### 4. Configure Microsoft Sentinel (SIEM) and Connect Log Analytics Workspace

<p>
<img src="https://github.com/user-attachments/assets/1b8b6c95-5e85-480f-937d-1715cd8bc3d0" height="60%" width="60%" alt="Connecting to Sentinel"/>
</p>
<p>

- Go to Microsoft Sentinel and Connect the Newly added Log Analytics Workspace to the Sentinel Instance.

</p>
<br />

<h2>Deployment Steps</h2>

### 5. Configure the Event Connector from the VM and Log Analytics Workspace. 
<p>
<img src="https://github.com/user-attachments/assets/92a39ece-2ada-4833-9068-c4aa1d457c42" height="60%" width="60%" alt="Install Programs"/>
</p>
<p>

- In the sentinel instance, go to content hub and install the "Windows Security Events" add on.

- Once installed click "Manage" and add the "Windows Security Events via AMA" to open the connector page.

- Create a data collection rule for our resource group and VM to share log analytics with the Log Analytics Workspace.

- In the rule, select our VM and leave everything else default. Click create, notice in our VM the extensions and applications being added.

</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/896d6888-5d32-48ef-a9f3-c56e50518516" height="60%" width="60%" alt="Log Workspaces"/>
</p>
<p>

- Verify logs are now being shared to the Log Analytics Workspace. Go to the resource group and select our LAW.

- Under our LAW, select logs and run the query "SecurityEvent" to witness the logs now being shared from our VM.

</p>
<br />

### 5. Querying our Log Repository using KQL
<p>
<img src="https://github.com/user-attachments/assets/644a5aea-1912-4d86-b541-41749943eab7" height="60%" width="60%" alt="Return an instance with columns"/>
</p>
<p>

- Run the query SecurityEvent | where Account == "\\FAGNER" | project TimeGenerated, Account, Computer, EventID, Activity, IpAddress

- Bonus: See how many attacks have happened in the last 5 minutes. 

- Bonus: Lookup the IPAddress from our attacker. 

</p>
<br />

### 6. Uploading the Different Attackers to a Geospatial Map

<p>
<img src="https://github.com/user-attachments/assets/ed9e0be0-3ab3-4a00-92c9-e3ff74017fe7" height="60%" width="60%" alt="geoip"/>
</p>
<p>

- Download the following file that summarizes IP Address Ranges and Specific Locations. [Drive File](https://drive.google.com/file/d/13EfjM_4BohrmaxqXZLB5VUBIz2sv9Siz/view?usp=sharing)

- Go to our Sentinel instance and go to configuration --> Watchlist --> create an watchlist with the name and alias 'geoip' and a import the source file we downloaded.

- Enter searchkey as 'network' and create.

</p> 
<br />

<p>
<img src="https://github.com/user-attachments/assets/fc9f9ee4-31f9-4ada-aff4-d76e0ead950d" height="60%" width="60%" alt="geoip on LAW"/>
</p>
<p>

- Once fully uploaded, lets examine some queries in the Log Analytics Workspace.

- If we run the query '_GetWatchlist("geoip")' we notice that we see the geoip file we uploaded in our Sentinel instance. 

</p> 
<br />

<p>
<img src="https://github.com/user-attachments/assets/02601115-c921-403a-8181-830c927b5181" height="60%" width="60%" alt="Running KQL query against our new tables"/>
</p>
<p>

- Test to see our attackers locations against our login failures.
    
    let GeoIPDB_FULL = _GetWatchlist("geoip");
    let WindowsEvents = SecurityEvent
    | where IpAddress == <attacker IP address> 
    | where EventID == 4625
    | order by TimeGenerated desc
    | evaluate ipv4_lookup(GeoIPDB_FULL, IpAddress, network);
    WindowsEvents 
    | project TimeGenerated, Computer, AttackerIp = IpAddress, cityname, countryname, latitude, longitude
    
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/de38187b-0fdd-4a10-a398-156035838585" height="60%" width="60%" alt="Creating the Map"/>
</p>
<p>

- Go to our Sentinel instance and add a workbook.

- Follow the link and add our map.json to the workbook: [Drive File](https://drive.google.com/file/d/1ErlVEK5cQjpGyOcu4T02xYy7F31dWuir/view?usp=drive_link)

- Under advanced editor, erase everything, and copy/paste the map.json inside. Then save. 
</p>
<br />

## Conclusion
Deploying a **honeypot in Azure** is a powerful way to observe real-world cyber threats in action. By setting up **Microsoft Sentinel**, configuring **log analytics**, and leveraging **Kusto Query Language (KQL)**, we gain invaluable insights into potential attack vectors and malicious actors.

Through this project, we:
- **Monitored real-world attack attempts** and analyzed unauthorized login attempts.
- **Configured Azure Sentinel** to collect security logs and track malicious activities.
- **Queried log data with KQL** to extract meaningful threat intelligence.
- **Mapped attacker locations** using geospatial visualization.

This lab serves as a foundational project for anyone pursuing cybersecurity, **blue team operations**, or **threat intelligence analysis**. Understanding attacker behavior is crucial for building **proactive defense mechanisms** in real-world enterprise environments. 

By experimenting in a **controlled environment**, you can improve your ability to detect, mitigate, and respond to cyber threats, strengthening your security expertise in a practical and engaging way.

