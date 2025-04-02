# Technical Documentation: Azure Virtual Desktop (AVD) Implementation for Secure, Scalable Remote Access
 
## **1. Introduction**  
This document outlines the design and implementation of a secure, scalable Azure Virtual Desktop (AVD) environment optimized for a monthly budget of $50. The solution enables remote employees to access virtual desktops securely from any device while ensuring cost efficiency.
 
---
 
## **2. Design and Architecture**
 
### **2.1 Solution Overview**  
AVD provides a cloud-based virtual desktop infrastructure (VDI) with built-in scalability and security. Key components:  
- **Host Pool**: Manages session hosts (VM) delivering desktops/apps.  
- **Workspace**: Aggregates application group for user access.  
- **Application Groups**: Logical groupings of desktops or apps.  
- **Session Hosts**: Azure VM running Windows 11 multi-session OS.  
 
### **2.2 Architecture Diagram**  
![AVD Architecture]
*Components: Azure AD, AVD Host Pool, Session Hosts,Scaling Plans.*
 
### **2.3 Key Design Decisions**  
- **Cost Control**:  
  - **Burstable VMs (Ds)**: 2 vCPUs, 4 GiB RAM at $0.0832/hour.  
  - **Auto-Scaling**: Shut down VMs during off-peak hours.  
  - **Azure Spot VMs**: Optional for non-critical workloads (not used here due to reliability needs).  
  - **Premuim SDD Storage**: $0.045/GB/month for OS disks.  
 
- **Security**:  
  - **Microsoft Entra ID Integration**  
 
 
- **Scalability**:  
  - **Scaling Plans**: Automate VM start/stop based on schedule (e.g., 8 AM–6 PM).  
  - **Depth-First Load Balancing**: Prevents unnecessary overhead from running multiple underutilizeds VMs.  
 
---
 
## **3. Implementation Steps**
 
### **3.1 Prerequisites**  
- Azure subscription with contributor and User Access Administrator role.  
- Microsoft Entra ID tenant with users.  
- Windows 11 Enterprise multi-session image.
 
### **3.2 Virtual Network Setup**  
1.**Create Virtual Network**
-	Sign in to Azure Portal
-	Create a Virtual Network
-	In the search bar, type Virtual Network and select Virtual networks.
-	Click on + Create to start creating a new VNet.
- Configure Virtual Network Settings
-	Subscription – Choose your Azure subscription.
-	Resource Group – created one (cynthia-RG).	
-	Name –  Virtual Network a name (CY-AVD-VNET).
-	Region – Selected my region East US 2.	
-	Configure IP Addressing
-	Go to the IP Addresses tab.
-	Under IPv4 Address space, enter an address range (172.168.0.0/16).	
-	Click + Add a subnet, name it (CY-AVDHOST).
-	Review and Create
-	Click Review + Create.
-	After validation, click Create to deploy the Virtual Network.
  
