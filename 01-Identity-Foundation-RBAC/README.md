# Microsoft Entra ID — Identity Foundation & RBAC

## Project Overview

This lab demonstrates the design and implementation of a foundational Identity and Access Management (IAM) environment using Microsoft Entra ID.

The environment represents **ChimaHealth Systems**, a fictional healthcare technology organization with employees across Clinical Operations, Research, Finance, Human Resources, Sales, IT, and Security.

The project focuses on centralized identity administration, security-group design, Role-Based Access Control (RBAC), least privilege, and separation of duties.

## Objectives

* Create and manage organizational identities in Microsoft Entra ID
* Configure department-based security groups
* Implement group-based identity management
* Delegate administrative privileges using Microsoft Entra built-in roles
* Apply the principle of least privilege
* Demonstrate separation of administrative duties
* Document the resulting IAM architecture

## Environment

**Identity Provider:** Microsoft Entra ID
**Environment Type:** Cloud IAM Lab
**Organization:** ChimaHealth Systems (Fictional)
**Users:** 8 fictional employee identities
**Security Groups:** 8
**Administrative Model:** Microsoft Entra RBAC

> All identities and organizational information shown in this project are fictional and were created solely for cybersecurity training.

---

## Identity Architecture

The simulated organization contains identities representing several business functions:

| Department          | Example Function                    |
| ------------------- | ----------------------------------- |
| Clinical Operations | Clinical services                   |
| Research            | Research operations                 |
| Finance             | Financial operations                |
| Human Resources     | Employee administration             |
| Sales               | Customer-facing operations          |
| IT                  | Technical support and IAM           |
| Security            | Security monitoring and engineering |

Each identity was configured with organizational attributes such as department, job title, and employee type where applicable.

---

## Security Group Design

Department and role-based security groups were created to organize identities according to business function.

Examples include:

* `GRP-ClinicalOps-Users`
* `GRP-Research-Users`
* `GRP-Finance-Users`
* `GRP-HR-Users`
* `GRP-Sales-Users`
* `GRP-IT-HelpDesk`
* `GRP-IT-IAM`
* `GRP-Security-Team`

The naming convention provides a consistent method for identifying the purpose of each group.

Rather than managing resource access individually for every employee, group-based authorization can be used to provide scalable access management.

Conceptually:

`User → Security Group → Resource`

This approach simplifies provisioning and revocation as employees join, change roles, or leave the organization.

---

## Role-Based Access Control

Administrative permissions were delegated according to business responsibilities rather than assigning broad administrative access.

| Identity     | Business Role     | Entra Role             | Purpose                                                                               |
| ------------ | ----------------- | ---------------------- | ------------------------------------------------------------------------------------- |
| Michael Chen | Help Desk Analyst | Helpdesk Administrator | Perform delegated help-desk identity support                                          |
| Marcus Lee   | IAM Analyst       | User Administrator     | Perform user and routine identity administration                                      |
| Robert Davis | Security Engineer | Security Reader        | View security-related information without unnecessary administrative write privileges |

No fictional employee was assigned Global Administrator simply because they worked in IT or Security.

---

## Least Privilege

The environment was designed according to the **principle of least privilege**, meaning identities should receive only the permissions required to perform their authorized business responsibilities.

For example, the IAM Analyst was assigned **User Administrator** rather than **Global Administrator**.

This reduces the potential impact of:

* Credential compromise
* Administrative mistakes
* Privilege abuse
* Excessive access
* Insider threats

---

## Separation of Duties

Administrative responsibilities were distributed across multiple identities.

The Help Desk Analyst performs limited support functions, the IAM Analyst manages identities, and the Security Engineer receives security visibility.

This prevents unnecessary concentration of administrative authority in a single account.

---

## IAM Architecture


                    Microsoft Entra ID
                           |
          +----------------+----------------+
          |                |                |
        Users            Groups          Roles
          |                |                |
    Employees       Department/Role    Administrative
                     Membership          Authority
                           |
             +-------------+-------------+
             |             |             |
          Help Desk       IAM         Security
             |             |             |
         Helpdesk         User         Security
       Administrator  Administrator      Reader


---

## Security Principles Demonstrated

### Role-Based Access Control (RBAC)

Administrative capabilities are granted through defined roles rather than arbitrary individual permissions.

### Least Privilege

Users and administrators receive only the access necessary for their responsibilities.

### Separation of Duties

Administrative responsibilities are divided between different organizational functions.

### Group-Based Access Management

Users are organized into security groups that can be used to manage access at scale.

### Identity Lifecycle Readiness

Department and role information establishes a foundation for future Joiner-Mover-Leaver automation and identity governance.

---

## Screenshots

Screenshots in this project demonstrate:

* Entra user objects
* Department and role-based security groups
* Security group membership
* Helpdesk Administrator assignment
* User Administrator assignment
* Security Reader assignment

Sensitive credentials, authentication information, and unnecessary tenant identifiers are excluded from the repository.

---

## Lessons Learned

* Why group-based access is more scalable than assigning resources directly to individual users 
  - Group-based access is more scalable because permissions are assigned to groups based on job function instead of individually.
    When users join, change roles, or leave the organization, administrators can manage their access simply by changing group membership. 
* How Entra directory roles differ from security groups
  - roles grant administrative permissions within Microsoft Entra ID, such as managing users, groups, or applications.
    Security groups organize users and other identities so access to applications and resources can be assigned collectively.
* Why Global Administrator should be tightly restricted
  - Because it is the highest level of access it has in the entra environment and as such high risk target for malicious actors.
    If the account is compromised, an attacker could modify identities, roles, authentication settings, and other critical configurations.
* How least privilege reduces identity-related risk
  - Users get the minimum access needed to perform their duties.This reduces the potential damage caused by compromised accounts, 
    mistakes, or misuse of privileges.
* Why different administrative responsibilities should be separated
  - It reduces the risk of having one person have access to many different permissions. 
    This reduces the risk of abuse, mistakes, credential compromise, and excessive privilege.

---

## Next Steps

Future labs will extend this identity foundation with:

* Multi-Factor Authentication (MFA)
* Conditional Access
* Joiner-Mover-Leaver lifecycle management
* Enterprise application and SSO integration
* Access Reviews
* Privileged Identity Management (PIM)
* Entitlement Management
* IAM automation with Microsoft Graph and PowerShell
