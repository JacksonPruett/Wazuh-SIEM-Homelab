# Wazuh SIEM Homelab Architecture

## Project Overview

This project is a home-lab Security Information and Event Management (SIEM) environment built with Wazuh.

The purpose of the lab is to develop practical experience with:

- SIEM architecture
- Endpoint monitoring
- Security event collection
- Log analysis
- Alert investigation
- Linux administration
- Security monitoring
- Troubleshooting
- Documentation

The environment is designed to simulate a small centralized security monitoring infrastructure.

---

## Architecture

The current environment uses a single Raspberry Pi 5 as the centralized Wazuh infrastructure.

    ┌─────────────────────────┐
    │      Analyst/Admin      │
    │       Desktop PC        │
    │                         │
    │  Wazuh Dashboard        │
    │  Future Security VMs    │
    └────────────┬────────────┘
                 │
               HTTPS
                 │
                 ▼
    ┌──────────────────────────────────────┐
    │             Raspberry Pi 5            │
    │              192.168.12.137           │
    │                                      │
    │  Wazuh Manager                       │
    │  Wazuh Indexer                       │
    │  Wazuh Dashboard                     │
    │  OpenSearch Security                  │
    └──────────────────┬───────────────────┘
                       ▲
                       │
                  Wazuh Agent
                       │
                       │
            ┌──────────┴───────────┐
            │     MacBook Air M4   │
            │        Endpoint      │
            └──────────────────────┘

---

## Components

### Raspberry Pi 5

The Raspberry Pi 5 hosts the centralized Wazuh infrastructure.

**Role:** SIEM server

**Services:**

- Wazuh Manager
- Wazuh Indexer
- Wazuh Dashboard
- OpenSearch Security

**IP Address:**

    192.168.12.137

The deployment is currently configured as a single-node environment rather than a multi-node cluster.

### Wazuh Manager

The Wazuh Manager is responsible for receiving and processing security data from Wazuh agents.

It provides the central management and analysis layer for monitored endpoints.

### Wazuh Indexer

The Wazuh Indexer stores and indexes the security data processed by the Wazuh Manager.

The current Indexer node is:

    node-1

Because this is a single-node deployment, `node-1` is the only Indexer node in the environment.

### Wazuh Dashboard

The Wazuh Dashboard provides the web-based interface used to interact with the SIEM.

The current Dashboard node is:

    dashboard

The Dashboard is accessed over HTTPS at:

    https://192.168.12.137

The laboratory uses locally generated certificates, so a browser may display a certificate trust warning.

### MacBook Air M4

The MacBook Air M4 is being used as the first monitored endpoint.

A Wazuh Agent will collect endpoint security information and forward it to the Wazuh Manager.

### Desktop PC

The desktop serves as the primary analyst and administration workstation.

It will also host virtual machines that can eventually provide additional monitored endpoints and infrastructure.

Planned virtual machines include:

- Windows 11
- Windows Server / Active Directory
- Ubuntu Server
- Additional Linux systems as needed

---

## Data Flow

The intended security-data flow is:

    Endpoint
       │
       │ Wazuh Agent
       ▼
    Wazuh Manager
       │
       ▼
    Wazuh Indexer
       │
       ▼
    Wazuh Dashboard
       │
       ▼
    Analyst

This separates endpoint collection, centralized processing, data storage/indexing, and analyst visualization.

---

## Current Deployment Status

The centralized Wazuh infrastructure has been successfully deployed.

| Component | Status |
|---|---|
| Wazuh Indexer | Operational |
| OpenSearch Security | Initialized |
| Wazuh Manager | Operational |
| Wazuh Dashboard | Operational |
| Dashboard HTTPS | Operational |
| Agents | None registered yet |

The next stage of the project is endpoint deployment and validation.

---

## Future Expansion

The lab will eventually expand beyond the initial MacBook endpoint.

Planned additions include:

- Windows 11 endpoint
- Windows Server / Active Directory environment
- Additional Linux endpoints
- Security event generation
- Detection testing
- Alert investigation
- SIEM validation
- Documentation of detection and response workflows

The goal is to progressively turn the environment into a realistic security operations laboratory.
