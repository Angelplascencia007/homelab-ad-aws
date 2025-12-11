# homelab-ad-awshomelab-ad-aws/
├─ README.md
├─ architecture-diagrams/
│  └─ homelab-network-diagram.png       # (optional – you can make one later)
├─ documentation/
│  ├─ ad-setup.md
│  ├─ gpo-hardening.md
│  ├─ file-shares.md
│  └─ workstation-setup.md
└─ screenshots/
   ├─ aws/
   │  ├─ 01-aws-console-ec2-dashboard.png
   │  ├─ 02-vpc-settings.png
   │  ├─ 03-subnet-settings.png
   │  ├─ 04-route-table-settings.png
   │  ├─ 05-internet-gateway.png
   │  ├─ 06-dc01-instance-details.png
   │  ├─ 07-dc01-rdp-connect.png
   │  ├─ 08-workstation01-instance-details.png
   │  └─ 09-workstation01-rdp-connect.png
   ├─ domain-controller/
   │  ├─ 01-server-manager-dashboard.png
   │  ├─ 02-promote-to-domain-controller.png
   │  ├─ 03-ad-ds-install-complete.png
   │  └─ 04-dc01-about-windows-server.png
   ├─ dns/
   │  ├─ 01-nslookup-helpdesk-local-timeout.png
   │  └─ 02-dns-manager-forward-lookup-zone.png
   ├─ active-directory/
   │  ├─ 01-aduc-root-ous.png
   │  ├─ 02-aduc-sales-ou.png
   │  ├─ 03-aduc-hr-ou.png
   │  ├─ 04-aduc-it-ou.png
   │  ├─ 05-aduc-workstations-ou-empty.png
   │  └─ 06-aduc-workstation-object-ec2amaz.png
   ├─ users-and-groups/
   │  ├─ 01-create-user-john-parker-sales.png
   │  ├─ 02-create-user-maria-lopez-hr.png
   │  ├─ 03-create-user-james-cruz-it.png
   │  ├─ 04-test-user1-root.png
   │  ├─ 05-helpdesk-users-ou.png
   │  ├─ 06-it-group-membership.png
   │  ├─ 07-rdp-access-group-membership.png
   │  └─ 08-add-users-to-groups.png
   ├─ gpo-security/
   │  ├─ 01-gpmc-create-security-baseline-gpo.png
   │  ├─ 02-gpo-password-account-lockout-kerberos.png
   │  ├─ 03-gpo-password-policy-settings.png
   │  ├─ 04-gpo-account-lockout-settings.png
   │  └─ 05-gpupdate-force-success.png
   ├─ file-shares/
   │  ├─ 01-companyshares-folder-structure.png
   │  ├─ 02-companyshares-ntfs-permissions.png
   │  ├─ 03-public-share-permissions-everyone.png
   │  ├─ 04-companyshares-advanced-sharing.png
   │  └─ 05-companyshares-share-permissions.png
   ├─ gpo-drive-mapping/
   │  ├─ 01-gpmc-company-departments-gpo.png
   │  ├─ 02-drive-maps-empty.png
   │  ├─ 03-new-mapped-drive-hr.png
   │  ├─ 04-hr-drive-gpo-properties-path.png
   │  └─ 05-hr-drive-gpo-properties-label.png
   └─ workstation/
      ├─ 01-workstation-joined-to-domain.png
      ├─ 02-workstation-about-windows.png
      ├─ 03-gpupdate-force-workstation.png
      ├─ 04-mapped-drives-visible.png
      └─ 05-hr-share-access-test.png
