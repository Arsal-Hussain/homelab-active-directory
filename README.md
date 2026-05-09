# Active Directory Homelab

A fully functional on-premises Active Directory environment built on a virtualized infrastructure using UTM on Apple Silicon. This project demonstrates core IAM and sysadmin competencies including identity lifecycle management, role-based access control, Group Policy administration, security policy configuration, and audit logging.

---

## Environment

| Component | Details |
|---|---|
| Hypervisor | UTM (QEMU-based) on macOS |
| Domain Controller | Windows Server 2022 (x64 emulated) |
| Domain | homelab.local |
| Services | AD DS, DNS |

---

## Identity & Access Management

### Organizational Unit Structure

```
homelab.local
├── AllComputers
│   ├── Servers
│   └── Workstations
├── AllGroups
│   └── RoleGroups
│       ├── RG_CSDept
│       ├── RG_ITDept
│       └── RG_Management
└── AllUsers
    ├── Customer Service Department
    │   └── Jim Halpert
    ├── IT Department
    │   └── Jason Smith
    └── Management
        └── Asim Munir
```

![ADUC OU Structure and Users](Screenshots/Screenshot_2026-05-09_at_3_36_43_PM.png)

---

### Users

| Name | Username | Department |
|---|---|---|
| Jim Halpert | jhalpert | Customer Service |
| Jason Smith | jsmith2 | IT Department |
| Asim Munir | amunir | Management |

### Security Groups

| Group | Members | Purpose |
|---|---|---|
| RG_CSDept | jhalpert | Customer Service access |
| RG_ITDept | jsmith2 | IT Department access |
| RG_Management | amunir | Management access |

---

## Group Policy Objects

![Group Policy Management](Screenshots/Screenshot_2026-05-09_at_3_37_20_PM.png)

### GPO_CSDept (Customer Service)
- Prohibit access to Control Panel and PC Settings
- Remove Run menu from Start Menu
- Prevent access to Command Prompt

### GPO_ITDept (IT Department)
- Interactive logon warning message
- Unrestricted PowerShell and system access

### GPO_Management (Management)
- Interactive logon message
- Prohibit access to Control Panel and PC Settings
- Prevent access to Command Prompt

---

## Security Policies

### Password Policy

| Setting | Value |
|---|---|
| Enforce password history | 10 passwords remembered |
| Maximum password age | 90 days |
| Minimum password age | 1 day |
| Minimum password length | 8 characters |
| Password must meet complexity requirements | Enabled |
| Store passwords using reversible encryption | Disabled |

![Password Policy](Screenshots/Screenshot_2026-05-09_at_4_05_39_PM.png)

### Account Lockout Policy

| Setting | Value |
|---|---|
| Account lockout duration | 30 minutes |
| Account lockout threshold | 5 invalid logon attempts |
| Reset account lockout counter after | 15 minutes |

![Account Lockout Policy](Screenshots/Screenshot_2026-05-09_at_4_06_18_PM.png)

### Audit Policy

| Event | Setting |
|---|---|
| Audit account logon events | Success, Failure |
| Audit account management | Success, Failure |
| Audit logon events | Success, Failure |
| Audit object access | Success, Failure |
| Audit policy change | Success, Failure |
| Audit privilege use | Success, Failure |
| Audit system events | Success, Failure |

![Audit Policy](Screenshots/Screenshot_2026-05-09_at_4_06_04_PM.png)

---

## File Shares

Department shares configured with NTFS permissions enforcing least privilege access. Each security group has Modify access only to their designated share.

| Share | Path | Access |
|---|---|---|
| CustomerService | C:\Shares\CustomerService | RG_CSDept |
| IT | C:\Shares\IT | RG_ITDept |
| Management | C:\Shares\Management | RG_Management |

![File Shares](Screenshots/Screenshot_2026-05-09_at_4_04_01_PM.png)

---

## Scripts

### set-ntfs-permissions.ps1
PowerShell script to automate NTFS permission assignment for department shares using security group-based access control.

```powershell
# Customer Service
$acl = Get-Acl "C:\Shares\CustomerService"
$rule = New-Object System.Security.AccessControl.FileSystemAccessRule("homelab\RG_CSDept","Modify","Allow")
$acl.SetAccessRule($rule)
Set-Acl "C:\Shares\CustomerService" $acl

# IT
$acl = Get-Acl "C:\Shares\IT"
$rule = New-Object System.Security.AccessControl.FileSystemAccessRule("homelab\RG_ITDept","Modify","Allow")
$acl.SetAccessRule($rule)
Set-Acl "C:\Shares\IT" $acl

# Management
$acl = Get-Acl "C:\Shares\Management"
$rule = New-Object System.Security.AccessControl.FileSystemAccessRule("homelab\RG_Management","Modify","Allow")
$acl.SetAccessRule($rule)
Set-Acl "C:\Shares\Management" $acl
```

---

## Skills Demonstrated

- Active Directory Domain Services (AD DS) deployment and configuration
- Organizational Unit (OU) design and user lifecycle management
- Role-based access control (RBAC) using security groups
- Group Policy Object (GPO) creation, linking, and enforcement
- Password and account lockout policy configuration
- Audit policy configuration for compliance and monitoring
- NTFS and SMB share permissions with least privilege principles
- Windows Server 2022 administration
- PowerShell scripting for access control automation
