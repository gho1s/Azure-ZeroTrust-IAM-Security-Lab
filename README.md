# Azure Zero Trust Identity & Access Management (IAM) Lab

**Microsoft Azure · Microsoft Entra ID · Azure RBAC · MFA · Least Privilege**

## Project overview

This hands-on lab demonstrates how to implement **group-based, least-privilege access** in Microsoft Azure. I created a dedicated Azure resource group, configured a Microsoft Entra ID security group and test user, assigned the built-in **Reader** role at the resource-group scope, and validated access by signing in as the restricted user.

> **Scope:** This is an Azure RBAC / identity-access lab illustrating *Zero Trust principles*. It is not a full enterprise Zero Trust architecture or a production deployment.

## Objectives

- Create an isolated Azure resource group for IAM testing.
- Provision a Microsoft Entra ID security group and test identity.
- Grant **Reader** permissions to the group, scoped only to the lab resource group.
- Complete multifactor authentication (MFA) enrollment for the test account during sign-in.
- Validate the test user's effective role and limited IAM management controls.

## Environment

| Component | Configuration |
| --- | --- |
| Cloud | Microsoft Azure |
| Identity provider | Microsoft Entra ID |
| Resource group | `Azure-ZeroTrust-SecurityLab` |
| Security group | `ZeroTrust-Security-Readers` |
| Test identity | `Zero Trust Lab Reader` |
| RBAC role | Built-in `Reader` |
| Assignment scope | Resource group (not entire subscription) |
| Authentication | Test-account sign-in with MFA registration |

## Implementation and evidence

### 1. Prepare the lab resource group

Created `Azure-ZeroTrust-SecurityLab` to isolate the scope of the permissions test.

![Lab resource group](screenshots/01-resource-group-created.png)

### 2. Configure an Entra ID security group

Created `ZeroTrust-Security-Readers` to manage access by group membership instead of assigning a role directly to an individual identity.

![Entra security group](screenshots/02-entra-security-group.png)

### 3. Provision the test identity

Created a dedicated test user, `Zero Trust Lab Reader`, and associated it with the security group. The test identity was kept separate from the administrator account.

![Test identity](screenshots/03-test-user-created.png)

### 4. Assign Azure RBAC Reader access

Used **Access control (IAM)** on the resource group to assign the built-in **Reader** role to the security group. The role grants management-plane read access within the scope but does not grant resource modification rights.

![Reader assignment configuration](screenshots/04-reader-role-assignment-setup.png)

![RBAC role assignments](screenshots/05-rbac-role-assignments.png)

### 5. Validate access as the restricted identity

Signed into the Azure portal using a separate browser session for the test account, completed the prompted Microsoft Authenticator registration, and verified the lab resource group was visible.

![Resource group visible to Reader](screenshots/06-reader-resource-group-visibility.png)

### 6. Inspect access restrictions and effective permissions

From the test identity, observed that **Add role assignment** was disabled in IAM. Inspected the built-in Reader role and confirmed the test account's effective **Reader** assignment was inherited through the `ZeroTrust-Security-Readers` group at **This resource** scope.

![IAM under Reader](screenshots/07-reader-iam-limited-controls.png)

![Reader role permissions](screenshots/08-reader-role-permissions.png)

![Effective group-based Reader assignment](screenshots/09-effective-reader-group-assignment.png)

## Validation results

| Test | Observed result | Outcome |
| --- | --- | --- |
| Sign in as test identity | Azure portal opened under restricted account | Passed |
| MFA registration | Microsoft Authenticator enrollment completed during sign-in | Passed |
| View scoped resource group | `Azure-ZeroTrust-SecurityLab` appeared in Resource groups | Passed |
| Inspect effective access | Reader role shown, granted via security group, at resource-group scope | Passed |
| Manage IAM role assignments | Add role assignment control disabled for test account | Passed (UI restriction) |
| Attempt a resource write/delete operation | Not performed | Not tested |

**Important limitation:** A disabled IAM control supports the least-privilege result but is **not** a substitute for an explicit denied write-operation test. This lab does not claim that every resource write or delete action was individually tested. Reader is an Azure management-plane role and does not necessarily grant access to application data inside resources.

## Security principles demonstrated

- **Least privilege:** Read-only Azure management permissions for the test identity.
- **Role-based access control (RBAC):** Permissions assigned using the built-in Reader role.
- **Group-based administration:** Group membership provides access rather than a direct user role assignment.
- **Scope minimization:** Assignment limited to the lab resource group.
- **Identity verification:** Separate test account and MFA enrollment.
- **Access validation:** Tested the identity's visible resources and inspected effective role assignments.

## Skills and tools

`Microsoft Azure` · `Microsoft Entra ID` · `Azure RBAC` · `IAM` · `MFA` · `Least Privilege` · `Resource Groups` · `Cloud Security`

## Lessons learned

This lab reinforced the difference between **authentication** (proving identity) and **authorization** (controlling what an identity can do). It also demonstrated why role assignments should be scoped narrowly and verified from the perspective of a non-admin test account.

## Future improvements

- Attempt a harmless write operation and capture the resulting authorization denial without modifying or incurring charges.
- Compare Reader versus Contributor permissions in a controlled environment.
- Explore Privileged Identity Management (PIM), Conditional Access, and access reviews where licensing allows.
- Automate deployment and role assignments with Bicep or Terraform.

## Repository structure

```text
Azure_Zero_Trust_IAM_GitHub_Portfolio/
├── README.md
└── screenshots/
    ├── 01-resource-group-created.png
    ├── 02-entra-security-group.png
    ├── 03-test-user-created.png
    ├── 04-reader-role-assignment-setup.png
    ├── 05-rbac-role-assignments.png
    ├── 06-reader-resource-group-visibility.png
    ├── 07-reader-iam-limited-controls.png
    ├── 08-reader-role-permissions.png
    └── 09-effective-reader-group-assignment.png
```

**Portfolio note:** Screenshots are selected to avoid sharing passwords, MFA enrollment QR codes, and recovery secrets. Review images again before publishing them publicly, as Azure portal screenshots may still reveal non-secret tenant or subscription metadata.
