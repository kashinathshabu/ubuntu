# Log Management with Journalctl

`journalctl` is used to view and manage logs collected by systemd.

## View All Logs

```bash
journalctl
```

## View Recent Logs

```bash
journalctl -n 50
```

Show last 100 lines:

```bash
journalctl -n 100
```

## Follow Logs in Real Time

```bash
journalctl -f
```

## View Logs for a Service

Example: SSH Service

```bash
journalctl -u ssh
```

Follow logs:

```bash
journalctl -u ssh -f
```

## View Logs Since Boot

```bash
journalctl -b
```

Previous boot:

```bash
journalctl -b -1
```

## Filter by Time

Last 1 hour:

```bash
journalctl --since "1 hour ago"
```

Today:

```bash
journalctl --since today
```

Specific time range:

```bash
journalctl --since "2025-01-01 10:00:00" --until "2025-01-01 12:00:00"
```

## Filter by Priority

Errors only:

```bash
journalctl -p err
```

Warnings and above:

```bash
journalctl -p warning
```

## Check Disk Usage

```bash
journalctl --disk-usage
```

## Clear Old Logs

Keep only 7 days:

```bash
sudo journalctl --vacuum-time=7d
```

Keep only 500 MB:

```bash
sudo journalctl --vacuum-size=500M
```

## Troubleshooting Examples

Check failed services:

```bash
systemctl --failed
```

View service errors:

```bash
journalctl -xeu nginx
```

Check kernel messages:

```bash
journalctl -k
```


