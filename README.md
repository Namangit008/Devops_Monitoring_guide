
# 🚀 DevOps Monitoring Setup Guide

Welcome to the **DevOps Monitoring Guide** repository!  
This repo includes **hands-on, step-by-step guides** to set up and manage monitoring systems using three powerful tools:

- 📡 [Nagios](#-nagios-monitoring)
- 📊 [Datadog](#-datadog-monitoring)
- 📈 [CloudWatch + Grafana](#-aws-cloudwatch--grafana-monitoring)

---

## 📦 Tools Covered

| Tool                   | Description |
|------------------------|-------------|
| **Nagios Core**        | Infrastructure and service monitoring with email alerting |
| **Datadog**            | Cloud-native metrics, dashboards & alerting |
| **CloudWatch + Grafana** | AWS-native logs/metrics with Grafana visualization |

---

## 📁 Repository Structure

```
Devops_Monitoring_guide/
├── nagios/                    ← Nagios setup configs & plugins
├── datadog/                   ← Datadog agent and dashboards
├── cloudwatch-grafana/        ← CloudWatch + Grafana config and dashboards
│   ├── amazon-cloudwatch-agent.json
│   ├── CloudWatch_Grafana_EC2_Setup_README.md
│   └── screenshots/
│       └── dashboard images
├── README.md                  ← You are here
```

---

## 📡 Nagios Monitoring

📄 [Nagios Setup Guide](nagios-setup-guide.md)  
Set up **Nagios Core** on Ubuntu EC2, configure **NRPE** for remote host monitoring, and enable **email alerts** for services.

Includes:
- NRPE checks for CPU, memory, disk
- Service notification configs
- Host/hostgroup monitoring
- Email alert setup with `mailx`

---

## 📊 Datadog Monitoring

📄 [Datadog Setup Guide](datadog-setup-guide.md)  
Deploy the **Datadog Agent** on EC2, build beautiful **real-time dashboards**, and configure **custom metric alerts**.

Includes:
- Agent installation with API key
- Dashboards for CPU, memory, network
- Monitor configurations with thresholds
- Events & service checks

---

## 📈 AWS CloudWatch + Grafana Monitoring

📄 [CloudWatch + Grafana Guide](./cloudwatch-grafana/CloudWatch_Grafana_EC2_Setup_README.md)  
Set up **CloudWatch Agent** to collect logs & metrics and visualize them in **Grafana** using the CloudWatch plugin.

Includes:
- EC2 metrics (CPU, memory, disk)
- Application logs (e.g., syslog, app.log)
- Grafana setup & CloudWatch data source
- Logs and metric dashboards
- IAM role configuration

---

## 🙋‍♂️ Author

**Naman Jain**  
🧑‍💻 DevOps Intern @ Nukkad Shops Technologies Pvt. Ltd.  
🔗 [LinkedIn Profile](https://www.linkedin.com/in/naman-jain-73b795266)  
📂 GitHub Repo: [Namangit008/Devops_Monitoring_guide](https://github.com/Namangit008/Devops_Monitoring_guide)

---

## 🌟 Feedback & Contributions

If you found this project useful:
- ⭐ Star the repo  
- 🛠️ Fork it and contribute improvements  
- 💬 Reach out via [LinkedIn](https://www.linkedin.com/in/naman-jain-73b795266)

---

## 📜 License

This project is open-source and free to use for learning and research purposes.

> Built with 💻 by a passionate DevOps intern learning real-world monitoring tools, one setup at a time.
