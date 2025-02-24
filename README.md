<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Create Resources
- Ensure Connectivity between the client and Domain Controller
- Install Active Directory
- Create an Admin and Normal User Account in AD
- Join Client-1 to your domain (mydomain.com)
- Setup Remote Desktop for non-administrative users on Client-1
- Create additional users and attempt to log into client-1 with one of the users

<h2>Deployment and Configuration Steps</h2>
<br />
<br />
<h3 align="center">Setup Resources in Azure</h3>
<br />
<p>
  Create the Domain Controller VM (Windows Server 2022) named “DC-1”:
</p>
<p>
  
![create DC-1](https://github.com/user-attachments/assets/eb8c14f8-cd21-4ff5-ab59-2dfd1b44c8d7)

</p>
<p>
  Create the Client VM (Windows 10) named “Client-1”. Use the same Resource Group and Vnet that was created in previous step:
</p>
<p>
  
![create client-1](https://github.com/user-attachments/assets/ec364521-01bf-4798-b6dd-3cf0afe0ff34)

</p>
<p>
  Set Domain Controller’s NIC Private IP address to be static:
</p>
<p>
  
![change to static](https://github.com/user-attachments/assets/0a7bad58-0efd-4fee-a1d8-16b520b6de7d)

</p>
<p>
  Ensure that both VMs are in the same Vnet (you can check the topology with Network Watcher):
</p>
<p>
  
  ![check they are on same vnet](https://github.com/user-attachments/assets/48f17f17-ac3d-42f4-a60d-20d372899efd)

</p>
<br /> 
<br />
<h3 align="center">Ensure Connectivity between the client and Domain Controller</h3>
<br />
<p>
  Login to Client-1 with Remote Desktop and ping DC-1’s private IP address with ping -t <ip address> (perpetual ping):
</p>
<p>
  
![ping dc1](https://github.com/user-attachments/assets/df32b6b2-ed37-4bae-865c-3bf20f15bdfc)

</p>
<p>
</p>
<br />
<br />
<h3 align="center">Install Active Directory</h3>
<br />
<p>
  Login to DC-1 and install Active Directory Domain Services:
</p>
<p>
  
  ![install active directory](https://github.com/user-attachments/assets/f2be4e2e-2cd3-4daf-ac3d-34130ba06c5a)

</p>
<p>
</p>
<p>
  Setup a new forest as myactivedirectory.com (can be anything, just remember what it is - I ultimately did set it up as myadproject.com which you'll see in the next pic):
</p>
<p>
  
![make a forest](https://github.com/user-attachments/assets/e944bc28-9505-4753-8920-c6d5cc8672f6)

</p>
<p>
  Restart and then log back into DC-1 as user: mydomain.com\labuser:
</p>
<p>
  
![log in as jane admin](https://github.com/user-attachments/assets/08355e26-883c-4ad0-ae1f-1e735f00a1a7)

</p>
<br />
<br />
<h3 align="center">Create an Admin and Normal User Account in AD</h3>
<br />
<p>
  In Active Directory Users and Computers (ADUC), create an Organizational Unit (OU) called “_EMPLOYEES” and another one called "_ADMINS":
</p>
<p>
  
  ![organization unit](https://github.com/user-attachments/assets/7733aec0-ff69-4932-b857-56d080b7800c)

</p>
<p>
  Create a new employee named “Jane Doe” with the username of “jane_admin”:
</p>
<p>
  
![create jane admin](https://github.com/user-attachments/assets/67adfdac-e810-4e83-9521-5f9a7409c18b)

</p>
<p>
  Add jane_admin to the “Domain Admins” Security Group:
</p>
<p>
  
 ![make jane an admin](https://github.com/user-attachments/assets/6a76e29e-d117-4c7c-bceb-8e26f6835962)

</p>
<p>  
  Log out/close the Remote Desktop connection to DC-1 and log back in as “mydomain.com\jane_admin”. Use jane_admin as your admin account from now on:
</p>
<p>
  
![log in as jane admin](https://github.com/user-attachments/assets/0ffaca3d-2f23-4ab0-8b24-69d32be79e49)

</p>
<br />
<br />
<h3 align="center">Join Client-1 to your domain (myadproject.com)</h3>
<br />
<p>
  From the Azure Portal, set Client-1’s DNS settings to the DC’s Private IP address:
</p>
<p>
  
![put dns to dc1 private ip](https://github.com/user-attachments/assets/cb309dcd-332e-49b8-b524-40039984c375)

</p>
<p>
  From the Azure Portal, restart Client-1.
</p>
<p>
  Login to Client-1 (Remote Desktop) as the original local admin (labuser) and join it to the domain (computer will restart):
</p>
<p>
  
 ![remote desktop add domain users](https://github.com/user-attachments/assets/d3d9910b-6375-4728-9e53-a744bf690742)

</p>
<p>
  Login to the Domain Controller (Remote Desktop) and verify Client-1 shows up in Active Directory Users and Computers (ADUC) inside the “Computers” container on the root of the domain.
</p>
<p>
  Create a new OU named “_CLIENTS” and drag Client-1 into there:
</p>
<p>
  
![move client 1 into client OU](https://github.com/user-attachments/assets/86fd2fae-10db-42f3-b604-42894104f162)

</p>
<br />
<br />
<h3 align="center">Setup Remote Desktop for non-administrative users on Client-1</h3>
<br />
<p>
  Log into Client-1 as mydomain.com\jane_admin and open system properties.
</p>
<p>
  Click “Remote Desktop”.
</p>
<p>
  Allow “domain users” access to remote desktop.
</p>
<p>
  You can now log into Client-1 as a normal, non-administrative user now.
</p>
<p>
  Normally you’d want to do this with Group Policy that allows you to change MANY systems at once (maybe a future lab):
</p>
<p>
  
  ![remote settings](https://github.com/user-attachments/assets/faee6eb7-b158-452a-88aa-b34e032e4710)

</p>
<br />
<br />
<h3 align="center">Create a bunch of additional users and attempt to log into client-1 with one of the users</h3>
<br />
<p>
  Login to DC-1 as jane_admin
</p>
<p>
  Open PowerShell_ise as an administrator.
</p> 
<p>  
  Create a new File and paste the contents of this script (https://github.com/Xinloiazn/configure-ad/blob/main/adscript.ps1) into it and Run the script and observe the accounts being created:
</p>
<p>
  
![generating names](https://github.com/user-attachments/assets/45d413c6-845c-455e-8a19-52d30b8fa736)

</p>
<p>

</p>
<p>
  
![showing the generated users](https://github.com/user-attachments/assets/c15cf840-5607-442a-95a4-300714494878)

</p>
<p>
  When finished, open ADUC and observe the accounts in the appropriate OU and attempt to log into Client-1 with one of the accounts (take note of the password in the script):
</p>
<p>
  <img src="https://i.imgur.com/ZZCfiCp.png" height="75%" width="100%" alt="employee user accounts"/>
  <img src="https://i.imgur.com/7gBpNzN.png" height="75%" width="100%" alt="employee user selection"/>
  <img src="https://i.imgur.com/cqsddjn.png" height="75%" width="100%" alt="employee user login"/>
</p>
<br />
<br />
<p>
  I hope this tutorial helped you learn a little bit about network security protocols and observe traffic between virtual machines. This can be easily done on a PC or a Mac. Mac would just have an extra step to download the Remote Desktop App.
</p>
<p>
  Now that we're done, DON'T FORGET TO CLEAN UP YOUR AZURE ENVIRONMENT so that you don't incur unnecessary charges.
</p>
<p>
  Close your Remote Desktop connection, delete the Resource Group(s) created at the beginning of this tutorial, and verify Resource Group deletion.
</p>

