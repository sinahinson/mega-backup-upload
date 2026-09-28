# ☁️ MEGA Backup Upload

[![Bash](https://img.shields.io/badge/Bash-Shell-121011?logo=gnu-bash&logoColor=white)](https://www.gnu.org/software/bash/)
[![MEGA](https://img.shields.io/badge/Storage-MEGA-red)](https://mega.io/)
[![Discord](https://img.shields.io/badge/Notifications-Discord-5865F2?logo=discord&logoColor=white)](https://discord.com/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

A small and practical **Bash backup script** for Linux VPS servers.

It creates a date-stamped ZIP archive of `/var/www/html`, uploads the archive to **MEGA** through **rclone**, sends a status report to **Discord**, and removes the temporary ZIP file after the upload attempt.

> Built for simple, scheduled website backups without a database, framework, or extra application layer.

---

## ✨ Features

- 📦 ZIP backup of `/var/www/html`
- 📅 Date-based backup filenames
- ☁️ MEGA upload via [rclone](https://rclone.org/)
- 🔔 Discord webhook notifications
- 🧹 Automatic temporary-file cleanup
- ⏰ Easy `cron` scheduling
- 🐚 Pure Bash — simple and easy to customize

---

## 🔄 Workflow

```text
/var/www/html
       │
       ▼
 ZIP archive
       │
       ▼
    rclone
       │
       ▼
     MEGA
       │
       └──────► Discord status report
       
Temporary ZIP ──► deleted
```

Example for **2026-09-28**:

```text
/tmp/html_backup_2026-09-28.zip
```

The current script uploads it to:

```text
mega:/VeePass/html_backup_2026-09-28.zip
```

---

## 📋 Requirements

Recommended for Debian/Ubuntu VPS servers:

- Bash
- `zip`
- `curl`
- `rclone`
- A configured MEGA remote in rclone
- A Discord webhook URL

The current script backs up:

```text
/var/www/html
```

---

## 🚀 Installation

### 1. Install dependencies

```bash
sudo apt update
sudo apt install zip curl -y
```

Install rclone:

```bash
curl https://rclone.org/install.sh | sudo bash
```

Verify:

```bash
rclone version
zip -v
curl --version
```

### 2. Configure MEGA

Start the rclone configuration wizard:

```bash
rclone config
```

Create a remote for your MEGA account.

The current script expects the remote to be named:

```text
mega
```

Test it:

```bash
rclone lsd mega:
```

If you use another remote name, change the `DEST` variable in `main.sh`.

---

## ⚙️ Configuration

Edit the script:

```bash
nano main.sh
```

Main settings:

```bash
DATE=$(date +%F)

SRC="/var/www/html"

ZIP_FILE="/tmp/html_backup_$DATE.zip"

DEST="mega:/VeePass/html_backup_$DATE.zip"

WEBHOOK_URL=""
```

### Change the source directory

For example:

```bash
SRC="/var/www/example.com"
```

### Change the MEGA destination

For example:

```bash
DEST="mega:/backups/html_backup_$DATE.zip"
```

### Configure Discord

Set your webhook:

```bash
WEBHOOK_URL="https://discord.com/api/webhooks/..."
```

Do **not** commit a real webhook URL to GitHub.

---

## ▶️ Usage

Make the script executable:

```bash
chmod +x main.sh
```

Run it manually:

```bash
./main.sh
```

The script then:

1. Creates the ZIP archive.
2. Attempts to upload it to MEGA.
3. Sends a Discord report containing the ZIP and upload status.
4. Removes the temporary ZIP file.

---

## ⏰ Automatic Backups with Cron

Edit your crontab:

```bash
crontab -e
```

Run the backup every day at **03:00**:

```cron
0 3 * * * /path/to/mega-backup-upload/main.sh >> /var/log/mega-backup.log 2>&1
```

Example:

```cron
0 3 * * * /opt/mega-backup-upload/main.sh >> /var/log/mega-backup.log 2>&1
```

Check the active cron jobs:

```bash
crontab -l
```

---

## 📂 Backup Format

Files are named using the server date:

```text
html_backup_YYYY-MM-DD.zip
```

Example:

```text
html_backup_2026-09-28.zip
html_backup_2026-09-29.zip
html_backup_2026-09-30.zip
```

---

## 🔐 Security

The script can contain access credentials or URLs for external services, so protect the configuration.

- Never publish your Discord webhook URL.
- Never commit sensitive rclone configuration files.
- Use appropriate file permissions on the script and configuration.
- Prefer a dedicated backup account or remote when practical.
- Make sure the user running the script can read the source directory.

A leaked Discord webhook should be treated as compromised and replaced.

---

## 🛠️ Troubleshooting

### Test the MEGA remote

```bash
rclone lsd mega:
```

### Check the source directory

```bash
ls -lah /var/www/html
```

### Test ZIP creation

```bash
zip -r /tmp/test-backup.zip /var/www/html
```

### Test the Discord webhook

```bash
curl -H "Content-Type: application/json" \
     -X POST \
     -d '{"content":"MEGA backup test"}' \
     "YOUR_WEBHOOK_URL"
```

### Check files uploaded to MEGA

```bash
rclone ls mega:/VeePass
```

---

## ⚠️ Current Behavior & Limitations

This repository intentionally keeps the backup process simple.

The current `main.sh` reports the ZIP and upload steps separately. If ZIP creation fails, the script still proceeds to the upload command and then sends the resulting status messages.

For production environments, you may want to add:

- `set -euo pipefail`
- stop-on-failure logic
- backup retention/rotation
- checksum or integrity verification
- restore testing
- configurable environment variables
- more detailed logging
- failure-specific notifications

These improvements are outside the scope of the current minimal script.

---

## 📁 Project Structure

```text
mega-backup-upload/
├── main.sh
├── README.md
└── LICENSE
```

| File | Purpose |
|------|---------|
| `main.sh` | Creates, uploads and cleans up the backup |
| `README.md` | Project documentation |
| `LICENSE` | MIT License |

---

## 📄 License

This project is licensed under the **MIT License**.

See [LICENSE](LICENSE) for the full license text.

---

## 👤 Author

**Sina Hosseini**

GitHub: [@sinahinson](https://github.com/sinahinson)

---

<p align="center">
  <sub>Simple backups · Remote storage · Discord alerts</sub>
</p>
