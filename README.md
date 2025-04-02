<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

# Setting Up On-Premises Active Directory in Azure VMs

This guide walks you through setting up an on-premises Active Directory environment using virtual machines in Microsoft Azure.

## Tools and Environments

- Microsoft Azure (Virtual Machines/Compute)  
- Remote Desktop  
- Active Directory Domain Services  
- PowerShell  

## Operating Systems

- Windows Server 2022  
- Windows 10 (21H2)  

## High-Level Steps

- Step 1: Set up two virtual machines - one for the domain controller (DC-1) and one for the client (Client-1)  
- Step 2: Configure the DC-1 IP as static and point Client-1's DNS to it  
- Step 3: Install Active Directory on DC-1, create a domain, and set up admin and user accounts  
- Step 4: Enable remote desktop access for regular users on Client-1 and test it out  

## Lab Diagram
<p>
  <img width="548" alt="image" src="https://github.com/user-attachments/assets/9653c620-73bd-4805-be60-734e519b9a65" />
</p>

## VM Prerequisites

- Both VMs must be on the same virtual network (not the default one)  
- Both in the same Azure region for efficiency  
- DC-1 needs a Windows Server OS image  

## Detailed Deployment Steps

<p>
<img width="929" alt="image" src="https://github.com/user-attachments/assets/839c63d4-f634-4e3d-af9b-8e3266e5b035" />
</p>
After creating the VMs in Azure, first make DC-1's private IP static by going to its network settings, IP configurations, and setting ipconfig1 to static. I also disable the Windows firewall on DC-1 for simplicity.

<p>
<img width="675" alt="image" src="https://github.com/user-attachments/assets/075a7dc9-87d4-4410-8865-45a4d4785ccf" />
<img width="537" alt="image" src="https://github.com/user-attachments/assets/cf28d994-ae2f-46b4-b01c-29d410864785" />
</p>
On Client-1, update its DNS server settings to use DC-1's private IP instead of Azure's default. Verify this works by logging into Client-1, opening PowerShell, and running "ipconfig /all" to check the DNS and "ping 10.0.0.4" to test connectivity to DC-1.

<p>
<img width="728" alt="image" src="https://github.com/user-attachments/assets/51957803-4e27-48a2-9545-db88f5379e0a" />
<img width="926" alt="image" src="https://github.com/user-attachments/assets/19c8d62b-5562-478f-b790-a986699caf63" />
<img width="299" alt="image" src="https://github.com/user-attachments/assets/9b968918-69ed-45fa-97a3-02e8927a65d2" />
</p>
To make DC-1 a domain controller, log in, open Server Manager, and add the "Active Directory Domain Services" role. After installing, click the flag icon, choose to create a new forest, and enter a root domain name like "mydomain.com". Finish the wizard and DC-1 will restart. Log back in using the domain, e.g. "mydomain\Kev".

<p>
  <img width="800" alt="image" src="https://github.com/user-attachments/assets/e9d2f78f-e97e-48b2-8a8c-51adcab07966" />
<img width="698" alt="image" src="https://github.com/user-attachments/assets/b4df4a58-5ef7-4217-b0d1-aa6703b045a2" />
<img width="820" alt="image" src="https://github.com/user-attachments/assets/5609d043-e2a3-4b56-8b57-199483032a11" />
</p>
In Active Directory Users and Computers on DC-1, create two organizational units under mydomain.com - "_EMPLOYEES" and "_ADMINS". In _ADMINS, add a new user like "Jane_admin", set a password, and make her a domain admin by adding her to the "Domain Admins" group in her properties.

<p>
  <img width="937" alt="image" src="https://github.com/user-attachments/assets/05ca39e0-ec39-4a39-b8ee-85e907cc4ff1" />
<img width="595" alt="image" src="https://github.com/user-attachments/assets/01447dc2-4bec-4521-9c66-b291ecb1b7f0" />
</p>
To join Client-1 to the domain, log in as a local user, go to system settings, and under "Rename this PC" choose to join the "mydomain.com" domain. Verify it worked by checking the Computers folder in Active Directory on DC-1.

<p>
  <img width="907" alt="image" src="https://github.com/user-attachments/assets/55578047-c3c0-424d-b3e1-8d58da78d882" />
</p>
For remote access, log into Client-1 as Jane_admin, go to remote desktop settings, and add "Domain Users" to the allowed list.

<p>
  <img width="829" alt="image" src="https://github.com/user-attachments/assets/35d5e7c0-ed11-4d8b-a89a-40019cf1f756" />
<img width="932" alt="image" src="https://github.com/user-attachments/assets/c81d8939-1b2c-470d-a853-26a708bc4135" />
</p>
Finally, on DC-1 as an admin, use a PowerShell ISE script to generate user accounts in the _EMPLOYEES OU, like 100 users with the password "Password1". Check _EMPLOYEES to confirm they're there. Test remote desktop to Client-1 with a user like "mydomain.com\big.sapir" and "Password1".
