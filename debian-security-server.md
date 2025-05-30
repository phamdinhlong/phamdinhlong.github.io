# Debian: Security Server

Tags: debian, linux

## 1. SSH

```bash
# Create a new authorized_keys
touch ~/.ssh/authorized_keys && chmod 700 ~/.ssh

# Paste the public key into authorized_keys
nano ~/.ssh/authorized_keys

# Modify the file permissions
chmod 600 ~/.ssh/authorized_keys

```
