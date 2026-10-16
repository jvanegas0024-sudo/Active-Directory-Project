# Active Directory Home Lab Project

## Project Overview
* This project documents the deployment and configuration of a fully functional Active Directory (AD) home lab environment built using VMware Workstation Pro. Designed to simulate a real-world enterprise network, the lab covers setting up a Windows Server 2022 Domain Controller (`DC01`), configuring custom DNS forwarding, establishing a structured Organizational Unit (OU) hierarchy, managing user accounts and security groups, applying targeted Group Policy Objects (GPOs), and joining Windows 11 client machines to the domain (`corp.xyz.com`).
## Tools Used
* VMware Workstation Pro
* Windows Server 2022
* Windows 11

 **Note:** The formatting, structure, and organization of these notes into Markdown were refined with the assistance of AI to improve clarity and readability. The raw, original notes have also been included in this repository for reference (`AD project.txt`).

---

## Installing and Setting Up Windows Server 2022
1. Create a virtual machine for the Windows server with 90 GB of storage, a 4-core CPU, and 4 GB of RAM.
   <img src="images/Installing%20and%20setting%20up%20Windows%20Sever%202022%20step%201.png" width="650" alt="Installing and setting up Windows Server 2022 step 1">
2. Name the VM `DC01`. Once installed, change its IP address to a static IP and ensure the hostname is set to `DC01` (a restart is required afterward).

    <img src="images/Installing%20and%20setting%20up%20Windows%20Sever%202022%20step%202.png" width="650" alt="Installing and setting up Windows Server 2022 step 2">
4. Update and restart the server.

    <img src="images/Installing%20and%20setting%20up%20Windows%20Sever%202022%20step%204.png" width="650" alt="Installing and setting up Windows Server 2022 step 4">

> **Note:** For the network settings of the VM, I used **Bridge mode** to give it direct access to my Local Area Network (LAN).

---

## Installing Windows 11 Machines
> **Note:** I am using Windows 11 since Windows 10 support has ended.

1. Create a VM with at least 2 CPU cores, 8 GB of RAM, 64 GB of storage, and a Virtual TPM 2.0.
2. When creating the VM, you will be prompted by the VMware wizard to configure encryption. Select **Only the files needed to support a virtual TPM**, create a password, and verify that the TPM is present under device settings when finished.

    <img src="images/Installing%20Windows%2011%20Machines%20step%202a.png" width="650" alt="Installing Windows 11 Machines step 2a">
   <img src="images/Installing%20Windows%2011%20Machines%20step%202b.png" width="650" alt="Installing Windows 11 Machines step 2b">
4. During Windows setup, select the **Windows 11 Education** operating system, as it supports joining an on-premises Active Directory domain and includes advanced management features. (*Note: Windows Home cannot join an Active Directory domain.*)
5. Once Windows setup is complete, clone the VM to create 3 additional VMs to simulate multiple endpoints on the network.
6. Follow a simple naming convention such as `D-PC-<1-4>`.
7. Change the hostnames of all four PCs accordingly.
   <img src="images/Installing%20Windows%2011%20Machines%20step%206.png" width="650" alt="Installing Windows 11 Machines step 6">

---

## Install Active Directory Domain Services (AD DS)
1. From the `DC01` server, install AD DS by clicking **Manage** > **Add Roles and Features**.

    <img src="images/Install%20Active%20Directory%20Domain%20Services%20step%201.png" width="650" alt="Install Active Directory Domain Services step 1">
3. For the installation type, select **Role-based or feature-based installation**.

    <img src="images/Install%20Active%20Directory%20Domain%20Services%20step%202.png" width="650" alt="Install Active Directory Domain Services step 2">
5. Ensure the destination server is set to `DC01`.

    <img src="images/Install%20Active%20Directory%20Domain%20Services%20step%203.png" width="650" alt="Install Active Directory Domain Services step 3">
7. Select the **Active Directory Domain Services** option. This will open an additional window identifying all dependent roles and features necessary for proper functionality.

    <img src="images/Install%20Active%20Directory%20Domain%20Services%20step%204.png" width="650" alt="Install Active Directory Domain Services step 4">
9. Leave the remaining features at their default settings.

    <img src="images/Install%20Active%20Directory%20Domain%20Services%20step%205.png" width="650" alt="Install Active Directory Domain Services step 5">
11. Under installation selections, check the box for **Restart the destination server automatically if required**, then click **Install**.
   > **Note:** In a live business environment, it is inappropriate to restart an operational server unless scheduled in advance so as not to disrupt services.
11. After installation is complete, restart the server.
12. Promote `DC01` to a domain controller.
13. Since this is a new domain, select **Add a new forest** and name it `corp.xyz.com`.
  
   <img src="images/Install%20Active%20Directory%20Domain%20Services%20step%209.png" width="650" alt="Install Active Directory Domain Services step 9">
14. Keep the defaults for the domain controller options and create a **Directory Services Restoration Mode (DSRM)** password. This is a critical security measure for recovery situations if the system enters a degraded state or maintenance is required.
   
    <img src="images/Install%20Active%20Directory%20Domain%20Services%20step%2010.png" width="650" alt="Install Active Directory Domain Services step 10">
