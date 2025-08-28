# SOAR Integration Platform

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Python Version](https://img.shields.io/badge/python-3.8+-blue.svg)](https://python.org)
[![Docker](https://img.shields.io/badge/docker-%230db7ed.svg?style=flat&logo=docker&logoColor=white)](https://docker.com)
[![Security Rating](https://img.shields.io/badge/security-A-green.svg)](https://github.com/your-username/soar-platform)

> **A comprehensive Security Orchestration, Automation and Response (SOAR) platform integrating Wazuh, TheHive, Cortex, and MISP for automated threat detection, analysis, and response.**


## 🏗️ Architecture

### Core Components

| Component | Version | Purpose | Documentation |
|-----------|---------|---------|---------------|
| **Wazuh** | 4.7+ | SIEM & Security Monitoring | 
| **TheHive** | 5.2+ | Case Management Platform | 
| **Cortex** | 3.1+ | Analysis Engine |
| **MISP** | 2.4.170+ | Threat Intelligence Platform |

<img width="1252" height="400" alt="pfa3" src="https://github.com/user-attachments/assets/656fa8bb-a2b7-45c1-90ce-96564a2b65e7" />

🚀 Features

Real-time Security Monitoring: Wazuh SIEM for comprehensive log analysis and threat detection
Automated Incident Response: Integration with TheHive for case management and workflow automation
Threat Intelligence: MISP integration for IOC correlation and threat hunting
Security Orchestration: Cortex analyzers for automated artifact analysis
Custom Detection Rules: Advanced Wazuh rules for specific threat scenarios
Dashboard & Reporting: Comprehensive security dashboards and automated reporting

## 🔗 IOC Sources

For the **CDB list configuration**, known malware hashes are collected from public threat intelligence sources.  
One of the main repositories used in this project is maintained by **Nextron Systems** 🛡️ :

📂 [Hash IOCs – Nextron Systems](https://github.com/Neo23x0/signature-base/blob/master/iocs/hash-iocs.txt)

![Source: Nextron Systems](https://img.shields.io/badge/IOC%20Source-Nextron%20Systems-blue?logo=github)

![SOAR Workflow]

## 🎯 Overview

This SOAR platform provides an integrated security operations environment that automates threat detection, incident response, and threat intelligence sharing. By combining industry-leading open-source security tools, organizations can achieve faster threat detection, standardized response procedures, and enhanced collaborative threat intelligence.

### Key Features

- **🚨 Automated Alert Processing**: Real-time security event detection and case creation
- **🔍 Intelligent Analysis**: Automated malware analysis, IOC enrichment, and threat hunting
- **📊 Threat Intelligence**: Centralized threat intelligence platform with automatic IOC correlation
- **⚡ Workflow Automation**: Streamlined incident response with customizable playbooks
- **👥 Collaborative Investigation**: Multi-analyst case management and knowledge sharing
- **📈 Metrics & Reporting**: Comprehensive dashboards and incident metrics


### Data Flow Architecture


<img width="1000" height="1024" alt="ChatGPT Image 28 août 2025, 18_03_37" src="https://github.com/user-attachments/assets/b4e66ae5-b513-4349-be1e-43bfe1e8b281" />


```
┌─────────┐    Events    ┌──────────────┐    Analysis    ┌─────────┐
│  Wazuh  │ ──────────► │ TheHive      │ ──────────────► │ Analyst │
│         │             │ + Cortex     │                 │         │
└─────────┘             └──────┬───────┘                 └────┬────┘
                               │                              │
                               │ Enrich Events                │
                               ▼                              │
                        ┌─────────────┐                       │
                        │    MISP     │ ◄─────────────────────┘
                        │Threat Intel │   Search Observables
                        └─────────────┘
```

## 🚀 Quick Start

### Prerequisites

- **Operating System**: Ubuntu 20.04+ / CentOS 8+ / RHEL 8+
- **Memory**: Minimum 16GB RAM (32GB+ recommended)
- **Storage**: 500GB+ available disk space
- **Network**: Internet connectivity for threat intelligence feeds
- **Docker**: Version 20.10+
- **Docker Compose**: Version 2.0+

### Access Points

| Service | URL | Default Credentials |
|---------|-----|-------------------|
| **Wazuh Dashboard** | `https://localhost:5601` | `admin / SecretPassword` |
| **TheHive** | `https://localhost:9000` | `admin@thehive.local / secret` |
| **Cortex** | `https://localhost:9001` | `admin / secret` |
| **MISP** | `https://localhost:8080` | `admin@admin.test / admin` |


### Integration Setup

#### 1. Wazuh → TheHive Integration

```bash
# Configure Wazuh custom integration
cp configs/wazuh/thehive-integration.py /var/ossec/integrations/
chmod 750 /var/ossec/integrations/thehive-integration.py
chown root:wazuh /var/ossec/integrations/thehive-integration.py

# Update Wazuh configuration
echo '<integration>
  <name>thehive-integration.py</name>
  <hook_url>http://thehive:9000</hook_url>
  <api_key>YOUR_THEHIVE_API_KEY</api_key>
  <level>3</level>
</integration>' >> /var/ossec/etc/ossec.conf
```

#### 2. TheHive ↔ MISP Integration

```yaml
# TheHive application.conf
misp {
  servers = [
    {
      name = "MISP-Server"
      url = "http://misp:8080"
      auth {
        type = "key"
        key = "YOUR_MISP_API_KEY"
      }
    }
  ]
}
```

#### 3. Cortex Analyzers Setup

```bash
# Enable built-in analyzers
docker exec -it cortex-container /opt/cortex/bin/cortex \
  -Dconfig.file=/etc/cortex/application.conf \
  -Dlogger.file=/etc/cortex/logback.xml
```



### Custom Alert Rules

```xml
<!-- Wazuh custom rule for high-priority alerts -->
<rule id="100001" level="12">
    <decoded_as>windows-audit</decoded_as>
    <field name="win.eventdata.targetUserName">administrator</field>
    <description>Administrator account login detected</description>
    <group>authentication_success,gdpr_IV_35.7.d,</group>
    <options>no_full_log</options>
</rule>
```

### MISP Feed Integration

```python
# Automated threat intelligence feed processing
import pymisp

def update_threat_feeds():
    """
    Update MISP with latest threat intelligence
    """
    misp = pymisp.ExpandedPyMISP('http://misp:8080', 'API_KEY')
    
    # Fetch and process threat feeds
    feeds = misp.feeds()
    for feed in feeds:
        if feed.enabled:
            misp.fetch_feed(feed.id)
```


## 📚 Documentation

- [Installation Guide](docs/installation.md)
- [Configuration Reference](docs/configuration.md)
- [API Documentation](docs/api.md)
- [Playbook Development](docs/playbooks.md)
- [Integration Examples](docs/integrations.md)
- [Performance Tuning](docs/performance.md)
- [Security Hardening](docs/security.md)



## 🙏 Acknowledgments

- [ls111-cybersec] Thanks for ls111-cybersec for his customs
- [Wazuh Team](https://wazuh.com/) for the comprehensive security platform
- [TheHive Project](https://thehive-project.org/) for incident response platform
- [MISP Project](https://www.misp-project.org/) for threat intelligence sharing
- [Cortex Project](https://github.com/TheHive-Project/Cortex) for analysis engine


