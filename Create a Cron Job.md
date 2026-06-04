# Cron Jobs on Ubuntu

Cron is a time-based job scheduler used to automate repetitive tasks such as backups, log cleanup, health checks, and monitoring scripts.

## Edit Crontab

Open the current user's crontab file:

```bash
sudo crontab -e                    #for current user
sudo crontab -u username -e   #for specific user
```  

## Cron Syntax

```text
* * * * * command
│ │ │ │ │
│ │ │ │ └── Day of Week (0-7)
│ │ │ └──── Month (1-12)
│ │ └────── Day of Month (1-31)
│ └──────── Hour (0-23)
└────────── Minute (0-59)
```

## Common Examples

### Run Every 5 Minutes

```cron
*/5 * * * * /opt/scripts/health-check.sh
```

### Run Daily at 2 AM

```cron
0 2 * * * /opt/scripts/backup.sh
```

### Run Every Sunday at Midnight

```cron
0 0 * * 0 /opt/scripts/cleanup.sh
```

### Reboot Task

Run after system startup:

```cron
@reboot /opt/scripts/startup.sh
```

## Redirect Output to Log File

```cron
0 2 * * * /opt/scripts/backup.sh >> /var/log/backup.log 2>&1
```

## List Existing Cron Jobs

```bash
crontab -l
```

## Remove All Cron Jobs

```bash
crontab -r
```

## Verify Cron Service

```bash
sudo systemctl status cron
```

## View Cron Logs

```bash
sudo journalctl -u cron
```

## Troubleshooting

Check whether cron service is running:

```bash
sudo systemctl restart cron
sudo systemctl status cron
```

Verify script permissions:

```bash
chmod +x /opt/scripts/script.sh
```

