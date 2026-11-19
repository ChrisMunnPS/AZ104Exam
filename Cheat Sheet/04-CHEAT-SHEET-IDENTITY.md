# AZ-104 cheat sheet 1/5 — Manage Azure identities and governance

*One page. Facts snapshot 2026-11; verify current behaviour on Microsoft Learn before the exam.*

## Tenants, subscriptions, and hierarchy

| Item | What to remember |
|---|---|
| Resource hierarchy | Management group → subscription → resource group → resource. Policy and RBAC inherit **downward only**. |
| Tenant vs subscription | A tenant (Entra ID) holds identities; a subscription is a billing and scale boundary that **trusts one tenant**. |
| Multiple subscriptions | Billing scopes (billing account → invoice section → subscription) are separate from the resource hierarchy. Move subscriptions between management groups freely. |
| Management groups | Up to **6 levels** below the tenant root, **10,000** per directory; a group or subscription has exactly **one parent**. |
| Moving resources | Moving to a new resource group keeps the resource ID's RG segment; moving to a new subscription changes the whole ID. Both sides must be in the **same region** for most types. |
| Region move | Not supported by "move" for most services — redeploy from a template, or use Azure Resource Mover. |
| Resource group rules | A resource lives in exactly one RG. Deleting the RG deletes everything in it. RGs cannot be nested. |
| Naming and tags | Tags are **not inherited** from RG or subscription. A policy with the `Modify` effect can add or inherit them. |

## Microsoft Entra users and groups

| Item | What to remember |
|---|---|
| User types | **Member** (in tenant) vs **Guest** (B2B). `userType` is informational; authorization comes from role assignments. |
| Create users | Portal, Microsoft Graph PowerShell, Azure CLI (`az ad user create`), bulk CSV import (max **50,000** rows). |
| Delete and restore | Deleted users stay restorable for **30 days** with the same object ID. |
| Groups | **Security** groups grant access only. **Microsoft 365** groups add mailbox, calendar, SharePoint, Teams. **Mail-enabled security** does both. |
| Membership | **Assigned** (manual) or **dynamic** (rule on attributes). Dynamic membership is read-only; you cannot add members by hand. |
| Dynamic rule limits | One rule per group; no nesting of groups; the owner of the attribute can change membership. |
| Role-assignable groups | Created with `isAssignableToRole = true`; cannot be converted afterwards, and cannot be dynamic. |
| Licences | Group-based licensing needs a **usage location** on every user. Removing a member removes the licence. |
| Licence errors | Not enough licences · usage location missing · conflicting service plans · dependent add-on missing. |
| SSPR | Needs **P1/P2** for on-prem writeback; administrators always need **two** methods; writeback requires Entra Connect. |
| External users | Invite by email; the guest redeems in their home tenant. Cross-tenant access settings control inbound and outbound trust. |
| Entra Domain Services | Managed domain for legacy LDAP/Kerberos/NTLM workloads; no domain-admin rights; one-way sync from Entra ID. |

## Azure RBAC

| Item | What to remember |
|---|---|
| Assignment parts | Security principal + role definition + **scope**. Permissions = Actions − NotActions, then deny assignments win. |
| Built-in roles | **Owner** (full + grant access) · **Contributor** (full, no grant) · **Reader** · **User Access Administrator** (grant only) · **Role Based Access Control Administrator** (grant, no data access). |
| Scope choice | Assign at the **highest** scope that still satisfies least privilege; group membership beats per-user assignments. |
| Deny assignments | Created by managed apps and Blueprints, not by you. They **block even Owners** and cannot be overridden. |
| Custom roles | JSON with `Actions`, `NotActions`, `DataActions`, `AssignableScopes`. One subscription per custom role by default; add all scopes you need. |
| Effective access | Check **Access control (IAM) → Check access**, or the **Effective permissions** blade, before troubleshooting. |
| Classic administrators | Co-administrator and Service Administrator are legacy; migrate to RBAC. |
| Audit | Role assignment changes appear in the **Activity log**; sign-ins in Entra **sign-in logs**. |

## Azure Policy

| Item | What to remember |
|---|---|
| Definitions and initiatives | A policy definition is one rule; an **initiative** groups definitions for one assignment. Assign at management group for the widest reach. |
| Effects | `Deny`, `Audit`, `Append`, `Modify`, `DeployIfNotExists`, `AuditIfNotExists`, `Disabled`. `Deny` blocks new and updated resources only. |
| Remediation | `deployIfNotExists` and `modify` need a **managed identity** and a **remediation task**. Existing non-compliant resources are not fixed automatically. |
| Exemptions | **Not required** (waiver) or **Mitigated** (category + expiry). Exemptions are auditable and time-bounded. |
| Compliance | Evaluation runs about every **24 hours**, or on demand. The compliance pane shows per-resource and per-policy state. |
| Policy vs RBAC | RBAC controls **who can do what**; Policy controls **what the result may look like**. |

## Locks, cost, and tooling

| Item | What to remember |
|---|---|
| Resource locks | **CanNotDelete** and **ReadOnly**. They apply to the scope and everything below; `ReadOnly` blocks reads of keys/secrets in some services. |
| Lock ownership | Locks are inherited from the parent scope. A lock does not stop a **deny assignment** or a policy. |
| Cost tools | **Cost analysis** (slice by tag/RG/resource), **budgets** (alert at a threshold), **Advisor** recommendations, **exports** to storage. |
| Azure Advisor | Reliability, security, performance, cost, operational excellence — free, per subscription. |
| CLI vs PowerShell | `az` (Bash/PowerShell) and `Az` module. `az role assignment create` / `New-AzRoleAssignment`; `az policy assignment create` / `New-AzPolicyAssignment`. |
| Sign-in blocking | `az ad user update --account-enabled false` or `Update-MgUser -AccountEnabled:$false`. Requires User Administrator or higher. |
