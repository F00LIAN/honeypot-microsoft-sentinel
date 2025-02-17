<p align="center">
<img src="https://github.com/user-attachments/assets/757ba505-315f-431e-bfc5-c320405709a7" height="50%" width="50%" alt="Microsoft Active Directory Logo"/>
</p>

<h1>Setting Up Honeypot for Public Attackers using Azure</h1>
This <br />

<h1>Familiar Use Case</h1>
You are<br />

<h2>Video Demonstration</h2>

- ### [YouTube: How to Deploy on-premises Active Directory within Azure Compute](https://www.youtube.com)

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>High-Level Deployment and Configuration Steps</h2>

1. Create Azure Resources

    - Create a dedicated Resource Group (e.g., "active-directory-lab")

    - Create a Virtual Network (e.g., "active-directory-vnet") in your preferred region (East US)

    - Provision two Azure Virtual Machines:
        1. DC-1: Windows Server 2022 (to be the Domain Controller)
        2. Client-1: Windows 10 (to be the domain-joined client)
  
2. Configure Network and Firewall Settings

    - Set a Static Private IP address for the Domain Controller’s Network Interface
    
    - Disable the Windows Firewall on the Domain Controller (for lab/demonstration purposes)
    
    - On Client-1, change the DNS server to point to the Domain Controller’s private IP address

3. Install and Configure Active Directory Domain Services

    - Install the "Active Directory Domain Services" server role on DC-1
    
    - Promote DC-1 to a Domain Controller and create a new forest (e.g., mydomain.com)
    
    - Restart DC-1 and log in to the new domain (e.g., mydomain.com\labuser)

4. Join the Client to the Domain and Manage Objects

    - Create a Domain Admin user (e.g., jane_admin) and assign Domain Admins membership
    
    - Join Client-1 to the new domain (mydomain.com)
    
    - Use PowerShell to create additional users in bulk
    
    - Configure Account Lockout, enable/disable accounts, and observe security logs for user events

<h2>Configuration Steps</h2>

### 1. Create Resource Group and Virtual Network
<p>
<img src="https://github.com/user-attachments/assets/e561a859-d224-4ab0-aa21-a8ad9e9655fb" height="60%" width="60%" alt="Azure VM image"/>
</p>
<p>

- Create a Windows 10 virtual machine in Microsoft Azure.
  
- Use a dedicated resource group "active-directory-lab" for connection purposes.

- Create a virtual network under the new resource group and call it "active-directory-vnet" under East US.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/ff64c3f6-01c4-4cb2-b457-e5ddfef53836" height="60%" width="60%" alt="Azure VM image"/>
</p>
<p>

- Create a Windows 10 virtual machine in Microsoft Azure, call it "DC-1" under East US.
  
- Make sure to use the dedicated resource group "active-directory-lab".

- For image use "Windows Server 2022 Datacenter: Azure Edition". Make sure to enable licensing and RDP/SSH.

- Under Networking make sure to select the Active-Directory-Vnet we created. Then Create. 
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/d1c31d13-9136-410e-90f1-4b9ccb6b089a" height="60%" width="60%" alt="Azure VM image"/>
</p>
<p>

- Create a Windows 10 virtual machine in Microsoft Azure, call it "client-1" under East US.
  
- Make sure to use the dedicated resource group "active-directory-lab".

- For image use "Windows 10 Pro". Make sure to enable RDP/SSH and licensing.

- Under Networking make sure to select the Active-Directory-Vnet we created. 
</p>
<br />

### 2. Create VM and Configure Firewall Rules
<p>
<img src="https://github.com/user-attachments/assets/f56d858f-5145-4693-9f45-e0265931976a" height="60%" width="60%" alt="DNS IP Addressing"/>
</p>
<p>

- On Azure Portal:
  - Go to VM → dc-1 → Networking → Click on the NIC → ipconfig1.
  - Set the Private IP address to Static.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/71becd86-5d4a-4a01-837b-3d13998dab19" height="60%" width="60%" alt="DNS IP Addressing"/>
