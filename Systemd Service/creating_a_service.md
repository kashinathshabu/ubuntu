# Creating a Systemd Service on Ubuntu

This guide explains how to create, enable, start, stop, and troubleshoot a custom systemd service on Ubuntu.

---

## What is Systemd?

Systemd is the default service manager used by most modern Linux distributions. It manages system services, startup processes, logging, and dependencies.

Benefits:

* Automatic service startup during boot
* Process monitoring and recovery
* Dependency management
* Centralized logging with journalctl

---

## Step 1: Create a Script

Create a simple script that will be managed by systemd.

```bash
sudo mkdir -p /opt/scripts

sudo tee /opt/scripts/hello.sh > /dev/null <<EOF
#!/bin/bash

while true
do
    echo "$(date) - Service is running" >> /var/log/hello-service.log
    sleep 60
done
EOF
```

Make the script executable:

```bash
sudo chmod +x /opt/scripts/hello.sh
```

---

## Step 2: Create a Service File

Create a new service unit file.

```bash
sudo nano /etc/systemd/system/hello.service
```

Add the following configuration:

```ini
[Unit]
Description=Hello Demo Service
After=network.target

[Service]
Type=simple
User=root
ExecStart=/opt/scripts/hello.sh
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
```

---

## Service File Explanation

### [Unit]

Defines metadata and dependencies.

| Directive   | Description                  |
| ----------- | ---------------------------- |
| Description | Service description          |
| After       | Start after specified target |

Example:

```ini
[Unit]
Description=My Application
After=network.target
```

---

### [Service]

Defines how the service runs.

| Directive  | Description              |
| ---------- | ------------------------ |
| Type       | Service startup type     |
| User       | User running the service |
| ExecStart  | Command to execute       |
| Restart    | Restart policy           |
| RestartSec | Delay before restart     |

Example:

```ini
[Service]
Type=simple
User=root
ExecStart=/opt/scripts/app.sh
Restart=always
```

---

### [Install]

Defines when the service should start.

```ini
[Install]
WantedBy=multi-user.target
```

This makes the service start during normal system boot.

---

## Step 3: Reload Systemd

After creating or modifying service files:

```bash
sudo systemctl daemon-reload
```

---

## Step 4: Start the Service

```bash
sudo systemctl start hello.service
```

Verify status:

```bash
sudo systemctl status hello.service
```

Expected output:

```text
Active: active (running)
```

---

## Step 5: Enable Service at Boot

```bash
sudo systemctl enable hello.service
```

Verify:

```bash
sudo systemctl is-enabled hello.service
```

Expected output:

```text
enabled
```

---

## Common Service Operations

### Start Service

```bash
sudo systemctl start hello.service
```

### Stop Service

```bash
sudo systemctl stop hello.service
```

### Restart Service

```bash
sudo systemctl restart hello.service
```

### Reload Service

```bash
sudo systemctl reload hello.service
```

### Check Status

```bash
sudo systemctl status hello.service
```

---

## View Service Logs

View all logs:

```bash
sudo journalctl -u hello.service
```

Follow logs in real time:

```bash
sudo journalctl -u hello.service -f
```

View recent logs:

```bash
sudo journalctl -u hello.service -n 50
```

---

## Useful Service Types

### Simple

Default service type.

```ini
Type=simple
```

### Forking

Used by traditional daemons.

```ini
Type=forking
```

### Oneshot

Runs once and exits.

```ini
Type=oneshot
```

Example:

```ini
[Service]
Type=oneshot
ExecStart=/opt/scripts/backup.sh
```

---

## Restart Policies

Always restart:

```ini
Restart=always
```

Restart only on failure:

```ini
Restart=on-failure
```

Never restart:

```ini
Restart=no
```

---

## Environment Variables

```ini
[Service]
Environment="APP_ENV=production"
Environment="LOG_LEVEL=info"
```

Or use an environment file:

```ini
EnvironmentFile=/etc/default/myapp
```

---

## Running Services as Non-Root Users

```ini
[Service]
User=ubuntu
Group=ubuntu
ExecStart=/opt/scripts/app.sh
```

Recommended for production workloads.

---

## Troubleshooting

### Service Fails to Start

```bash
sudo systemctl status hello.service
```

Check logs:

```bash
sudo journalctl -xeu hello.service
```

---

### Verify Service Syntax

```bash
sudo systemd-analyze verify /etc/systemd/system/hello.service
```

---

### Check Service Configuration

```bash
systemctl cat hello.service
```

---

### Check Dependencies

```bash
systemctl list-dependencies hello.service
```

---

## Production Example

```ini
[Unit]
Description=Node Exporter
After=network.target

[Service]
Type=simple
User=node_exporter
ExecStart=/usr/local/bin/node_exporter
Restart=on-failure
RestartSec=10

[Install]
WantedBy=multi-user.target
```

---

## Cheat Sheet

| Task            | Command                     |
| --------------- | --------------------------- |
| Reload systemd  | `systemctl daemon-reload`   |
| Start service   | `systemctl start service`   |
| Stop service    | `systemctl stop service`    |
| Restart service | `systemctl restart service` |
| Enable at boot  | `systemctl enable service`  |
| Disable at boot | `systemctl disable service` |
| Check status    | `systemctl status service`  |
| View logs       | `journalctl -u service`     |

---

