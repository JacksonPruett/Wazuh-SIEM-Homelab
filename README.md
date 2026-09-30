# Wazuh SIEM Homelab

A hands-on cybersecurity homelab focused on deploying, configuring, troubleshooting, and validating a Wazuh Security Information and Event Management (SIEM) platform.

This project documents the development of a centralized security monitoring environment from initial deployment through endpoint onboarding, detection engineering, and validation.

## Project Overview

The goal of this project is to build practical experience with:

- SIEM architecture and administration
- Security event collection and analysis
- Endpoint monitoring
- Log management
- Detection and alerting
- Linux administration
- Network security
- Troubleshooting and incident investigation
- Security documentation
- Git and GitHub-based project management

## Architecture

                         ┌─────────────────────────┐
                         │       Desktop PC        │
                         │     Analyst / Admin     │
                         │   Virtualization Host   │
                         └────────────┬────────────┘
                                      │
                                      │ HTTPS
                                      ▼
                         ┌─────────────────────────┐
                         │    Raspberry Pi 5       │
                         │    192.168.12.137       │
                         │                         │
                         │    Wazuh Dashboard      │
                         │    Wazuh Manager        │
                         │    Wazuh Indexer        │
                         └────────────┬────────────┘
                                      │
                  ┌───────────────────┼───────────────────┐
                  │                   │                   │
                  ▼                   ▼                   ▼
          ┌──────────────┐    ┌──────────────┐    ┌──────────────┐
          │ Windows 11   │    │ Windows      │    │ Linux Mint   │
          │ Pro          │    │ Server 2025  │    │ VM           │
          │ Agent 002    │    │ Agent 003    │    │ Agent 004    │
          └──────────────┘    └──────────────┘    └──────────────┘

                  ┌───────────────────┴───────────────────┐
                  │                                       │
                  ▼                                       ▼
          ┌──────────────┐                        ┌──────────────┐
          │ MacBook Air  │                        │ Kali Linux   │
          │ M4           │                        │ VM           │
          │ Agent 001    │                        │ Agent 005    │
          └──────────────┘                        └──────────────┘

The Raspberry Pi 5 hosts the central Wazuh infrastructure as a single-node deployment:

- Wazuh Manager
- Wazuh Indexer
- Wazuh Dashboard

The desktop PC serves as the analyst and administration workstation and will also host future virtual machines for additional monitored endpoints.

## Current Infrastructure

| Component | Role | Status |
|---|---|---|
| Raspberry Pi 5 | Wazuh server infrastructure | Operational |
| Wazuh Manager | Event collection and security management | Operational |
| Wazuh Indexer | Security event indexing and storage | Operational |
| Wazuh Dashboard | Web-based monitoring interface | Operational |
| MacBook Air M4 | Wazuh agent endpoint | Planned |
| Desktop PC | Analyst workstation / virtualization host | Operational |

## Deployment Status

### Milestone 1 — Central Wazuh Infrastructure

**Complete**

- Wazuh Indexer deployed
- OpenSearch Security initialized
- Wazuh Manager deployed
- Wazuh Dashboard deployed
- Dashboard-to-Indexer communication validated
- TLS certificates configured
- Dashboard migrations completed
- Wazuh monitoring index created
- Dashboard accessible over HTTPS

### Next Steps

- Deploy Wazuh Agent to MacBook
- Register and validate the first endpoint
- Begin collecting endpoint telemetry
- Deploy additional Windows and Linux virtual machines
- Develop and test detection rules
- Generate security events for validation
- Document testing and results

## Documentation

Detailed project documentation is organized by deployment component:

- [Architecture](documentation/01-architecture.md)
- [Initial Installation](documentation/02-initial-installation.md)
- [Wazuh Indexer](documentation/03-indexer.md)
- [Wazuh Manager](documentation/04-wazuh-manager.md)
- [Wazuh Dashboard](documentation/05-wazuh-dashboard.md)
- [Wazuh Agents](documentation/06-agents.md)
- [Testing and Validation](documentation/07-testing-and-validation.md)

Troubleshooting and lessons learned are maintained separately:

- [Errors and Solutions](troubleshooting/errors-and-solutions.md)

## Lessons Learned

This project intentionally documents both successful deployments and failed attempts.

The initial Wazuh deployment encountered operating system compatibility issues, certificate and trust configuration problems, repository permission issues, and OpenSearch Security initialization problems.

Rather than hiding these failures, they are documented as part of the development process and used to demonstrate troubleshooting methodology and lessons learned.

## Technologies

- Wazuh
- OpenSearch
- Linux
- Raspberry Pi
- Git
- GitHub
- TLS / PKI
- SIEM
- Endpoint Security
- Security Monitoring
