---
name: vps-security-hardening
description: Harden Linux VPS security with UFW firewall, SSH hardening, and fail2ban
license: MIT
compatibility: opencode
metadata:
  audience: devops
  os: linux
---

## What I Do

Comprehensive VPS security hardening for Linux servers, implementing industry-standard security measures:

- **UFW Firewall Configuration**: Set up firewall rules to block unnecessary ports and allow only essential services
- **SSH Hardening**: Configure SSH for secure key-based authentication while maintaining tool compatibility (e.g., Coolify)
- **Fail2ban Installation**: Protect against brute force attacks by banning IPs after failed login attempts

## When to Use Me

Use this skill when:
- Setting up a new VPS for the first time
- Hardening existing VPS security
- Deploying applications to production servers
- Following security best practices for Linux servers

## Prerequisites

- Linux-based VPS (Ubuntu/Debian recommended)
- Root or sudo access
- SSH access to the VPS

## Implementation Steps

### 1. Configure UFW Firewall

Check current UFW status:
```bash
sudo ufw status verbose
```

Allow essential ports (adjust as needed):
```bash
sudo ufw allow 22/tcp   # SSH
sudo ufw allow 80/tcp   # HTTP
sudo ufw allow 443/tcp  # HTTPS
```

Enable firewall (may disrupt SSH - ensure you have other access):
```bash
sudo ufw enable
```

Verify rules:
```bash
sudo ufw status numbered
```

### 2. SSH Hardening

Create backup:
```bash
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.backup
```

Configure SSH:
```bash
# Enable public key authentication
sudo sed -i 's/#PubkeyAuthentication yes/PubkeyAuthentication yes/' /etc/ssh/sshd_config

# Disable password authentication globally
sudo sed -i 's/PasswordAuthentication yes/PasswordAuthentication no/' /etc/ssh/sshd_config

# Allow password authentication only for specific users (e.g., for deployment tools)
# Append to the end of sshd_config:
cat <<EOF | sudo tee -a /etc/ssh/sshd_config

# Allow password authentication only for root (for Coolify compatibility)
Match User root
    PasswordAuthentication yes
EOF
```

Test configuration:
```bash
sudo sshd -t
```

Restart SSH:
```bash
sudo systemctl restart sshd
sudo systemctl status sshd
```

### 3. Install and Configure Fail2ban

Install fail2ban:
```bash
sudo apt update
sudo apt install -y fail2ban
```

Create custom configuration:
```bash
cat <<EOF | sudo tee /etc/fail2ban/jail.local
[DEFAULT]
# Ban hosts for 1 hour (3600 seconds)
bantime = 3600
# Find hosts that failed within the last 10 minutes
findtime = 600
# Allow 3 failed attempts before banning
maxretry = 3

[sshd]
# Monitor SSH service
enabled = true
port = ssh
filter = sshd
logpath = /var/log/auth.log
maxretry = 3
bantime = 3600
EOF
```

Enable and start fail2ban:
```bash
sudo systemctl enable fail2ban
sudo systemctl start fail2ban
sudo systemctl status fail2ban
```

Check fail2ban status:
```bash
sudo fail2ban-client status
sudo fail2ban-client status sshd
```

## Verification Steps

After implementation, verify:

1. **UFW Firewall**:
   ```bash
   sudo ufw status verbose
   ```
   - Should show "Status: active"
   - Only essential ports allowed (22, 80, 443)

2. **SSH Configuration**:
   ```bash
   cat /etc/ssh/sshd_config | grep -E "(PubkeyAuthentication|PasswordAuthentication|Match User)"
   ```
   - PubkeyAuthentication should be "yes"
   - PasswordAuthentication should be "no" (except for specific users)

3. **Fail2ban**:
   ```bash
   sudo fail2ban-client status sshd
   ```
   - Should show jail is active
   - May show banned IPs if attacks were detected