</p>
<p>

- On DC-1 (inside the VM):
  - Press Windows Key + R, type wf.msc to open Windows Firewall.
  - Click Properties → Turn off firewall (for all profiles) → Apply.
</p>
<br />

### 3. Login to New VM and Disable Windows Firewall
<p>
<img src="https://github.com/user-attachments/assets/6c7cb46a-da29-4583-ba88-c79a0ceac911" height="60%" width="60%" alt="Set Client 1 DNS"/>
</p>
<p>

- On Azure Portal for Client-1:
  - Go to VM → client-1 → Networking → Click on the NIC → DNS Servers.
  - Change from Inherit from Virtual Network to Custom, and paste dc-1’s private IP.
  - Restart the client-1 VM.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/aa8ad062-7c0e-457a-9e1c-67117e122ebd" height="60%" width="60%" alt="Set Client 1 DNS"/>
</p>
<p>
  
- RDP into client-1 and open a terminal:
  - Ping dc-1’s private IP address to ensure connectivity. 
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/aa4bbfca-8719-4f3d-a6d8-0a539f12fbf8" height="60%" width="60%" alt="Set Client 1 DNS"/>
</p>
<p>
  
- Run ipconfig /all
  - Confirm the DNS Server is set to dc-1’s IP address.
</p>
<br />

<h2>Deployment Steps</h2>

### 4. Viewing Raw Logs on Event Viewer
<p>
<img src="https://github.com/user-attachments/assets/ee8dc31c-0585-49c7-8c35-abc84f4e53c8" height="60%" width="60%" alt="Install Programs"/>
</p>
<p>
  
- On DC-1, open Server Manager:
  - Click Add Roles and Features → Check Active Directory Domain Services → Install.
  - After installation, click Promote this server to a domain controller → Add a new forest (e.g., mydomain.com) → Set a DS Restore Mode password → Install.
  - Restart DC-1 and log back in using mydomain.com\labuser.
</p>
<br />

### 5. Connect VM to Log Analytics Workspace
<p>
<img src="https://github.com/user-attachments/assets/44bf5b74-f26a-4baa-9c56-c92f061d0faa" height="60%" width="60%" alt="Organizational Units"/>
</p>
<p>
  

</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/ab8d0246-aae9-4120-9ceb-208df7542689" height="60%" width="60%" alt="Create Domain User"/>
</p>
<p>
  
- Under the _ADMINS OU, create a new user jane_admin.
- Set a password, then go to Properties → Member Of → Add Domain Admins group.
- Log out and log back in as mydomain.com\jane_admin.
</p>
<br />

### 6. Run Different Queries and Notice Attackers
<p>
<img src="https://github.com/user-attachments/assets/a3639515-7f99-4ced-bb63-d1d841332cf8" height="60%" width="60%" alt="Register PHP Version on IIS Manager"/>
</p>
<p>
let GeoIPDB_FULL = _GetWatchlist("geoip");
let WindowsEvents = SecurityEvent
| where IpAddress == "185.156.73.169"
| where EventID == 4625
| order by TimeGenerated desc
| evaluate ipv4_lookup(GeoIPDB_FULL, IpAddress, network);
WindowsEvents 
| project TimeGenerated, Computer, AttackerIp = IpAddress, cityname, countryname, latitude, longitude

</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/c3c9f9d5-27bb-4ca5-9c6c-36b1527dcb25" height="60%" width="60%" alt="Domain Client Check"/>
</p>
<p>
  
- On DC-1, open Active Directory Users and Computers.
  - Check under Computers; you should see client-1.
  - Optionally create a new OU called _CLIENTS and move client-1 there.
</p>
<br />

<h2>Creating Users using Powershell</h2>

### 7. Create Map of Attacker Areas
<p>
<img src="https://github.com/user-attachments/assets/629197ca-429a-4a70-b3ea-e9312c938186" height="60%" width="60%" alt="Installation Steps"/>
</p>
<p>

- On Client-1, log in as mydomain.com\jane_admin.
  - Open System Properties → Remote Desktop → Select users who can remotely access this PC → Add Domain Users from mydomain.com.
