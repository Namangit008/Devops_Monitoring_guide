
# Nagios Monitoring Setup on AWS EC2 (Ubuntu 22.04)

This document provides a complete, step-by-step guide for setting up Nagios Core on an AWS EC2 Ubuntu 22.04 instance, adding a remote host (slave), and configuring email alerts.

---

## Table of Contents
1. Launch EC2 Instances
2. Nagios Master Installation
3. Install NRPE and Plugins on Remote Host
4. Configure Slave in Nagios
5. Enable and Test Email Alerts

---

## 1. Launch EC2 Instances

### 1.1 Create Two EC2 Ubuntu 22.04 Instances
- Instance 1: Nagios Master
- Instance 2: Remote Host (Slave)

### 1.2 Security Group Rules
Allow: HTTP (80), SSH (22), and Custom TCP (5666) from the master IP.

---

## 2. Install Nagios Core on Master

### 2.1 Prerequisites
```bash
sudo apt update
sudo apt install -y apache2 php php-gd unzip build-essential libgd-dev libapache2-mod-php
```

### 2.2 Add Nagios User and Group
```bash
sudo useradd nagios
sudo groupadd nagcmd
sudo usermod -a -G nagcmd nagios
sudo usermod -a -G nagcmd www-data
```

### 2.3 Download and Compile Nagios
```bash
cd /tmp
curl -L -O https://assets.nagios.com/downloads/nagioscore/releases/nagios-4.4.14.tar.gz
tar zxvf nagios-4.4.14.tar.gz
cd nagios-4.4.14
./configure --with-command-group=nagcmd
make all
sudo make install
sudo make install-commandmode
sudo make install-init
sudo make install-config
sudo make install-webconf
```

### 2.4 Create Web User
```bash
sudo htpasswd -c /usr/local/nagios/etc/htpasswd.users nagiosadmin
```

### 2.5 Enable and Start Services
```bash
sudo a2enmod cgi
sudo systemctl restart apache2
sudo systemctl enable apache2
sudo systemctl enable nagios
```

### 2.6 Install Nagios Plugins
```bash
cd /tmp
curl -L -O https://nagios-plugins.org/download/nagios-plugins-2.3.3.tar.gz
tar zxvf nagios-plugins-2.3.3.tar.gz
cd nagios-plugins-2.3.3
./configure --with-nagios-user=nagios --with-nagios-group=nagios
make
sudo make install
```

---

## 3. Install NRPE and Plugins on Remote Host

### 3.1 Install Prerequisites
```bash
sudo apt update
sudo apt install -y nagios-nrpe-server nagios-plugins
```

### 3.2 Configure NRPE
```bash
sudo nano /etc/nagios/nrpe.cfg
```
- Add master IP in allowed_hosts
- Confirm command definitions (e.g., check_disk, check_load)
```bash
sudo systemctl restart nagios-nrpe-server
sudo systemctl enable nagios-nrpe-server
```

---

## 4. Add Slave (Remote Host) in Nagios

### 4.1 Define Host and Services
```bash
sudo mkdir -p /usr/local/nagios/etc/servers
sudo nano /usr/local/nagios/etc/servers/slave1.cfg
```

Example config:
```cfg
define host {
    use             linux-server
    host_name       slave1
    alias           Ubuntu Slave
    address         <SLAVE_PRIVATE_IP>
}
define service {
    use                     generic-service
    host_name               slave1
    service_description     Disk Space
    check_command           check_nrpe!check_disk
}
```

### 4.2 Include Config in Main File
```bash
sudo nano /usr/local/nagios/etc/nagios.cfg
```
Add or uncomment:
```cfg
cfg_dir=/usr/local/nagios/etc/servers
```

### 4.3 Restart Nagios
```bash
sudo /usr/local/nagios/bin/nagios -v /usr/local/nagios/etc/nagios.cfg
sudo systemctl restart nagios
```

---

## 5. Enable and Test Email Alerts

### 5.1 Install Mail Agent
```bash
sudo apt install -y postfix mailutils
```

### 5.2 Configure Gmail SMTP
```bash
sudo nano /etc/postfix/main.cf
```

Add:
```cfg
relayhost = [smtp.gmail.com]:587
smtp_use_tls = yes
smtp_sasl_auth_enable = yes
smtp_sasl_security_options = noanonymous
smtp_sasl_password_maps = hash:/etc/postfix/sasl_passwd
smtp_tls_CAfile = /etc/ssl/certs/ca-certificates.crt
```

Create credentials:
```bash
echo "[smtp.gmail.com]:587 your_email@gmail.com:your_app_password" | sudo tee /etc/postfix/sasl_passwd
sudo postmap /etc/postfix/sasl_passwd
sudo chmod 600 /etc/postfix/sasl_passwd
sudo systemctl restart postfix
```

### 5.3 Configure Nagios Contact
```bash
sudo nano /usr/local/nagios/etc/objects/contacts.cfg
```

Example:
```cfg
define contact{
    contact_name            nagiosadmin
    use                     generic-contact
    alias                   Nagios Admin
    email                   your_email@gmail.com
}
```

### 5.4 Test Mail
```bash
echo "Test Mail" | mail -s "Test Nagios Email" your_email@gmail.com
```

### 5.5 Simulate Alert
Edit a service to trigger a fake alert:
```cfg
check_command           check_nrpe!check_disk_fake
```
Restart Nagios:
```bash
sudo systemctl restart nagios
```

---

## ✅ You're Done!
You now have Nagios monitoring and alerting set up across AWS EC2 instances!
