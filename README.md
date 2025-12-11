🧪 Active Directory Help Desk Homelab (AWS Cloud)

This project is a complete enterprise-grade Active Directory lab built in AWS to simulate a real corporate environment used by Help Desk, Desktop Support, and System Administrators.

It includes:

✔ AWS VPC & Cloud Network
✔ Windows Server Domain Controller (DC01)
✔ Windows 10 Workstation (WORKSTATION01)
✔ Active Directory Structure
✔ OU Design & User Provisioning
✔ Group Policies (GPOs)
✔ File Shares
✔ Drive Mapping via GPO
✔ Security Groups
✔ Troubleshooting workflow
✔ Real-world enterprise tasks

📌 Table of Contents

Project Overview

AWS Infrastructure

Deploying the Domain Controller

Deploying the Workstation

Active Directory OU Structure

User & Group Management

Group Policy Management

File Shares & NTFS Permissions

Drive Mapping via GPO

Testing & Troubleshooting

Key Skills Learned

📝 Project Overview

This homelab replicates a real company environment where I practiced:

User onboarding

Password resets

Account lockouts

AD DS administration

DNS troubleshooting

Group Policy creation

Mapping network drives

Managing security groups

File share permissions

Workstation domain joins

Pictures are placed throughout — I will insert them after your upload.

☁️ AWS Infrastructure
VPC Setup

Created custom VPC: 10.0.0.0/16

Created Public Subnet: 10.0.1.0/24

Attached Internet Gateway

Updated Route Tables to allow outbound internet

Enabled DNS hostnames + DNS resolution

(Insert AWS screenshots here)

🖥 Deploying the Domain Controller
Instance Details

Windows Server 2019 / 2022

Static Private IP: 10.0.1.129

Promoted to Domain Controller

Domain created: helpdesk.local

Roles Installed

Active Directory Domain Services

DNS Server

Tasks Performed

Configured forward lookup zones

Verified DNS health

Ensured DC01 can resolve internal hostnames

(Insert DC01 setup screenshots here)

💻 Deploying the Workstation (WORKSTATION01)
Instance Details

Windows 10 / Server 2019 Desktop Experience

Joined domain: helpdesk.local

Validation

Ping DC01 by hostname

Tested DNS resolution

Logged in using AD credentials

(Insert workstation screenshots here)

🗂 Active Directory OU Structure

Created a full organizational layout:

helpdesk.local
│
├── Company Departments
│     ├── HR
│     ├── IT
│     ├── Sales
│     └── Service OUs
│
├── Workstations
│     └── Computer Objects
│
├── Groups
│     ├── HR-Group
│     ├── Sales-Group
│     ├── IT-Admin
│     └── RDP-Access
│
└── Helpdesk Users


(Insert OU screenshots here)

👥 User & Group Management

Created users inside each department:

HR employees

Sales employees

Helpdesk users

IT admin account

Configured:

Group membership

Password resets

Unlock accounts

Department-based access

(Insert ADUC user management screenshots here)

🔐 Group Policy Management
Created GPOs:

Security Baseline GPO

Password policy (8+ characters, 90-day expiration)

Account lockout after 5 failed attempts

Kerberos policy settings

RDP Access GPO

Remote Desktop Users Group → includes RDP-Access

Drive Mapping GPOs

HR → H: drive

Sales → S: drive

IT → I: drive

Public share → P: drive

(Insert GPO editor screenshots here)

📁 File Shares & NTFS Permissions

Created CompanyShares on DC01:

Subfolders

HR → HR-Group = Modify

IT → IT-Admin = Full

Sales → Sales-Group = Modify

Public → Everyone = Read/Write

Configured:

NTFS permissions

Share permissions

Security inheritance

Department-level access

(Insert file share screenshots here)

🔗 Drive Mapping via GPO

Mapped drives based on security group membership:

Department	Share Path	Drive Letter
HR	\DC01\CompanyShares\HR	H:
Sales	\DC01\CompanyShares\Sales	S:
IT	\DC01\CompanyShares\IT	I:
Public	\DC01\CompanyShares\Public	P:

Applied via:

Computer Configuration
 → Preferences
   → Windows Settings
     → Drive Maps


(Insert drive mapping screenshots here)

🧪 Testing & Troubleshooting

Validation steps performed:

nslookup DC01

Verified A and PTR DNS records

Ran gpupdate /force on workstation

Tested GPO linking

Verified NTFS access for each department

Confirmed drive mappings

Tested RDP access control

(Insert troubleshooting screenshots here)

🎯 Key Skills Learned

This lab improved my abilities in:

Active Directory Domain Services

AWS VPC configuration

Windows Server administration

DNS management

User + group provisioning

Least privilege access

Group Policy creation

File sharing & NTFS permissions

Troubleshooting authentication issues

Real-world help desk workflows

📎 Final Notes

This project simulates a real corporate IT environment designed to:

✔ Build hands-on experience
✔ Strengthen troubleshooting skills
✔ Prepare for Help Desk / IT Support roles
✔ Demonstrate system administration knowledge
