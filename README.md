# Cloud-Native SOAR: Automated Threat Response & Perimeter Auto-Ban

- **Cloud Security Engineer:** Harmandeep Singh
- **Focus Area:** Security Orchestration, Automation, and Response (SOAR), AWS Boto3 SDK, CloudSecOps
- **Target Roles:** Cloud Security Engineer | Tier-2 SOC Analyst | Security Automation Specialist

---

## 1. Executive Summary
Architected and deployed a cloud-native Security Orchestration, Automation, and Response (SOAR) pipeline on AWS. Engineered an active response daemon in Python utilizing the AWS Boto3 SDK that intercepts intrusion attempts, resolves real-time geolocation intelligence, programmatically calculates rule priorities, and injects dynamic `DENY` rules into AWS Network ACLs (NACLs) to drop malicious traffic at the subnet boundary in under 1 second. Dispatches structured incident telemetry to a centralized SOC webhook channel.

---

## 2. Architecture Overview

```text
    [ External Threat Actor / Bot ]
                 │
                 ▼ (TCP 22 SSH Probe)
    ┌────────────────────────────────────────────────────────┐
    │  AWS EC2 Instance (t3.micro)                           │
    │  • Systemd intercepts authentication failure           │
    │  • Python SOAR Daemon (soar_autoban.py) parses IP      │
    └────────────────────────────┬───────────────────────────┘
                                 │
                 ┌───────────────┴───────────────┐
                 ▼                               ▼
    [ Action 1: AWS Boto3 API ]     [ Action 2: SOC Notification ]
     • ec2.create_network_acl_entry  • Dispatches HTTP POST Webhook
     • Injects DENY Rule into NACL   • Real-time Discord/Slack Card
       (acl-03cd128a9e8d45489)         with IP, Geo & MTTR
                 │                               │
                 ▼                               ▼
     Subnet perimeter drops all       SOC team notified with
     future attacker packets!         sub-second response time
