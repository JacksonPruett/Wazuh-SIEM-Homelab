# Wazuh Agents

## Overview

This document records the deployment and enrollment of the first two Wazuh agents in the homelab environment.

The Wazuh Manager is hosted on the Raspberry Pi 5 at:

    192.168.12.137

Two endpoints have been successfully enrolled and are currently reporting an **Active** status.

- MacBook Air M4 — Agent `001`
- Windows Desktop — Agent `002`

## Agent Architecture

    ┌───────────────────┐
    │   MacBook Air M4  │
    │     Agent 001     │
    └─────────┬─────────┘
              │
              │ Wazuh Agent
              │
              ▼
    ┌──────────────────────┐
    │    Wazuh Manager     │
    │    192.168.12.137    │
    │       wazuh-1        │
    └──────────┬───────────┘
              ▲
              │
              │ Wazuh Agent
              │
    ┌─────────┴─────────┐
    │  Windows Desktop  │
    │     Agent 002     │
    │     JacksonPC     │
    └───────────────────┘

## Current Agents

| Agent ID | Name | Endpoint | Status |
|---|---|---|---|
| 001 | Mac.lan | MacBook Air M4 | Active |
| 002 | JacksonPC | Windows Desktop | Active |

## MacBook Air M4

### Installation

The Apple Silicon ARM64 Wazuh Agent package was installed on the MacBook Air M4.

The agent installation completed successfully and the Wazuh service was loaded using `launchctl`.

### Manager Configuration

The Wazuh agent configuration file is:

    /Library/Ossec/etc/ossec.conf

The agent was initially configured with an incorrect Manager address:

    10.0.0.2

The address was changed to the Wazuh Manager running on the Raspberry Pi:

    192.168.12.137

The agent communication configuration was:

    Port: 1514
    Protocol: TCP

### Agent Restart

After modifying `ossec.conf`, the agent was restarted with:

    sudo /Library/Ossec/bin/wazuh-control restart

The agent status was then verified with:

    sudo /Library/Ossec/bin/wazuh-control status

The agent reported a running status.

### Enrollment Verification

The Wazuh Manager was queried using:

    sudo /var/ossec/bin/agent_control -l

The MacBook appeared as:

    ID: 001
    Name: Mac.lan
    IP: any
    Status: Active

The endpoint was also visible in the Wazuh Dashboard.

## Windows Desktop

### Installation

The Wazuh Agent was installed on the Windows desktop using the graphical installer.

During installation, the endpoint was automatically configured and enrolled with the Wazuh Manager.

Unlike the initial investigation suggested, a manually generated authentication key was not required for the successful Windows deployment.

### Enrollment Verification

The Wazuh Manager was queried using:

    sudo /var/ossec/bin/agent_control -l

The Windows desktop appeared as:

    ID: 002
    Name: JacksonPC
    IP: any
    Status: Active

The endpoint was also visible in the Wazuh Dashboard.

## Agent Enrollment

The initial MacBook deployment demonstrated that installing the agent and configuring the Manager address were separate steps.

The MacBook was initially pointed at:

    10.0.0.2

After changing the configuration to:

    192.168.12.137

and restarting the agent, the endpoint successfully enrolled with the Manager.

The Windows deployment subsequently demonstrated that the graphical installer could complete enrollment automatically without manually generating or importing an authentication key.

## Troubleshooting and Lessons Learned

### MacBook Manager Address

The MacBook agent was initially configured with an incorrect Manager address.

**Initial configuration:**

    10.0.0.2

**Correct Manager address:**

    192.168.12.137

The configuration was corrected in:

    /Library/Ossec/etc/ossec.conf

The agent was restarted and subsequently appeared as Active.

### Unused Windows Agent Entry

During the Windows deployment, a manual authentication-key workflow was initially investigated.

An additional agent entry was temporarily created:

    ID: 003
    Name: Windows-Desktop
    IP: any
    Status: Never connected

The Windows installation subsequently enrolled automatically as Agent `002` (`JacksonPC`), making Agent `003` unnecessary.

The unused entry was removed.

### Key Lesson

Agent installation, enrollment, and verification are separate stages:

    Installation
          ↓
    Manager Configuration
          ↓
    Enrollment
          ↓
    Authentication
          ↓
    Active Agent
          ↓
    Telemetry Collection

A successfully installed agent is not necessarily a successfully enrolled or communicating agent.

## Current Status

### Milestone 2 — Endpoint Enrollment

**Complete**

The first two endpoints are successfully enrolled and actively communicating with the Wazuh Manager.

Current endpoints:

- MacBook Air M4 — Agent `001`
- Windows Desktop — Agent `002`

The next phase is to validate endpoint telemetry, generate test security events, and investigate how those events appear within the Wazuh Dashboard.
