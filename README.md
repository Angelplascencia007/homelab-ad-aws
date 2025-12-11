Create a file called README.md in the root with this content:

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

![EC2 instances]()

VPC, subnet, route table, and Internet gateway configuration:

![VPC settings](screenshots/aws/02-vpc-settings.png)  
![Subnet settings](screenshots/aws/03-subnet-settings.png)  
![Route table](screenshots/aws/04-route-table-settings.png)  
![Internet gateway](screenshots/aws/05-internet-gateway.png)

---

## 2. EC2 Instances

### Domain Controller – DC01

- AMI: **Microsoft Windows Server 2019 Base**
- Instance type: `t3.small`
- Launched in `Home lab Cloud` VPC and subnet
- Security group allows:
  - RDP from my public IP
  - Domain services within the VPC (TCP/UDP 53, 88, 389, 445, etc. – locked down to the subnet)

![DC01 instance details](screenshots/aws/06-dc01-instance-details.png)  
![DC01 RDP connect](screenshots/aws/07-dc01-rdp-connect.png)

### Workstation – Workstation01

- AMI: **Microsoft Windows Server 2019 Base**
- Instance type: `t3.micro`
- Joined to `helpdesk.local` and used to test GPOs, file shares, and drive mappings.

![Workstation01 instance](screenshots/aws/08-workstation01-instance-details.png)  
![Workstation RDP connect](screenshots/aws/09-workstation01-rdp-connect.png)

More details about the instance configuration and domain promotion are in  
📄 [`documentation/ad-setup.md`](documentation/ad-setup.md)

---

## 3. Active Directory Design

**Domain:** `helpdesk.local`

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

![ADUC OU structure](screenshots/active-directory/01-aduc-root-ous.png)

Workstations have their own OU so I can target GPOs just at computers:

![Workstations OU](screenshots/active-directory/05-aduc-workstations-ou-empty.png)  
![Workstation computer object](screenshots/active-directory/06-aduc-workstation-object-ec2amaz.png)

### Users

Example users:

- **John Parker** – Sales (`jparker`)
- **Maria Lopez** – HR (`mlopez`)
- **James Cruz** – IT (`jcruz`)
- **Test User1** – generic test account

![Create John Parker](screenshots/users-and-groups/01-create-user-john-parker-sales.png)  
![Create Maria Lopez](screenshots/users-and-groups/02-create-user-maria-lopez-hr.png)  
![Create James Cruz](screenshots/users-and-groups/03-create-user-james-cruz-it.png)

All AD creation steps are covered in  
📄 [`documentation/ad-setup.md`](documentation/ad-setup.md)

---

## 4. Security Baseline GPO

I created a **domain-linked GPO** called **“Security Baseline GPO”** that configures:

- Password policy (length, age, complexity)
- Account lockout policy
- Kerberos policy
- Local security options & auditing (extendable)

![Create Security Baseline GPO](screenshots/gpo-security/01-gpmc-create-security-baseline-gpo.png)

### Password Policy

- Maximum password age: **90 days**
- Minimum password age: **30 days**
- Minimum length: **8 characters**
- Complexity: **Enabled**

![Password policy settings](screenshots/gpo-security/03-gpo-password-policy-settings.png)

### Account Lockout Policy

- Lockout threshold: **5** invalid attempts  
- Lockout duration: **15 minutes**  
- Reset counter after: **10 minutes**

![Account lockout settings](screenshots/gpo-security/05-gpo-account-lockout-settings.png)

After linking the GPO at the domain level, I forced an update:

```powershell
gpupdate /force


More details in
📄 documentation/gpo-hardening.md

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




Share Permissions

Shared C:\CompanyShares as \\DC01\CompanyShares:

Administrators – Full Control

Domain Users – Read (I enforce access via NTFS)




Full breakdown in
📄 documentation/file-shares.md

6. GPO – Drive Mapping

To automatically map drives based on department, I used Group Policy Preferences → Drive Maps.

Example: Map HR drive for HR users

GPO: Map HR Drive GPO

Location: \\DC01\CompanyShares\HR

Drive letter: H:

Label: HR Share

Security filter / WMI / Item-level targeting: HR group only (lab logic)








After applying the GPO, I ran:

gpupdate /force


from the workstation and confirmed the mapped drive appears in File Explorer.

More details in
📄 documentation/workstation-setup.md

7. Workstation Join & Testing

On Workstation01:

Pointed DNS to DC01 (10.0.1.129).

Joined the helpdesk.local domain.

Logged in as different users to verify:

HR users can access HR and Public.

Sales users can access Sales and Public.

IT users have wider access (lab-style admin).

Mapped drives appear automatically.






8. Troubleshooting Example – DNS / nslookup

At one point I saw DNS timeouts when trying to resolve the domain from the workstation:

This was fixed by:

Ensuring the workstation’s only DNS server is 10.0.1.129 (DC01).

Verifying the helpdesk.local forward lookup zone exists on DC01.

Checking security groups to allow DNS (UDP/TCP 53) within the subnet.

9. Next Steps / Ideas

Some future improvements I may add to this lab:

WSUS or SCCM / MECM for patching

Additional GPOs (hardening, software deployment, browser settings)

VPN into the VPC instead of public RDP

Integration with Azure AD / Entra ID

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


---

## 3. `documentation/ad-setup.md`

```markdown
# Active Directory Setup (helpdesk.local)

