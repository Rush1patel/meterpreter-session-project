# 🛡️ Blue Team Findings — Meterpreter Session Exploitation Project

This file captures **observations and defensive notes** from the conceptual Meterpreter exploitation project.
All findings are meant for **educational and defensive purposes**.

---

## 📌 Purpose

*   Record **Indicators of Compromise (IoCs)** observed during the exercise.
*   Highlight **logs, processes, or files** defenders should monitor.
*   Suggest **alerting or monitoring rules** to detect similar activity in real environments.

---

## 🕵️ Observed Artifacts

| Category           | Description / Example                                     | Defensive Notes / Actions                                                               |
| :----------------- | :-------------------------------------------------------- | :-------------------------------------------------------------------------------------- |
| Suspicious Files   | `shell64.elf` executed in `/home/<user>/`                 | Alert on new executables created unexpectedly. Verify permissions.                      |
| Temporary Files    | Executables in `/tmp/` or `/var/tmp/`                     | Track newly created files with execute permissions (`/tmp/*.elf`).                      |
| Network Traffic    | Outbound TCP connection to `127.0.0.1:4444`               | Monitor unexpected outbound connections, especially from unprivileged users.            |
| Processes          | Unknown processes with unusual parent (`./shell64.elf`)   | Use `ps -ef --forest` to detect abnormal process hierarchies.                           |
| Users / Accounts   | New users or privilege escalations                        | Check `/etc/passwd`, `/etc/group`, and sudoers logs for unexpected changes.             |
| Logs               | Auth logs showing unusual logins or command execution     | `journalctl -xe`, `/var/log/auth.log`, `/var/log/syslog` — monitor for anomalies.       |

---

## 🔍 Suggested Monitoring & Alerts

### 1. File Creation / Modification

```bash
inotifywait -m /home /tmp /var/tmp -e create -e attrib
```

### 2. Unusual Processes / Parent-Child Relationships

```bash
ps -ef --forest | grep -E 'shell64|unknown_process'
```

### 3. Network Connections

```bash
lsof -i -nP | grep ESTABLISHED
netstat -tulpn
```

### 4. Log Monitoring

```bash
tail -f /var/log/auth.log /var/log/syslog
journalctl -f
```

---

## 📝 Defensive Observations

*   Always validate user-executed binaries and incoming connections.
*   Segregate sensitive environments; unprivileged users shouldn’t execute unknown files.
*   Monitor ports and services for unusual activity.
*   Regularly check system and authentication logs for anomalies.
*   Document incident responses and cleanup steps for audit and training purposes.

---

⚠️ **Disclaimer**
This file is for educational and defensive purposes only.
Do not attempt offensive actions on systems without explicit permission.

All recommendations are intended for learning, detection, and monitoring exercises.
```
