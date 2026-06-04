# SNMP Installation and Configuration on Ubuntu

This document explains how to install and configure the Simple Network Management Protocol (SNMP) service on Ubuntu.

---

## Prerequisites

- Ubuntu Server/Desktop
- Sudo privileges

---

## Step 1: Install SNMP Packages

Update the package repository and install SNMP packages:

```bash
sudo apt update
sudo apt install -y snmpd snmp
```

---

## Step 2: Backup the Default Configuration

Before making changes, create a backup of the default SNMP configuration file.

```bash
sudo mv /etc/snmp/snmpd.conf /etc/snmp/snmpd.conf.bak
```

---
## Step 3: Configure SNMP

Create a new SNMP configuration file.

```bash
sudo nano /etc/snmp/snmpd.conf 

# SNMP Daemon Configuration


# Listen on UDP port 161
agentAddress udp:161


# Read-only community string
rocommunity public


# Optional System Information (Uncomment and Modify as Required)

# sysLocation    Data Center Rack A1
# sysContact     admin@example.com
# sysName        ubuntu-server
# sysServices    72


# Access Control Examples


# Allow only a specific subnet
# rocommunity public 192.168.1.0/24

# Allow only a specific host
# rocommunity public 192.168.1.100

# Read-write community (NOT recommended)
# rwcommunity private


# SNMPv3 (Recommended for Production)


# createUser snmpadmin SHA StrongAuthPassword AES StrongPrivacyPassword
# rouser snmpadmin authPriv


# Agent Behavior


# Log SNMP authentication failures
# authtrapenable 1

# Enable traps
# trapsink 192.168.1.50 public


# Custom Monitoring Scripts


# Extend command output through SNMP
# extend disk_usage /usr/bin/df -h
# extend memory_usage /usr/bin/free -m


# Custom OID Examples


# pass .1.3.6.1.4.1.8072.9999 /usr/local/bin/custom-snmp-script.sh


# End of Configuration


```

### Example Configuration

```conf

# SNMP Daemon Configuration


agentAddress udp:161
rocommunity public

# System Information
sysLocation    Server Room - Building A
sysContact     kashinathdms@gmail.com
sysName        ceph-node-01

# Restrict access to monitoring subnet
# rocommunity public 10.10.10.0/24
```

### Commonly Used Variables

| Variable | Purpose |
|-----------|---------|
| `agentAddress` | SNMP listening address and port |
| `rocommunity` | Read-only community string |
| `rwcommunity` | Read-write community string |
| `sysLocation` | Physical server location |
| `sysContact` | Administrator contact details |
| `sysName` | Hostname shown through SNMP |
| `sysServices` | Type of services provided by host |
| `trapsink` | Destination SNMP trap server |
| `authtrapenable` | Enable authentication failure traps |
| `extend` | Expose command output via SNMP |
| `createUser` | Create SNMPv3 user |
| `rouser` | Read-only SNMPv3 user |
| `pass` | Custom script/OID integration |

### Recommended for Production

```conf
agentAddress udp:161

rocommunity MonitoringCommunity 10.10.10.0/24

sysLocation  Primary Datacenter
sysContact   kashinathdms@gmail.com
sysName      ceph-mon-01

authtrapenable 1
```

> **Best Practice:** Use SNMPv3 whenever possible. SNMPv2 community strings are transmitted in plain text and should be restricted to trusted management networks.

## Step 4: Restart the SNMP Service

Apply the new configuration by restarting the SNMP daemon.

```bash
sudo systemctl restart snmpd
```

---

## Step 5: Verify Service Status

Check whether the SNMP service is running.

```bash
sudo systemctl status snmpd
```

Expected output:

```text
● snmpd.service - Simple Network Management Protocol (SNMP) Daemon
     Active: active (running)
```

---

## Step 6: Test SNMP Locally

Verify that SNMP is responding correctly.

```bash
snmpwalk -v2c -c public localhost system
```

Example output:

```text
SNMPv2-MIB::sysDescr.0 = STRING: Linux ubuntu
SNMPv2-MIB::sysName.0 = STRING: ubuntu
```

---

## Production Example

Instead of using the default `public` community string:

```bash
sudo tee /etc/snmp/snmpd.conf > /dev/null <<EOF
agentAddress udp:161
rocommunity MySecureCommunity 192.168.1.0/24
EOF
```

This configuration:

- Uses a custom community string (`MySecureCommunity`)
- Restricts SNMP access to the `192.168.1.0/24` subnet

Restart the service after making changes:

```bash
sudo systemctl restart snmpd
```

---

## Useful Commands

### Check Listening Port

```bash
sudo ss -tulpn | grep 161
```

### View SNMP Logs

```bash
sudo journalctl -u snmpd -f
```

### Test from a Remote Host

```bash
snmpwalk -v2c -c public <SERVER_IP> system
```

Example:

```bash
snmpwalk -v2c -c public 192.168.1.100 system
```

---

## Troubleshooting

### SNMP Service Not Running

```bash
sudo systemctl restart snmpd
sudo systemctl status snmpd
```

### Firewall Blocking SNMP

Allow UDP port 161:

```bash
sudo ufw allow 161/udp
sudo ufw reload
```

### Configuration Errors

Validate configuration:

```bash
sudo snmpd -f -Lo
```

---

## Summary

| Task | Command |
|--------|---------|
| Install SNMP | `sudo apt install -y snmpd snmp` |
| Backup Config | `sudo mv /etc/snmp/snmpd.conf /etc/snmp/snmpd.conf.bak` |
| Restart Service | `sudo systemctl restart snmpd` |
| Check Status | `sudo systemctl status snmpd` |
| Local Test | `snmpwalk -v2c -c public localhost system` |

---

**Author:** Kashinath S  