## 1. Promote DC01 to Domain Controller

1. Install the **Active Directory Domain Services** role from **Server Manager**.
2. Promote the server to a new forest:
   - Root domain name: `helpdesk.local`
   - NetBIOS name: `HELPDESK`
3. Restart after promotion.

![Server Manager dashboard](../screenshots/domain-controller/01-server-manager-dashboard.png)

## 2. Create OU Structure

In **Active Directory Users and Computers (ADUC)**:

- Right-click the domain → **New → Organizational Unit**.
- Created OUs:
  - `HR`
  - `IT`
  - `Sales`
  - `Workstations`
  - `Helpdesk users`
  - `Helpdesk Computers`
  - `Company Departments`
  - `Service OUs`

![OU structure](../screenshots/active-directory/01-aduc-root-ous.png)

## 3. Create Users

Examples:

- John Parker – Sales → `jparker`
- Maria Lopez – HR → `mlopez`
- James Cruz – IT → `jcruz`

![Create user](../screenshots/users-and-groups/01-create-user-john-parker-sales.png)

I placed users into department OUs and added them to groups (covered more in `gpo-hardening.md` and `file-shares.md`).

## 4. Join Workstation to Domain

On **Workstation01**:

1. Set the DNS server to point to `DC01` (`10.0.1.129`).
2. Rename the computer if desired.
3. Join the domain from **System Properties → Computer Name → Change**.
4. Reboot and log in with a domain user.

![Workstation joined](../screenshots/workstation/01-workstation-joined-to-domain.png)

4. documentation/gpo-hardening.md
# Security Baseline GPO

GPO name: **Security Baseline GPO**  
Linked to: **helpdesk.local** domain

## 1. Password Policy

Path:  
`Computer Configuration → Policies → Windows Settings → Security Settings → Account Policies → Password Policy`

Settings:

- **Maximum password age:** 90 days
- **Minimum password age:** 30 days
- **Minimum password length:** 8 characters
- **Password must meet complexity requirements:** Enabled

![Password policy](../screenshots/gpo-security/03-gpo-password-policy-settings.png)

## 2. Account Lockout Policy

Path:  
`Computer Configuration → Policies → Windows Settings → Security Settings → Account Policies → Account Lockout Policy`

Settings:

- Account lockout threshold: **5 invalid logon attempts**
- Account lockout duration: **15 minutes**
- Reset account lockout counter after: **10 minutes**

![Account lockout policy](../screenshots/gpo-security/05-gpo-account-lockout-settings.png)

## 3. Applying the GPO

After linking the GPO at the domain level:

```powershell
gpupdate /force



---

## 5. `documentation/file-shares.md`

```markdown
# File Shares and Permissions

Root folder: `C:\CompanyShares`

Subfolders:

- `HR`
- `IT`
- `Sales`
- `Public`

![Folder structure](../screenshots/file-shares/01-companyshares-folder-structure.png)

## 1. NTFS Permissions

### HR

- Group: `HR-Group`
- Permissions: **Modify** (this folder, subfolders and files)

### Public

- Group: `Everyone` (lab) or `Domain Users` (more realistic)
- Permissions: **Modify**

![Public NTFS permissions](../screenshots/file-shares/03-public-share-permissions-everyone.png)

## 2. Share Permissions

Shared `C:\CompanyShares` as `\\DC01\CompanyShares`.

Share permissions:

- `Administrators` – Full Control
- `Domain Users` – Read (access controlled by NTFS)

![Share settings](../screenshots/file-shares/04-companyshares-advanced-sharing.png)

## 3. Validation

From the workstation:

- Log in as HR user → confirm HR share access.
- Log in as Sales user → confirm Sales share access.
- Public is accessible to all.

6. documentation/workstation-setup.md
# Workstation Setup & Drive Mapping

## 1. DNS & Domain Join

1. Configure workstation DNS → `10.0.1.129` (DC01).
2. Join domain `helpdesk.local`.
3. Reboot and log in as a domain user.

## 2. Drive Mapping GPO

GPO: `Map HR Drive GPO` (example for HR)

Path:  
`User Configuration → Preferences → Windows Settings → Drive Maps`

Drive:

- Action: **Update**
- Location: `\\DC01\CompanyShares\HR`
- Drive letter: `H:`
- Label as: `HR Share`

![HR drive GPO](../screenshots/gpo-drive-mapping/04-hr-drive-gpo-properties-path.png)

Apply with:

```powershell
gpupdate /force


Confirm in File Explorer:


---

## 7. What you should do now

1. **Create the repo** on GitHub: `homelab-ad-aws` (or any name you like).
2. Locally:
   - Create the folder structure above.
   - Put your images into the matching `screenshots/...` folders.
   - Rename them to the suggested filenames (or update the markdown paths).
3. Copy-paste:
   - `README.md`
   - All files in `documentation/`
4. Commit & push.

If you want, next we can:

- Add a **network diagram** for `architecture-diagrams/`.
- Add a **“Projects”** section in the README that explains how this homelab relates to a helpdesk / sysadmin / jr-sysadmin role.