</p>
<br />

### 8. Create a Bunch of Additional Users and Attempt to Log into Client-1 with one of the Users
<p>
<img src="https://github.com/user-attachments/assets/a729dc27-6d29-41cb-9f43-3e68efb38c90" height="60%" width="60%" alt=""/>
</p>
<p>
  
- On DC-1, log in as jane_admin.
  - Open PowerShell ISE as administrator.
  - Paste the content of the Generate-Names-Create-Users.ps1 script.
  - Run the script to bulk-create user accounts in your domain.
</p>
<br />

<h2>Group Policy and Managing Accounts</h2>

### 9. Dealing with Account Lockouts
<p>
<img src="https://github.com/user-attachments/assets/92ace8a8-7f9f-4e95-95f1-0c3cd50f11dd" height="60%" width="60%" alt=""/>
</p>

- On DC-1, open Active Directory Users and Computers.
  - Pick a random account in _EMPLOYEES to test.

- Press Windows Key + R, type gpmc.msc to open the Group Policy Management Console.
  - Edit the Default Domain Policy → Computer Configuration → Policies → Windows Settings → Security Settings → Account Policies → Account Lockout Policy.
  - Set:
      - Account lockout duration: 30 minutes
      - Account lockout threshold: 5 invalid attempts

- On Client-1, attempt to log in with the chosen user and deliberately enter the wrong password multiple times to lock the account.
<p>
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/4b9db056-6dbc-4949-8364-c2db0cf71133" height="60%" width="60%" alt="unlock account"/>
</p>
<p>
  
- Back on DC-1, open Active Directory Users and Computers.
  - Find the locked user → Properties → Account tab → Unlock Account.
  - Optionally reset their password.
  - Attempt to log in again on Client-1 with the correct credentials. 
</p>
<br />

### 10. Enabling and Disabling Accounts
<p>
<img src="https://github.com/user-attachments/assets/ea297579-54db-428e-94ff-ef8264175e52" height="60%" width="60%" alt="enabling account"/>
</p>
<p>
  
- On DC-1, choose a user account → Right-click → Disable Account.
- Attempt to log in on Client-1 with that user and note that it fails.
- Back on DC-1, Enable the same user account.
- Try logging in again, which should now succeed.
</p>
<br />

### 11. Obeserve the Logs 
<p>
<img src="https://github.com/user-attachments/assets/034d2401-69a2-4448-b974-2bca759fff8f" height="60%" width="60%" alt=""/>
</p>
<p>
  
- On DC-1, press Windows Key + R, type eventvwr.msc to open Event Viewer.
  - Go to Windows Logs → Security → Search for events related to the chosen user.

- On Client-1, you can also open Event Viewer to observe successful or failed login attempts from the local perspective.
</p>
<br />

### 12. Conclusion
You have successfully deployed a basic on-premises style Active Directory lab inside Azure, created domain administrator and standard user accounts, joined a Windows 10 client to the domain, and tested various Active Directory features such as account lockout policies, enabling/disabling accounts, and examining security logs.

<h2>Next Steps for Exploring Active Directory Further</h2>

1. Advanced Group Policy
    - Configure specific Group Policies for software deployment, folder redirection, or workstation security settings.
    - Explore creating and linking multiple GPOs to different Organizational Units.

2. DNS and DHCP Integration
    - Set up DHCP on your Domain Controller or another server to manage IP addressing in a more dynamic environment.
    - Explore advanced DNS configurations, like conditional forwarders and DNS zone replication.

3. Replication and Sites
    - Experiment with adding another Domain Controller in a different Azure region or on-premises location.
    - Configure Sites and Subnets in Active Directory to optimize replication.

4. Certificate Services
    - Install and configure Active Directory Certificate Services to issue certificates for users and machines.
    - Explore how certificates can secure RDP sessions, web servers, or wireless networks.

5. Multi-Domain / Multi-Forest Environments
    - Investigate creating multiple domains and establishing trust relationships.
