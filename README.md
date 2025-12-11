# AWS Active Directory Homelab (helpdesk.local)

This repository documents my full **Active Directory homelab** built entirely in **AWS**, including:

- Custom VPC + subnet + routing + security groups  
- A Windows Server 2019 **Domain Controller** (`DC01`)  
- A Windows Server 2019 **workstation** (`Workstation01`)  
- A fully configured **Active Directory domain**: `helpdesk.local`  
- OUs, users, groups, security groups  
- **Security baseline GPO** (passwords, lockout policies, auditing)  
- **File shares** (HR, Sales, IT, Public) with NTFS + share permissions  
- **GPO-based drive mappings** for departments  
- RDP access controlled via AD security groups  

---

## 1. Architecture Overview

- **Cloud:** AWS  
- **Region:** us-east-2 (Ohio)  
- **VPC:** `Home lab Cloud` – `10.0.0.0/16`  
- **Subnet:** `home lab cloud subnet` – `10.0.1.0/24`  
- **Instances:**
  - `DC01` – Windows Server 2019 Datacenter, `t3.small`, private IP `10.0.1.129`
  - `Workstation01` – Windows Server 2019 Datacenter, `t3.micro`, private IP `10.0.1.223`

### Screenshots

AWS console – EC2 instances:

<img width="1920" height="1080" alt="1" src="https://github.com/user-attachments/assets/4d58b8ae-d43c-461c-be8f-40a6c7dc1cb3" />

VPC, subnet, route table, and Internet gateway configuration:
VPC 

<img width="1920" height="1080" alt="2" src="https://github.com/user-attachments/assets/f74dc333-0645-43d7-b231-5fc3724490ce" />

<img width="1920" height="1080" alt="3" src="https://github.com/user-attachments/assets/b21c7eaf-f5fd-44e0-b6ea-8aabf3c026fb" />

Subnet settings

<img width="1920" height="1080" alt="4" src="https://github.com/user-attachments/assets/43517948-ef7c-4001-b0ee-dab246166276" />

<img width="1920" height="1080" alt="5" src="https://github.com/user-attachments/assets/8313ec6e-723c-462b-b9ec-b7cb8fa12b44" />

Route table

<img width="1920" height="1080" alt="8" src="https://github.com/user-attachments/assets/992a626d-04a3-43fe-85a6-984bd45214b0" />

<img width="1920" height="1080" alt="9" src="https://github.com/user-attachments/assets/dcc19c52-f43d-431b-b45d-565a3796ffc8" />

Internet gateway

<img width="1920" height="1080" alt="6" src="https://github.com/user-attachments/assets/d33bc6a1-0e10-47d8-81c5-1c53358ca407" />

<img width="1920" height="1079" alt="7" src="https://github.com/user-attachments/assets/5a73cffa-c387-431f-bad6-b735c8a5449d" />

---

## 2. EC2 Instances

### Domain Controller – DC01

<img width="1920" height="1080" alt="10" src="https://github.com/user-attachments/assets/92625f53-4833-492b-99d3-00618b340a7b" />

- AMI: **Microsoft Windows Server 2019 Base**
- Instance type: `t3.small`
- Launched in `Home lab Cloud` VPC and subnet
- Security group allows:
  - RDP from my public IP
  - Domain services within the VPC (TCP/UDP 53, 88, 389, 445, etc. – locked down to the subnet)

DC01 instance details

<img width="1920" height="1080" alt="11" src="https://github.com/user-attachments/assets/bf29fe96-bcb1-4c8c-9dc2-332f1aef60c0" />

<img width="1920" height="1080" alt="15" src="https://github.com/user-attachments/assets/b8282a69-fb50-4090-9d3d-d65f9c92030c" />

<img width="1920" height="1080" alt="16" src="https://github.com/user-attachments/assets/14b6a4da-1dd0-4a49-acb1-3ad7670efccf" />

DC01 RDP connect

<img width="1920" height="1080" alt="12" src="https://github.com/user-attachments/assets/0af46646-bf69-4cf7-8c96-a3e4894f2035" />

<img width="1920" height="1079" alt="13" src="https://github.com/user-attachments/assets/04e612c5-927d-4c30-966a-b8bb84df32cc" />

<img width="1910" height="725" alt="14" src="https://github.com/user-attachments/assets/fd0cbe5b-595e-4e56-a4f4-0b59c34621d2" />

<img width="1920" height="1080" alt="17" src="https://github.com/user-attachments/assets/5b3b8d3e-1106-41aa-a19e-15f01e3e5d9d" />

