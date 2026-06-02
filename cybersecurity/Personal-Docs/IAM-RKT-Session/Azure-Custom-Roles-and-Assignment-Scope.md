# Azure Custom Roles — Creation & Usage Across Volvo Cars Repos

> Purpose: Client-facing reverse KT material summarizing our understanding of the Azure **custom role definitions** are authored, stored, and consumed.

---

## 1. Core Principles Applied

1. Prefer built-in roles; custom roles only fill a real gap.
2. Permissions are scoped tight — heavy use of `not_actions` to block destructive operations.
3. Single source of truth — vending workflows **look up** central roles by name, never re-define them.
4. Naming conventions encode the **owner** and **capability** (e.g. `[FinOps]-…`, `[PCE]-…`, `(GF)`, `(RCN)`, `-LimitAction`).

---

## 2. Custom roles — `azure-core-infrastructure`

Two parallel authoring patterns live here.

### 2.1 Pattern A — YAML-driven catalog (preferred for scale)

**Layout**

```
DR/Layer0b/
├── custom_roles.tf              # generic loader
└── config/custom-roles/
    ├── ResourceLock_Mgmt.yaml
    ├── CNAPP_Reader.yaml
    ├── FinOps-Turbonomics-Reader.yaml
    └── Reader-CustomRole.yaml
```

**YAML schema (one file per role)**

> **Reference**: [`azure-core-infrastructure` → `DR/Layer0b/config/custom-roles/*.yaml`](https://github.com/volvo-cars/azure-core-infrastructure/tree/main/DR/Layer0b/config/custom-roles)

**Generic Terraform loader (`custom_roles.tf`)**

> **Reference**: [`azure-core-infrastructure` → `DR/Layer0b/custom_roles.tf`](https://github.com/volvo-cars/azure-core-infrastructure/blob/main/DR/Layer0b/custom_roles.tf)

> **Why this matters**: Adding a new role is a one-file PR. No Terraform changes required.

### 2.2 Pattern B — Direct Terraform resource

Used in `gov/L1/enterprise-scale/Foundation/custom-roles.tf` for platform-critical roles that benefit from inline review.

> **Reference**: [`azure-core-infrastructure` → `gov/L1/enterprise-scale/Foundation/custom-roles.tf`](https://github.com/volvo-cars/azure-core-infrastructure/blob/main/gov/L1/enterprise-scale/Foundation/custom-roles.tf)

### 2.3 Roles authored in this repo

| Role | Purpose |
|---|---|
| `ResourceLock_Mgmt` | Read-everything + manage resource locks |
| `CNAPP_Reader` | Read scope for Prisma Cloud / CNAPP onboarding |
| `[FinOps]-Turbonomics-Reader` | FinOps monitoring (Turbonomic) |
| `Reader-CustomRole` | Platform-management reader |
| `Custom-Role-Network(DC-Exit)` | Network role for DC-Exit area troubleshoot |
| `IaC-Pce-Deployment` | Root-level IaC deployment identity |
| `Owner_No_Delete` | Owner without delete capability |
| `Subscription owner(root)` | Delegated subscription Owner |

---

## 3. Custom Roles — `azure-global-enterprise-scale`

Authors custom roles **inline in Terraform**, both as a `locals` map (looped) and as standalone resources for platform roles.

### 3.1 Pattern A — `locals` map + `for_each`

File: `code/L0/custom_role_definitions.tf`

> **Reference**: [`azure-global-enterprise-scale` → `code/L0/custom_role_definitions.tf`](https://github.com/volvo-cars/azure-global-enterprise-scale/blob/main/code/L0/custom_role_definitions.tf)

### 3.2 Pattern B — Standalone `azurerm_role_definition`

File: `code/L0/mg1.tf` — used for the most foundational platform roles.

> **Reference**: [`azure-global-enterprise-scale` → `code/L0/mg1.tf`](https://github.com/volvo-cars/azure-global-enterprise-scale/blob/main/code/L0/mg1.tf)

### 3.3 Roles authored in this repo

| Role | Type | Key intent |
|---|---|---|
| `[PCE] - Reader_No_Logs` | Read | Reader minus Log Analytics |
| `Rubrik-Cloud-Native(RCN)` | Action | Backup / recovery agent |
| `[FinOps]-Turbonomics-Reader` | Read | FinOps monitoring |
| `[FinOps]-Turbonomics-Reader&Execution` | Action + data | FinOps + VM start/stop + blob R/W |
| `NSG_contributor` | Action | NSG, flow logs, deployments |
| `Owner-LimitAction` | Owner-derived | Owner minus subscription / capacity writes |
| `IaC-Pce-Deployment` | Owner-derived | Root-level IaC deployments |

---

## 4. Authoring Checklist

Before opening a PR for a new custom role:

- [ ] Confirmed no built-in role or existing custom role meets the need.
- [ ] `actions` / `data_actions` are the **minimum** required.
- [ ] `not_actions` blocks destructive or privilege-escalation paths:
      `Microsoft.Authorization/elevateAccess/action`,
      `Microsoft.Subscription/*/(write|delete|action)`,
      `Microsoft.Authorization/roleDefinitions/(write|delete)`.
- [ ] `assignable_scopes` is the smallest scope that still allows reuse (root MG is acceptable when the role is platform-wide).
- [ ] Name follows the prefix convention (`[Team]-RoleName` / `(GF)` / `-LimitAction`).
- [ ] Owner and review approver are clear in the PR description.
- [ ] If consumed by vending machine, the `data.azurerm_role_definition` lookup is updated in repo 3.

---

## 5. Quick Reference — Naming Conventions

| Token | Meaning |
|---|---|
| `[PCE]` | Platform Core Engineering owned |
| `[FinOps]` | FinOps domain |
| `(GF)` | Greenfield variant |
| `(RCN)` | Rubrik Cloud Native |
| `-LimitAction` | Owner-derived with destructive ops removed |
| `_Reader` / `_Execution` | Capability suffix |

---
