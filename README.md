# Breach Alert Bot SEC + Heartbeat Counts + Log Rotation)

This bot monitors:
- SEC 8-K current feed (alerts on Item 1.05 or security keywords)
It sends alerts by email when potential breach items are detected, plus a daily heartbeat email with scan counts.

---
## 1. Install

```bash
# Unzip into your home directory
mkdir -p $HOME/breach_alert_bot
unzip breach_alert_bot.zip -d $HOME/breach_alert_bot
cd $HOME/breach_alert_bot

# Install dependencies
pip3 install -r requirements.txt
```

Edit `breach_bot.py` and update:
```python
EMAIL_SENDER = "your_email@gmail.com"
EMAIL_PASSWORD = "your_app_password"
EMAIL_RECEIVER = "your_email@gmail.com"
```

---
## 2. Run Manually

```bash
python3 breach_bot.py
```

Logs are written to:
```
$HOME/breach_alert_bot/breachbot.log
```

---
## 3. Run as a Systemd Service

Copy the included service file into systemd:
```bash
sudo cp $HOME/breach_alert_bot/breachbot.service /etc/systemd/system/breachbot.service
sudo chown root:root /etc/systemd/system/breachbot.service
sudo chmod 644 /etc/systemd/system/breachbot.service
```

Enable and start the service:
```bash
sudo systemctl daemon-reload
sudo systemctl enable breachbot.service
sudo systemctl start breachbot.service
```

Check logs:
```bash
sudo journalctl -u breachbot -f
```

---
## 4. File Ownership & Permissions

Set correct ownership for the bot folder:
```bash
sudo chown -R $USER:$USER $HOME/breach_alert_bot
sudo chmod -R 755 $HOME/breach_alert_bot
chmod 644 $HOME/breach_alert_bot/breach_bot.py
chmod 644 $HOME/breach_alert_bot/requirements.txt
chmod 644 $HOME/breach_alert_bot/README.txt
chmod 644 $HOME/breach_alert_bot/seen_ids.txt
chmod 644 $HOME/breach_alert_bot/breachbot.log
```

---
## 5. Log Rotation

A `breachbot.logrotate` config file is included. Copy it to `/etc/logrotate.d/`:

```bash
sudo cp $HOME/breach_alert_bot/breachbot.logrotate /etc/logrotate.d/breachbot
sudo chown root:root /etc/logrotate.d/breachbot
sudo chmod 644 /etc/logrotate.d/breachbot
```

Reload logrotate so the new rule takes effect immediately:
```bash
sudo systemctl reload logrotate
```

By default:
- Rotates `breachbot.log` daily
- Keeps 7 days of logs
- Compresses old logs

---
## 6. Test

- First run should send you a heartbeat email.
- Watch `$HOME/breach_alert_bot/breachbot.log` for activity.
- Next day, check `/var/log` for rotated log archives.