4. **Open Ports**:
   ```bash
   sudo ss -tuln
   ```
   - Verify only necessary services are listening publicly
   - Database ports (5432, 6379, etc.) should not be exposed

## Important Considerations

### SSH Key Setup

Before disabling password authentication, set up SSH keys:

1. Generate key pair on local machine:
   ```bash
   ssh-keygen -t ed25519
   ```

2. Copy public key to VPS:
   ```bash
   ssh-copy-id user@vps-ip
   ```

3. Test SSH key login:
   ```bash
   ssh user@vps-ip
   ```
   Should login without password prompt.

### Database Security

Database ports (PostgreSQL 5432, Redis 6379, etc.) should NOT be exposed publicly:
- Use Docker internal networks
- Configure reverse proxy (Nginx/Caddy) for external access
- Use Coolify's built-in reverse proxy if available

### Coolify Compatibility

If using Coolify or similar deployment tools:
- Keep password authentication enabled for root user
- Add `Match User root` block to allow password auth for specific users
- Test deployment tool access after SSH hardening

### Testing Before Lockout

Always test SSH key authentication before closing current session:
- Open new terminal and try logging in with SSH key
- Keep root password access as backup
- Have alternative access method (VPS provider console)

## Troubleshooting

### SSH Key Login Fails

1. Check SSH key permissions:
   ```bash
   ls -la ~/.ssh/
   # Should be 700 for .ssh directory, 600 for private keys
   chmod 700 ~/.ssh
   chmod 600 ~/.ssh/id_ed25519
   chmod 644 ~/.ssh/id_ed25519.pub
   ```

2. Verify authorized_keys:
   ```bash
   cat ~/.ssh/authorized_keys
   ```

3. Check SSH logs:
   ```bash
   sudo tail -f /var/log/auth.log
   ```

### Fail2ban Not Working

1. Check if jail is active:
   ```bash
   sudo fail2ban-client status sshd
   ```

2. Reload configuration:
   ```bash
   sudo fail2ban-client reload
   ```

3. Check logs:
   ```bash
   sudo tail -f /var/log/fail2ban.log
   ```

### UFW Issues

1. Reset to defaults:
   ```bash
   sudo ufw --force reset
   ```

2. Disable temporarily:
   ```bash
   sudo ufw disable
   ```

## Best Practices

- **Never expose database ports directly** to the internet
- **Use SSH keys instead of passwords** for all user accounts
- **Keep software updated** with security patches
- **Monitor logs regularly** for suspicious activity
- **Use strong passwords** for accounts that still use password authentication
- **Implement backups** of critical configurations
- **Test changes** in non-production environments first
- **Document custom configurations** for future reference

## Additional Security Measures

Consider implementing:

1. **Automatic security updates**:
   ```bash
   sudo apt install -y unattended-upgrades
   sudo dpkg-reconfigure -plow unattended-upgrades
   ```

2. **Disable IPv6 if not needed** (in /etc/sysctl.conf):
   ```bash
   net.ipv6.conf.all.disable_ipv6 = 1
   ```

3. **Install intrusion detection** (OSSEC, Wazuh, etc.)

4. **Configure log rotation** to prevent disk space issues

5. **Set up monitoring and alerts** for security events

## References

- [Ubuntu UFW Documentation](https://help.ubuntu.com/community/UFW)
- [OpenSSH Configuration](https://www.openssh.com/manual.html)
- [Fail2ban Manual](https://www.fail2ban.org/wiki/index.php/Main_Page)
- [CIS Ubuntu Benchmark](https://www.cisecurity.org/benchmark/ubuntu_linux)

## Exit Criteria

The VPS security hardening is complete when:

✅ UFW firewall is active and configured
✅ SSH uses key-based authentication for regular users
✅ Password authentication is disabled except for specific users
✅ Fail2ban is running and actively monitoring SSH
✅ Unnecessary ports are blocked by firewall
✅ SSH key authentication is tested and working
✅ Backup access method is confirmed (root password + VPS console)