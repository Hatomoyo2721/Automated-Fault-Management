# 🛡️ Automated Fault Management System

A self-healing monitoring system designed for large-scale communication networks. This project automates the process of detecting, alerting, and remedying system faults without human intervention.

## 🚀 Key Features
- **Real-time Monitoring:** Collects metrics using **Prometheus** & **Node Exporter**.
- **Visualization:** Interactive dashboards via **Grafana**.
- **Smart Alerting:** Routing alerts to **Discord Webhook** (Firing/Resolved states) via **Alertmanager**.
- **Auto-Remediation:** Custom **Python (Flask + Paramiko)** service to automatically restart failed services (Docker containers) via SSH.

## 🛠️ Tech Stack
- **Core:** Docker, Docker Compose
- **Monitoring:** Prometheus, Blackbox Exporter, cAdvisor
- **Visualization:** Grafana
- **Automation:** Python 3, Flask, Paramiko (SSH)

## 🎥 Demo & Usage
Check out the full implementation and testing scenarios in the playlist below:
**[📺 Watch Demo Playlist on YouTube](https://www.youtube.com/playlist?list=PLiXwXacd4F0gUcHUCHFB3eoSB9P1efQny)**

## 🧪 Scenarios Tested
1.  **Web Service Failure:** Auto-detect `nginx` down ➜ Trigger Alert ➜ Auto-restart container ➜ Resolve Alert.
2.  **High CPU Usage:** Detect load > 85% ➜ Trigger Warning Alert to Discord.
