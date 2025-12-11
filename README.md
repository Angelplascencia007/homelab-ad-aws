🏢 AWS Active Directory Homelab – helpdesk.local

A fully documented Windows Server / Active Directory environment built in AWS

This project demonstrates my ability to design, deploy, secure, document, and troubleshoot a real-world Active Directory environment in AWS.
It includes AWS networking, Windows Server administration, Group Policy design, file server configuration, and drive-mapping automation used in enterprise environments.

📌 Table of Contents

Project Overview

Architecture Diagram

AWS Network Build (VPC, Subnet, Routing)

Domain Controller Deployment

Active Directory Design (OUs, Users, Groups)

Security Baseline GPO

File Server & NTFS Permissions

Drive Mapping GPO (Automation)

Workstation Domain Join & Testing

Real Troubleshooting Example (DNS Failure)

Skills Demonstrated

Future Enhancements

📘 Project Overview

This homelab simulates a realistic enterprise Windows environment, including:

A custom VPC with subnet, route table, and Internet Gateway

A Windows Server 2019 Domain Controller (AD DS + DNS)

A Windows Server 2019 Workstation joined to the domain

Department-based OUs, users, and groups

Security baseline Group Policies

NTFS + share-level file server permissions

Automated drive mapping using Group Policy Preferences

Real-world troubleshooting steps (DNS, domain join, GP issues)

This project mirrors what you’d build in a real helpdesk or system administrator role and is excellent for both practice and portfolio demonstration.

🖥️ Architecture Diagram
                   AWS VPC – 10.0.0.0/16
               ┌────────────────────────────────┐
               │        Subnet 10.0.1.0/24       │
               │                                 │
     ┌──────────────────────┐       ┌────────────────────────┐
     │  DC01 (Domain Ctrl)  │       │ Workstation01 (Client) │
     │  10.0.1.129          │◄────►│ 10.0.1.223              │
     │ AD DS + DNS + File   │       │ Domain-Joined Test PC   │
     └──────────────────────┘       └────────────────────────┘
               │                                 │
               └────────────────────────────────┘
                  Internet Gateway for RDP

⭐ 1. AWS Network Build

A reliable Active Directory environment requires a stable and correctly configured network.

1.1 VPC Creation
<img src="https://github.com/user-attachments/assets/f74dc333-0645-43d7-b231-5fc3724490ce" width="100%" />

Why this matters:
A VPC gives you a private network where you control IP ranges, routing, DNS, and security — exactly how enterprise networks operate.

1.2 Subnet Configuration
<img src="https://github.com/user-attachments/assets/b21c7eaf-f5fd-44e0-b6ea-8aabf3c026fb" width="100%" /> <img src="https://github.com/user-attachments/assets/43517948-ef7c-4001-b0ee-dab246166276" width="100%" />

Why this matters:
Domain Controllers and domain-joined computers must live on the same subnet for easy communication.

1.3 Route Table & Internet Gateway
<img src="https://github.com/user-attachments/assets/992a626d-04a3-43fe-85a6-984bd45214b0" width="100%" /> <img src="https://github.com/user-attachments/assets/dcc19c52-f43d-431b-b45d-565a3796ffc8" width="100%" />

The route table provides outbound access for:

Windows updates

Feature installation

RDP administration

<img src="https://github.com/user-attachments/assets/d33bc6a1-0e10-47d8-81c5-1c53358ca407" width="100%" />
⭐ 2. Domain Controller Deployment
<img src="https://github.com/user-attachments/assets/92625f53-4833-492b-99d3-00618b340a7b" width="100%" />
2.1 Instance Setup

Windows Server 2019

Size: t3.small (AD DS needs more than 1GB RAM)

Private IP: 10.0.1.129

2.2 Security Groups

Ports allowed (least privilege):

Service	Port	Why Needed
RDP	3389	Admin access
DNS	53	AD name resolution
LDAP	389	Directory queries
Kerberos	88	Authentication
SMB	445	SYSVOL, file shares
RPC	135	AD tools & GP
2.3 Promote to Domain Controller

Steps inside Server Manager:

Add roles → AD DS

Promote → New Forest

Domain: helpdesk.local

Reboot and verify domain services