15. For the NetBIOS name, use `CORPXYZ`. The NetBIOS name is used for backward compatibility and communication with legacy systems.

     <img src="images/Install%20Active%20Directory%20Domain%20Services%20step%2011.png" width="650" alt="Install Active Directory Domain Services step 11">
17. Keep the default paths. The `NTDS` folder stores the Active Directory database (containing directory objects, users, groups, policies, and system configurations) along with transaction log files.
  
    <img src="images/Install%20Active%20Directory%20Domain%20Services%20step%2012.png" width="650" alt="Install Active Directory Domain Services step 12">
18. Review all options to ensure everything is correct.
19. Once prerequisite checks pass, click **Install**.
  
    <img src="images/Install%20Active%20Directory%20Domain%20Services%20step%2014.png" width="650" alt="Install Active Directory Domain Services step 14">
20. At the end of the installation, the server will automatically restart.
  
    <img src="images/Install%20Active%20Directory%20Domain%20Services%20step%2015.png" width="650" alt="Install Active Directory Domain Services step 15">

---

## Users, Groups, and DNS Settings
1. For this homelab, configure a DNS forwarder pointing to your router so that `DC01` resolves local names like `corp.xyz.com`, while external requests (e.g., `google.com`) are forwarded by the router. Update the DNS IP address of all clients and servers to point to `DC01`.
 
   <img src="images/Users%20and%20Groups%20and%20DNS%20settings%20step%201.png" width="650" alt="Users and Groups and DNS settings step 1">
2. Configure `DC01`'s primary DNS to point to itself and all other VMs to point to `DC01`.

    <img src="images/Users%20and%20Groups%20and%20DNS%20settings%20step%202a.png" width="650" alt="Users and Groups and DNS settings step 2a">
   <img src="images/Users%20and%20Groups%20and%20DNS%20settings%20step%202b.png" width="650" alt="Users and Groups and DNS settings step 2b">
4. To create Organizational Units (OUs), navigate to **Server Manager** > **Tools** > **Active Directory Users and Computers**.
5. Create a top-level OU named `XYZCorporation`. This structure makes it easy to apply Group Policy Objects (GPOs) company-wide, such as password policies and baseline security settings.
6. Inside `XYZCorporation`, create the following sub-OUs:
   * **Administration:** To hold elevated domain and service accounts separately from regular users.
   * **Departments:** To group standard employee accounts by department (useful for mapping network drives and deploying targeted software).
   * **Workstations:** To separate client OS endpoints (laptops and desktops).
   * **Servers:** To keep server infrastructure isolated and apply stricter security policies.
   * **Groups:** To organize and centralize security groups.

    <img src="images/Users%20and%20Groups%20and%20DNS%20settings%20step%205.png" width="650" alt="Users and Groups and DNS settings step 5">
8. Create a user named **Jane Doe** and place them in the IT department OU.

    <img src="images/Users%20and%20Groups%20and%20DNS%20settings%20step%206a.png" width="650" alt="Users and Groups and DNS settings step 6a">
   <img src="images/Users%20and%20Groups%20and%20DNS%20settings%20step%206b.png" width="650" alt="Users and Groups and DNS settings step 6b">
   <img src="images/Users%20and%20Groups%20and%20DNS%20settings%20step%206c.png" width="650" alt="Users and Groups and DNS settings step 6c">
10. Create a security group named `GRP_IT_STAFF`(images show ADMIN because I changed it) and add the user Jane Doe to it.
 
   <img src="images/Users%20and%20Groups%20and%20DNS%20settings%20step%207a.png" width="650" alt="Users and Groups and DNS settings step 7a">
   <img src="images/Users%20and%20Groups%20and%20DNS%20settings%20step%207b.png" width="650" alt="Users and Groups and DNS settings step 7b">
11. Create and link a GPO to the IT OU by navigating to **Server Manager** > **Tools** > **Group Policy Management**, expanding the domain to locate the IT OU, right-clicking, and selecting **Create a GPO in this domain, and Link it here**.
  
   <img src="images/Users%20and%20Groups%20and%20DNS%20settings%20step%208.png" width="650" alt="Users and Groups and DNS settings step 8">
12. Name the GPO `IT-Restricted-Settings`. Under its security filtering tab, remove **Authenticated Users** and add the `GRP_IT_STAFF` group as well as **Domain Admins**.
  
   <img src="images/Users%20and%20Groups%20and%20DNS%20settings%20step%209.png" width="650" alt="Users and Groups and DNS settings step 9">

---

## Adding Machines to the Domain
1. Power on the Windows 11 VM and navigate to **Settings** > **System** > **Domain or workgroup** > **Change**.
2. Click on **Domain**, enter your domain name (`corp.xyz.com`), and provide credentials for an account with permissions to join computers to the domain.
   <img src="images/Adding%20machines%20step%202.png" width="650" alt="Adding machines step 2">
