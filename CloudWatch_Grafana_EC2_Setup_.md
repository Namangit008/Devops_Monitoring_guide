
# 🔍 DevOps Monitoring Setup: AWS EC2 + CloudWatch + Grafana

This guide walks you through setting up application and EC2 metrics logging to **Amazon CloudWatch**, and visualizing both metrics and logs using **Grafana**, all running on an **EC2 Ubuntu 22.04 instance**.

---

## ✅ Overview

| Component        | Description |
|------------------|-------------|
| **EC2 Instance** | Ubuntu 22.04 (t2.micro or above) |
| **CloudWatch**   | Collect system/app logs and EC2 metrics |
| **Grafana**      | Visualize logs and metrics from CloudWatch |
| **IAM Role**     | Grant EC2 permission to access CloudWatch |

---

## 🚀 1. Launch EC2 Instance

- **AMI**: Ubuntu Server 22.04 LTS
- **Type**: t2.micro (or larger)
- **Storage**: default 8GB
- **Security Group**:
  - `SSH (TCP 22)` – from your IP
  - `Grafana UI (TCP 3000)` – from your IP
- **IAM Role**: Attach a role with:
  - `CloudWatchAgentServerPolicy`
  - `CloudWatchReadOnlyAccess`
- Enable **Detailed Monitoring** (optional but recommended)

---

## 🔌 2. Connect to EC2

```bash
ssh -i key.pem ubuntu@<ec2-public-ip>
sudo apt update && sudo apt upgrade -y
```

---

## 📦 3. Install CloudWatch Agent

```bash
wget https://s3.amazonaws.com/amazoncloudwatch-agent/ubuntu/amd64/latest/amazon-cloudwatch-agent.deb
sudo dpkg -i -E ./amazon-cloudwatch-agent.deb
```

---

## ⚙️ 4. Configure CloudWatch Agent

Create config file:
```bash
sudo nano /opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.json
```

Sample config:
```json
{
  "logs": {
    "logs_collected": {
      "files": {
        "collect_list": [
          {
            "file_path": "/var/log/syslog",
            "log_group_name": "/myapp/syslog",
            "log_stream_name": "{instance_id}"
          }
        ]
      }
    }
  },
  "metrics": {
    "metrics_collected": {
      "mem": {
        "measurement": ["mem_used_percent"]
      },
      "disk": {
        "resources": ["/"],
        "measurement": ["used_percent"]
      }
    }
  }
}
```

Start agent:
```bash
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl   -a fetch-config -m ec2 -c file:/opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.json -s
```

---

## 📊 5. Install Grafana on EC2

```bash
sudo apt install -y software-properties-common
sudo add-apt-repository "deb https://packages.grafana.com/oss/deb stable main"
wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
sudo apt update
sudo apt install grafana -y
sudo systemctl enable grafana-server
sudo systemctl start grafana-server
```

Access Grafana:
```
http://<ec2-public-ip>:3000
Username: admin
Password: admin
```

---

## 🔌 6. Add CloudWatch as a Data Source

1. Go to **Connections > Data Sources > Add data source**
2. Select **CloudWatch**
3. Set:
   - **Authentication Provider**: AWS SDK Default
   - **Region**: `ap-south-1`
4. Click **Save & Test**

✅ Ensure EC2 IAM Role has `cloudwatch:ListMetrics` and log permissions.

---

## 📈 7. Create Dashboards

### A. **Logs Panel**

- **Query Mode**: Logs
- **Log Group**: `/myapp/syslog`
- **Query**:
```sql
fields @timestamp, @message
| sort @timestamp desc
| limit 20
```

---

### B. **EC2 Metrics Panel**

- **Namespace**: `AWS/EC2`
- **Metric Name**: `CPUUtilization`
- **Statistic**: Average
- **Dimension**: `InstanceId = i-xxxxxxxxxxxx`
- **Period**: `300`

---

## 🛠 8. Troubleshooting

| Error | Solution |
|-------|----------|
| `AccessDenied: cloudwatch:ListMetrics` | Add `CloudWatchReadOnlyAccess` to IAM Role |
| No CPU data | Enable Detailed Monitoring in EC2 |
| No logs | Check file path and log group name in agent config |
| Grafana not loading | Ensure port 3000 is open and service is running |

---

## 🎯 Final Setup

✅ CloudWatch Agent pushing metrics & logs  
✅ Grafana reading CloudWatch data  
✅ Live dashboard for EC2 metrics + syslogs  
✅ IAM-based secure access (no static credentials)

---

## 📌 Author

*This was configured during hands-on DevOps training by Naman Jain.*  
Repo: [DevOps_Monitoring_Guide](https://github.com/Namangit008/Devops_Monitoring_guide)
