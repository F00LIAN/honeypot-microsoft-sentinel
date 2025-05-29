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
- ### [YouTube: How to Create an At-Home Security Operations Center Lab](https://www.youtube.com/watch?v=YwbTSAbr2Cs)

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

## Configuration Steps

### 1. Create Resource Group, Virtual Network, and VM in Azure
<p>
<img src="https://github.com/user-attachments/assets/4d18d3e3-d73b-4561-8b10-505968de5c82" height="60%" width="60%" alt="Azure Resource Setup"/>
</p>

- Set up an **Azure Resource Group** for project organization.
- Create a **Virtual Network (VNet)** and ensure it’s in the same region as the VM.
- Deploy a **Windows 10 Virtual Machine** in Azure.
- Open all ports in the **Network Security Group (NSG)** for lab testing (**Note: This is dangerous in a real-world scenario**).
- Create an **inbound security rule** that allows unrestricted traffic (**again, not recommended in production**).

### 2. Disable Firewall Rules in Windows 10 VM
<p>
<img src="https://github.com/user-attachments/assets/d956d59c-c87b-4918-82d6-e8bf8a146c09" height="60%" width="60%" alt="Turning Off Firewall"/>
</p>

- Log in to the VM, open the **Start Menu**, and search for `wf.msc`.
- Navigate to **Windows Defender Firewall Properties** and disable **Firewall State** for Domain, Private, and Public profiles.
- Open **PowerShell** on your local machine and ping the VM’s IP address to verify connectivity.

### 3. Viewing Security Logs in the VM and Configuring Log Analytics
<p>
<img src="https://github.com/user-attachments/assets/f99af185-e30d-40f1-be0e-048fa2cb2801" height="60%" width="60%" alt="Event Viewer"/>
</p>

- Open **Event Viewer** in the VM.
- Navigate to **Windows Logs → Security** to observe security-related events.
- **Event ID 4625** represents a failed login attempt.
- After some time, multiple failed login attempts will appear.
- On Azure, create a **Log Analytics Workspace** in the same **Resource Group and Region** as your VM.

### 4. Configure Microsoft Sentinel and Connect Log Analytics Workspace
<p>
<img src="https://github.com/user-attachments/assets/1b8b6c95-5e85-480f-937d-1715cd8bc3d0" height="60%" width="60%" alt="Connecting to Sentinel"/>
</p>

- In **Microsoft Sentinel**, connect the **Log Analytics Workspace** created in the previous step.

## Deployment Steps

### 5. Configure the Event Connector for VM and Log Analytics Workspace
<p>
<img src="https://github.com/user-attachments/assets/92a39ece-2ada-4833-9068-c4aa1d457c42" height="60%" width="60%" alt="Install Programs"/>
</p>

- In **Sentinel**, install the **Windows Security Events** add-on from the **Content Hub**.
- Click **Manage**, then add the **Windows Security Events via AMA** connector.
- Create a **Data Collection Rule (DCR)** to send logs from the VM to **Log Analytics Workspace**.
- Select the VM and leave other settings as default.

### 6. Querying Log Repository using KQL
<p>
<img src="https://github.com/user-attachments/assets/644a5aea-1912-4d86-b541-41749943eab7" height="60%" width="60%" alt="Return an instance with columns"/>
</p>

```kql
SecurityEvent 
| where Account == "\\FAGNER" 
| project TimeGenerated, Account, Computer, EventID, Activity, IpAddress
```

- **Bonus:** Count how many attacks have occurred in the last 5 minutes.
- **Bonus:** Lookup attacker **IP Address** information.

### 7. Uploading Attackers’ IPs to a Geospatial Map
<p>
<img src="https://github.com/user-attachments/assets/ed9e0be0-3ab3-4a00-92c9-e3ff74017fe7" height="60%" width="60%" alt="geoip"/>
</p>

- Download [GeoIP Database](https://drive.google.com/file/d/13EfjM_4BohrmaxqXZLB5VUBIz2sv9Siz/view?usp=sharing).
- In **Sentinel**, navigate to **Configuration → Watchlist**, and create a watchlist named `geoip`.
- Set `searchkey` as `network` and upload the database file.

### 8. Initialize the Geo IP Map on Microsoft Sentinel with a Query
<p>
<img src="https://github.com/user-attachments/assets/bbcb451f-49f3-40fa-ae24-1ab0cb7ca8ea" height="60%" width="60%" alt="geoip_query"/>
</p>

```json
{
	"type": 3,
	"content": {
	"version": "KqlItem/1.0",
	"query": "let GeoIPDB_FULL = _GetWatchlist(\"geoip\");\nlet WindowsEvents = SecurityEvent;\nWindowsEvents | where EventID == 4625\n| order by TimeGenerated desc\n| evaluate ipv4_lookup(GeoIPDB_FULL, IpAddress, network)\n| summarize FailureCount = count() by IpAddress, latitude, longitude, cityname, countryname\n| project FailureCount, AttackerIp = IpAddress, latitude, longitude, city = cityname, country = countryname,\nfriendly_location = strcat(cityname, \" (\", countryname, \")\");",
	"size": 3,
	"timeContext": {
		"durationMs": 2592000000
	},
	"queryType": 0,
	"resourceType": "microsoft.operationalinsights/workspaces",
	"visualization": "map",
	"mapSettings": {
		"locInfo": "LatLong",
		"locInfoColumn": "countryname",
		"latitude": "latitude",
		"longitude": "longitude",
		"sizeSettings": "FailureCount",
		"sizeAggregation": "Sum",
		"opacity": 0.8,
		"labelSettings": "friendly_location",
		"legendMetric": "FailureCount",
		"legendAggregation": "Sum",
		"itemColorSettings": {
		"nodeColorField": "FailureCount",
		"colorAggregation": "Sum",
		"type": "heatmap",
		"heatmapPalette": "greenRed"
		}
	}
	},
	"name": "query - 0"
}
```

- In **Sentinel**, create a new **workbook**, delete the default items, and add a new workbook with the above insert **query**.

### 8. Mapping Attackers Using KQL and Geolocation
<p>
<img src="https://github.com/user-attachments/assets/4f91675b-73fd-4f88-bfe8-77c2b8096e82" height="60%" width="60%" alt="geoip_query"/>
</p>

```kql
let GeoIPDB_FULL = _GetWatchlist("geoip");
let WindowsEvents = SecurityEvent;
WindowsEvents | where EventID == 4625
| order by TimeGenerated desc
| evaluate ipv4_lookup(GeoIPDB_FULL, IpAddress, network)
| summarize FailureCount = count() by IpAddress, latitude, longitude, cityname, countryname
| project FailureCount, AttackerIp = IpAddress, latitude, longitude, city = cityname, country = countryname,
friendly_location = strcat(cityname, " (", countryname, ")");
```

- Once the **workbook** adds the geomap, go ahead and edit the map and input the above **query**.

## Conclusion
Deploying a **honeypot in Azure** allows real-world threat monitoring, log analysis, and proactive cybersecurity research. This project is ideal for IT professionals looking to strengthen security expertise and gain hands-on experience with **Microsoft Sentinel** and **KQL**.

This lab serves as a foundational project for anyone pursuing cybersecurity, **blue team operations**, or **threat intelligence analysis**. Understanding attacker behavior is crucial for building **proactive defense mechanisms** in real-world enterprise environments. 

By experimenting in a **controlled environment**, you can improve your ability to detect, mitigate, and respond to cyber threats, strengthening your security expertise in a practical and engaging way.

