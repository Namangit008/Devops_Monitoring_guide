
# Datadog Monitoring Setup and Alerting Guide

This guide walks through installing the Datadog Agent, creating dashboards, and setting up custom alerts on AWS EC2 Ubuntu instances.

---

## 1. Introduction to Datadog

Datadog is a cloud-based monitoring and analytics platform for infrastructure and applications. It provides:

- Centralized monitoring
- Alerting and automation
- Cloud provider integrations
- Custom dashboards
- Scalable observability

---

## 2. Install Datadog Agent on Ubuntu EC2

### Step 1: Sign Up

- Go to [Datadog](https://www.datadoghq.com/) and create an account.
- Note your API key after logging in.

### Step 2: Run Install Script

```bash
DD_AGENT_MAJOR_VERSION=7 DD_API_KEY=<YOUR_API_KEY> DD_SITE="datadoghq.com" bash -c "$(curl -L https://s3.amazonaws.com/dd-agent/scripts/install_script.sh)"
```

### Step 3: Verify Agent

```bash
sudo datadog-agent status
```

You should see metrics and integrations listed.

---

## 3. Create a Custom Dashboard

### Step 1: Go to Dashboards
- Click **New Dashboard**
- Name it (e.g., `System Monitoring`)

### Step 2: Add Widgets
- Timeseries Graph: `system.cpu.user`
- Add memory, disk, and network widgets

### Step 3: Customize Layout
- Drag-and-drop widgets
- Use template variables for multiple hosts

---

## 4. Create Alerts for High CPU

### Step 1: Navigate to Monitors > New Monitor
- Type: Metric
- Metric: `system.cpu.user`
- Aggregation: Average over 5 minutes

### Step 2: Alert Conditions
- Trigger above 80%
- Optional: Warning threshold

### Step 3: Notification Message

```
## What's happening
High CPU usage detected on host `$host.name`
This means the host is processing at near full capacity. Check for high-load processes or traffic spikes.
```

- Add your email: `@your_email@example.com`

### Step 4: Final Settings
- Set priority
- Choose Multi Alert for multi-host setup

### Step 5: Test It
- Click Test Notification > Run Test

---

## 5. Verifying Alerts

- Simulate high CPU or wait for threshold
- Check triggered alerts in Datadog
- Verify email notification

---

## 6. Best Practices

- Use tags (e.g., service:db, env:prod)
- Separate warning and critical thresholds
- Always test alerts before production
- Combine metrics, logs, and traces

---

## ✅ Done!

You now have full visibility into your EC2 instances using Datadog. Explore more integrations for deeper observability and automated incident response.