<img src="https://github.com/user-attachments/assets/fd0cbe5b-595e-4e56-a4f4-0b59c34621d2" width="100%" />
⭐ 3. Active Directory Design

A well-structured AD is critical for applying the correct policies and permissions.

<img src="https://github.com/user-attachments/assets/05c1666e-2f8b-4bd1-94e7-ba17893e088c" width="100%" />
3.1 Organizational Units
<img src="https://github.com/user-attachments/assets/c66aeff5-cd80-4883-8351-f79862192bdc" width="100%" />

OUs created:

Company Departments

HR

IT

Sales

Workstations

Helpdesk Users

Helpdesk Computers

Why this matters:
This mirrors real companies and ensures Group Policies apply to the correct scope.

3.2 User Creation

Users created to test permissions & GPOs:

John Parker – Sales

Maria Lopez – HR

James Cruz – IT

Examples:

<img src="https://github.com/user-attachments/assets/fff48354-e08b-49e2-a4e9-bc5e2f3559c2" width="100%" /> <img src="https://github.com/user-attachments/assets/ba7abba4-8dab-4b13-b2c1-7720a634294d" width="100%" />
⭐ 4. Security Baseline GPO

This domain-linked GPO enforces secure authentication practices.

<img src="https://github.com/user-attachments/assets/d03fe194-885e-4447-8e27-bdf263c8d4cf" width="100%" />
Password Policy
<img src="https://github.com/user-attachments/assets/c33f7b01-eca2-4e15-a898-f8afc20b3aa6" width="100%" />
Account Lockout
<img src="https://github.com/user-attachments/assets/e8b8655b-c916-4b12-9dc7-113cff605c75" width="100%" />

These are standard security requirements in most companies.

⭐ 5. File Server & NTFS Permissions

Folder structure:

C:\CompanyShares\
   ├── HR
   ├── IT
   ├── Sales
   └── Public



Why NTFS matters:
NTFS defines actual access.
Share permissions are set to read — NTFS determines departmental permissions.

⭐ 6. Drive Mapping Automation

Each department receives an automatic mapped network drive using GPO Preferences.

<img src="https://github.com/user-attachments/assets/c4e1356f-fb2f-4919-b3ba-0d7bd95ec6e9" width="100%" />

This simulates real-world corporate user experience.

⭐ 7. Workstation Join & Testing
<img src="https://github.com/user-attachments/assets/4bbbc3d4-df07-4780-8614-8b865fbdedc1" width="100%" />

Steps:

Set DNS to the DC: 10.0.1.129

Join domain

Log in as HR/Sales/IT users

Verify:

Correct file share permissions

Correct mapped drives

GPO enforcement

⭐ 8. Troubleshooting DNS Example

A realistic issue occurred where the workstation couldn't resolve the domain.

<img src="https://github.com/user-attachments/assets/cb990442-1692-420c-904e-91c0cbc79d94" width="100%" />
Fix Required:

Ensuring workstation points only to domain controller for DNS

Verifying forward lookup zone

Allowing DNS traffic in Security Groups

Restarting DNS service

Running nslookup tests

<img src="https://github.com/user-attachments/assets/cbcae326-4e51-45c7-9e10-8d7e7fb482e4" width="100%" />

Troubleshooting examples like this show hands-on understanding — a major plus for recruiters.

🧠 Skills Demonstrated
AWS Cloud Skills

VPC & subnet design

Route tables & Internet Gateway configuration

EC2 deployment & IAM integration

Security Group firewalling (least privilege model)

Windows Server / SysAdmin Skills

Active Directory Domain Services

DNS configuration & troubleshooting

Organizational Unit design

User and group provisioning

NTFS permissioning

Group Policy creation and targeting

Automation / Policy Management

Drive mapping via Group Policy Preferences

Domain-wide security baseline policies

Troubleshooting & Tools

DNS failures

nslookup, Event Viewer

Group Policy Result / Force Update

Authentication & logon issues

🚀 Future Enhancements

To expand the lab even further:

Add a second Domain Controller for redundancy

Configure DHCP on the domain

Deploy WSUS update server

Add Azure AD Connect & test hybrid identity

Automate user onboarding with PowerShell

Add a SIEM solution (Splunk / Wazuh)

Implement GPO hardening (CIS benchmarks)