![18](https://github.com/user-attachments/assets/7c82b404-8fa6-4a99-8dff-26dac9848125)

### Workstation – Workstation01

- AMI: **Microsoft Windows Server 2019 Base**
- Instance type: `t3.micro`
- Joined to `helpdesk.local` and used to test GPOs, file shares, and drive mappings.

## 3. Active Directory Design

**Domain:** `helpdesk.local`

![IMG_5529](https://github.com/user-attachments/assets/05c1666e-2f8b-4bd1-94e7-ba17893e088c)

### Organizational Units

I created a simple but realistic OU structure:

- `HR`
- `IT`
- `Sales`
- `Workstations`
- `Helpdesk users`
- `Helpdesk Computers`
- `Company Departments`
- `Service OUs`

![IMG_F3944D0A-D20C-495E-902D-AE610133C64F](https://github.com/user-attachments/assets/c66aeff5-cd80-4883-8351-f79862192bdc)


Workstations have their own OU so I can target GPOs just at computers:

![Workstations OU](screenshots/active-directory/05-aduc-workstations-ou-empty.png) 

![IMG_5564](https://github.com/user-attachments/assets/35fce3e0-b2a5-4c2e-be14-20ca520e8624)

### Users

Example users:

- **John Parker** – Sales (`jparker`)
- **Maria Lopez** – HR (`mlopez`)
- **James Cruz** – IT (`jcruz`)
- **Test User1** – generic test account

Create John Parker
![IMG_5533](https://github.com/user-attachments/assets/fff48354-e08b-49e2-a4e9-bc5e2f3559c2)

Create Maria Lopez
![IMG_5534](https://github.com/user-attachments/assets/e38e2512-2f32-45a7-b57c-a4cfb35f8ead)

Create James Cruz
![IMG_5535](https://github.com/user-attachments/assets/ba7abba4-8dab-4b13-b2c1-7720a634294d)

## 4. Security Baseline GPO

I created a **domain-linked GPO** called **“Security Baseline GPO”** that configures:

- Password policy (length, age, complexity)
- Account lockout policy
- Kerberos policy
- Local security options & auditing (extendable)

Create Security Baseline GPO
![IMG_5568](https://github.com/user-attachments/assets/d03fe194-885e-4447-8e27-bdf263c8d4cf)


### Password Policy

- Maximum password age: **90 days**
- Minimum password age: **30 days**
- Minimum length: **8 characters**
- Complexity: **Enabled**

Password policy settings

![IMG_5570](https://github.com/user-attachments/assets/c33f7b01-eca2-4e15-a898-f8afc20b3aa6)

### Account Lockout Policy

- Lockout threshold: **5** invalid attempts  
- Lockout duration: **15 minutes**  
- Reset counter after: **10 minutes**

Account lockout settings

After linking the GPO at the domain level, I forced an update:
![IMG_5571](https://github.com/user-attachments/assets/e8b8655b-c916-4b12-9dc7-113cff605c75)

5. File Shares & NTFS Permissions

On DC01 I created a root folder:

C:\CompanyShares\
   ├─ HR
   ├─ IT
   ├─ Sales
   └─ Public


NTFS Permissions

HR-Group – Modify on HR

Sales-Group – Modify on Sales

IT-Group – Modify on IT

Everyone / Domain Users – Read/Write on Public (lab choice)

Helpdesk\Administrators – Full control on all

![IMG_5588](https://github.com/user-attachments/assets/f90a96ef-91cf-4187-84e9-33e5d91d14f8)



Share Permissions

Shared C:\CompanyShares as \\DC01\CompanyShares:

Administrators – Full Control

Domain Users – Read (I enforce access via NTFS)

![IMG_5592](https://github.com/user-attachments/assets/153e3d3e-2945-4815-8cb7-365e7bd5cbf8)


6. GPO – Drive Mapping

To automatically map drives based on department, I used Group Policy Preferences → Drive Maps.

Example: Map HR drive for HR users

GPO: Map HR Drive GPO

Location: \\DC01\CompanyShares\HR

Drive letter: H:

Label: HR Share

Security filter / WMI / Item-level targeting: HR group only (lab logic)

![IMG_5596](https://github.com/user-attachments/assets/c4e1356f-fb2f-4919-b3ba-0d7bd95ec6e9)

After applying the GPO, I ran:

gpupdate /force

![IMG_5586](https://github.com/user-attachments/assets/4e22c1e2-4358-421b-996d-0e4510bce558)

from the workstation and confirmed the mapped drive appears in File Explorer.

7. Workstation Join & Testing

On Workstation01:

Pointed DNS to DC01 (10.0.1.129).

Joined the helpdesk.local domain.

Logged in as different users to verify:

HR users can access HR and Public.

Sales users can access Sales and Public.

IT users have wider access (lab-style admin).

Mapped drives appear automatically.


![IMG_5553](https://github.com/user-attachments/assets/4bbbc3d4-df07-4780-8614-8b865fbdedc1)




8. Troubleshooting Example – DNS / nslookup

At one point I saw DNS timeouts when trying to resolve the domain from the workstation:

This was fixed by:

Ensuring the workstation’s only DNS server is 10.0.1.129 (DC01).

Verifying the helpdesk.local forward lookup zone exists on DC01.

Checking security groups to allow DNS (UDP/TCP 53) within the subnet.

![IMG_6B052B13-0EFE-40C9-8218-CCF18FA8848D](https://github.com/user-attachments/assets/cb990442-1692-420c-904e-91c0cbc79d94)

![IMG_F140E4E6-DA26-480C-8FD4-D0A3CEBF992C](https://github.com/user-attachments/assets/7f25e764-6dea-46d8-81b9-9b6af8358b04)

![IMG_A46DD81F-0456-4716-9EE0-9964DF7F9F27](https://github.com/user-attachments/assets/cbcae326-4e51-45c7-9e10-8d7e7fb482e4)



10. How to Use This Repo

This repo is meant as both:

A learning resource for people wanting to build a similar homelab, and

A portfolio project demonstrating my experience with:

AWS networking & security groups

Windows Server

Active Directory & Group Policy

File server design and permissions

Troubleshooting & documentation

Feel free to clone, adapt, and build your own version.


