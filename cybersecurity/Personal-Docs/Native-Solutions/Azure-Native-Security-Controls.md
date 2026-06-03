# Azure Native Security Services Overview

This repository is built around a set of core **Azure-native security services** that together enforce governance, strengthen identity controls, and improve overall cloud security posture.

---

## 1. Azure Policy

### Purpose
Azure Policy is the **preventive control mechanism** used to enforce governance and compliance across the environment.

### Key Capabilities
- Enforces **deny, audit, and configuration guardrails**
- Supports **policy exemption tracking and reporting**
- Prevents non-compliant configurations at deployment time


---

## 2. Microsoft Defender for Cloud

### Purpose
Provides **Cloud Security Posture Management (CSPM)** and workload protection across Azure resources.

### Key Capabilities
- Detects **misconfigurations and security risks**
- Generates **security recommendations** aligned to industry benchmarks
- Provides a **secure score** for measuring posture over time

### Note
- **Defender for cloud is in POC.**

---

## 3. Azure IAM (Identity and Access Management)

### Purpose
Controls **who can access Azure resources** and **what actions they can perform**, forming the foundation of the security model.

### Key Capabilities
- **Azure RBAC** for granular role-based access control
- **Custom roles** to enforce least privilege
- Integration with **Microsoft Entra ID** for identity governance


---

## Summary

This repository demonstrates a **policy-driven, Azure-native security architecture** built on:

- **Preventive controls** → Azure Policy
- **Posture management** → Microsoft Defender for Cloud
- **Identity and access** → Azure IAM

Together, these services provide a **layered security model** aligned with enterprise cloud security best practices.

---