![image](https://github.com/user-attachments/assets/2035d4dd-0c4a-47a3-b67c-62028b6b16a6)

![image](https://github.com/user-attachments/assets/8afb7f63-d067-4d56-aabb-36bf514af67e)

### **3.3 Host Pool Setup** 

2. **Create Host Pool**:  
-	Click + Create to start the process.
-	Step 3: Configure Host Pool Basics
-	Subscription – Select your Azure subscription(Visual studio subscription).
-	Resource Group – Choose an existing one or create a new one.
-	Host Pool Name – Enter a name (CynthiaPool).
-	Location – Choose the same region with the VN(East US 2).
-	Host Pool Type – Choose:
-	Pooled (multiple users share VMs)
-	Click Next: Virtual Machines.
-	Configure Virtual Machines (VMs)
-	Add Virtual Machines? – Select Yes to create session hosts.
-	Resource Group – Select the RG(cynthia-RG).
-	Virtual Machine Size – Choose a VM size based on performance needs(Standard D2ls v5).
-	Number of VMs – Set the number of VM for the pool(1).
-	Image – Select Windows 11 Multi-Session  OS.	
-	Administrator Credentials – Set a username and password for session hosts.
-	Click Next: Workspace(Skipped).
-	Click Next to Advance Tab and Next to Tag
-	Click Next: Review + Create.
  
![image](https://github.com/user-attachments/assets/1dbd2ee9-c7d5-4995-9f3f-ed459adc932d) 

![image](https://github.com/user-attachments/assets/ad9651a6-2cbc-4a25-9316-883368f47fb8)

![image](https://github.com/user-attachments/assets/49bf85af-c4df-4345-ad38-fadbb5e2331d) 

![image](https://github.com/user-attachments/assets/5ae6ff1d-d1c2-455f-a796-7022938d8b49)



 
### **3.4 Workspace and Application Groups**  
1. **Create Workspace**:  
   - Portal: **AVD** > **Workspaces** > Link to host pool.

![image](https://github.com/user-attachments/assets/a69b7a9f-8ba8-4c3d-8c79-b26a86b4295e)

2. **Desktop Application Group**:  
   - This automatically created once the Host Pool is setup.  
   - Assign users

### **3.5 -	Register Session Hosts to Host Pool** 
-	The AVD agent and bootloader are installed on session hosts.
-	The session hosts automatically register with the Host Pool.
-	You can check this under Azure Virtual Desktop → Host Pools → Session Hosts.

 
### **3.6 Assign Users to AVD Applications**  
- 	Go to Azure Virtual Desktop.
-	Click on Application Groups → Select your application group.
-	Under Assignments, click + Add and select the users who should have access.
-	Save changes.

### **3.7 Configure Access & Permissions**
-  At resource group level in Azure IAM give the user a VM user login role and VM Administrotor login.	
-	If using FSLogix for profile management, configure the storage for user profiles (e.g., Azure Files).

  ![image](https://github.com/user-attachments/assets/3bd71150-cf5a-4a57-b1fc-297ca29cadd7)


### **3.8 Configure Access & Permissions**
**Test AVD Connection**
-	Download the Remote Desktop Client or use AVD Web Client (https://rdweb.wvd.microsoft.com).
-	RDP for access-Micrsoft Entra single sign up configuration.
-	Sign in with an assigned user’s credentials.
-	Launch a session and verify everything works as expected.

![image](https://github.com/user-attachments/assets/7e96b877-1484-4cb0-b294-45a679c22dde)

![image](https://github.com/user-attachments/assets/3635d2eb-cdee-492c-b924-80226854b200)

![image](https://github.com/user-attachments/assets/5c01bde3-150d-4c11-b173-a013312cb1c2)



 
### **3.9 Cost Optimization**  
1. **Schedule VM Shutdown**: Use Azure Automation to deallocate VMs after hours.  
2. **Right-Sizing**: Monitor VM performance with Azure Metrics (CPU/RAM usage).  
3. **Storage**:  
   Use Standard HDD for OS disks
---
 
## **4. Cost Estimation**  
| **Component**         | **Cost/Month** |  
|------------------------|----------------|  
| 1x D2ls VM(176 hours)| $14.64         |  
| 64 GB OS Disk        | $2.88          |  
| Azure Files (50 GB)    | $3.00          |  
| Network Egress (10 GB) | $1.00          |  
| **Total**              | **$21.52**     |  

## **5. Challenges encountered** 

After setting up my first AVD host pool, the VM failed to connect to it. To resolve the issue, I deleted the session host, created a new VM, and the problem was successfully fixed.

![image](https://github.com/user-attachments/assets/1afe4031-3920-4470-bf1c-2e048763102f)


## **6. Conclusion**  
This implementation delivers a secure, scalable AVD environment within a $50/month budget. Key optimizations include burstable VMs, auto-scaling, and Azure AD security defaults. Future enhancements could include Azure Bastion for secure admin access (if budget increases).
