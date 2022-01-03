# Cheat Sheet

### <mark style="color:red;">Systemd Service Commands</mark>

| Command                                  | Description                          |
| ---------------------------------------- | ------------------------------------ |
| systemctl stop service-name              | systemd stop running service         |
| systemctl start service-name             | systemctl start service              |
| systemctl restart service-name           | systemd restart running service      |
| systemctl reload service-name            | reloads all config files for service |
| systemctl status service-name            | systemctl show if service is running |
| systemctl enable service-name            | systemctl start service at boot      |
| systemctrl disable service-name          | systemctl - disable service at boot  |
| systemctl show service-name              | show systemctl service info          |
| systemctl -H target command service-name | run systemctl commands remotely      |

### <mark style="color:red;">Systemd Information Commands</mark>

\
Systemd commands that show useful system information.\


| Command                     | Description                                    |
| --------------------------- | ---------------------------------------------- |
| systemctl list-dependencies | show and units dependencies                    |
| systemctl list-sockets      | systemd list sockets and activities            |
| systemctl list-jobs         | view active systemd jobs                       |
| systemctl list-unit-files   | systemctl list unit files and their states     |
| systemctl list-units        | systemctl list default target (like run level) |

### <mark style="color:red;">Changing System State</mark>

\
systemd reboot, shutdown, default target etc

| Command             | Description                               |
| ------------------- | ----------------------------------------- |
| systemctl reboot    | systemctl reboot the system               |
| systemctl poweroff  | systemctl shutdown (power off the system) |
| systemctl emergency | Put in emergency mode                     |
| systemctl default   | systemctl default mode                    |

### &#x20;<mark style="color:red;">Systemctl Viewing Log Messages</mark>

| Command                    | Description                     |
| -------------------------- | ------------------------------- |
| journalctl                 | show all collected log messages |
| journalctl -u sshd.service | see sshd service messages       |
| journelctl -f              | follow messages as they appear  |
| journelctl -k              | show kernel messages only       |

\
\
