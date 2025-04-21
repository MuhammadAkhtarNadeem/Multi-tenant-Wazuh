
# Multi-Tenancy in Wazuh

This repository provides a complete step-by-step guide for implementing **Multi-Tenancy** in the Wazuh security platform using OpenSearch Dashboards. Multi-tenancy enables isolated access for different users or groups to shared Wazuh infrastructure — ideal for companies with multiple departments or managed security service providers (MSSPs) managing multiple clients.

---

## 📘 Project Overview

**Author:** Muhammad Akhtar Nadeem  
**Instructor:** Muhammad Moiz Ud Din Rafay  
**Institution:** Akhuwat College University Kasur  
**Date:** April 13, 2025

---

## 🌐 What is Multi-Tenancy?

Multi-tenancy is a software architecture where a single instance of an application serves multiple users (tenants), with logical data separation. Each tenant operates independently and securely within the shared environment.
---

## 🔐 Multi-Tenancy in Wazuh

Wazuh supports multi-tenancy via OpenSearch Dashboards using tenant-based isolation of dashboards, index patterns, and visualizations.

### Use Case Example:
A company has departments (IT, HR, Finance). All send logs to one Wazuh server but see only their own data in the dashboard, using tenant-based role mapping and policies.

---

## 🛠️ Implementation Guide

### ✅ Prerequisites
- Working Wazuh and OpenSearch environment
- Admin access to Wazuh Dashboard
- Basic knowledge of roles, policies, and OpenSearch security

---

### 📋 Step-by-Step Configuration

#### 🔧 Task 1: Create Agent Groups with Labels
1. Go to **Agents management > Endpoint Groups**
2. Create groups: `Team_A`, `Team_B`
3. Add labels in group config:

```xml
<agent_config>
  <labels>
    <label key="group">Team_A</label>
  </labels>
</agent_config>

<agent_config>
  <labels>
    <label key="group">Team_B</label>
  </labels>
</agent_config>

```
4. Assign agents to the respective groups

🔐 Task 2: Define Roles with Document-Level Security
Go to Security > Roles

Create a role read_Team_A with:

Cluster Permissions:
cluster_composite_ops_ro

Index Permissions:

wazuh-alerts* with DLS:
```xml
{
  "bool": {
    "must": {
      "match": {
        "agent.labels.group": "Team_A"
      }
    }
  }
}
```

wazuh-monitoring* with similar DLS

Assign tenant permissions to global_tenant (read-only)

Map this role to an internal user (e.g., team_a_user)


📜 Task 3: Create and Assign Custom Policies
Go to Server Management > Security > Policies

Create policy Read_Team_A:

Action: agent:read

Resource: agent:group

Resource Identifier: Team_A

Effect: Allow

Create a role using this policy and map it to the internal user


🧩 Task 4: Wazuh App Permissions
Enable run_as in /usr/share/wazuh-dashboard/data/wazuh/config/wazuh.yml

Restart Wazuh Dashboard:
```xml
sudo systemctl restart wazuh-dashboard
```

🔍 Verification
Logout from admin

Login as team_a_user

Confirm only Team_A agents and data are visible

✅ Outcome
With this setup:

Each group/user sees and manages only their assigned data

Data visibility and action permissions are securely isolated per tenant

Demonstrates a production-ready multi-tenant Wazuh configuration """


