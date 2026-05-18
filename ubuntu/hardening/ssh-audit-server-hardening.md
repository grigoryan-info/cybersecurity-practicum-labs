# Ubuntu Server SSH Audit and Hardening

## Objective

This lab documents an SSH audit and hardening process performed on an Ubuntu Server accessed remotely from a Lenovo Ubuntu notebook through Tailscale VPN.

The goal was to reduce SSH attack surface, remove weak cryptographic algorithms, enforce key-based authentication, disable direct root login, and restrict SSH access to the Tailscale interface only.

## Environment

| Component | Description |
|---|---|
| Client | Lenovo notebook running Ubuntu |
| Server | Ubuntu Server 24.04 LTS |
| Remote Access | Tailscale VPN |
| SSH Port | 22/tcp |
| Firewall | UFW |
| Audit Tool | ssh-audit |

## Initial SSH Audit

The SSH service was audited locally on the server:

```bash
ssh-audit localhost

The audit showed that OpenSSH was running and several modern algorithms were available. However, weak or unnecessary algorithms were also present.

Findings included:

NIST-based ECDH key exchange algorithms
ECDSA host key algorithms
SHA1-based MAC algorithms
UMAC-64 algorithms
Diffie-Hellman group14
SSH listening on all interfaces
Backup Before Changes

Before modifying SSH configuration, the original configuration was backed up:

sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.bak-before-ssh-audit
Authentication Hardening

The SSH configuration was reviewed:

sudo sshd -T | grep -Ei 'permitrootlogin|passwordauthentication|pubkeyauthentication'

The following settings were applied in /etc/ssh/sshd_config:

PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes

The configuration syntax was validated:

sudo sshd -t

The SSH service was reloaded:

sudo systemctl reload ssh

Result:

Root login disabled
Password authentication disabled
Public key authentication enabled
Cryptographic Hardening

A separate hardening configuration file was created:

sudo nano /etc/ssh/sshd_config.d/hardening.conf

The following configuration was added:

KexAlgorithms sntrup761x25519-sha512@openssh.com,curve25519-sha256,curve25519-sha256@libssh.org,diffie-hellman-group16-sha512,diffie-hellman-group18-sha512,diffie-hellman-group-exchange-sha256

MACs hmac-sha2-512-etm@openssh.com,hmac-sha2-256-etm@openssh.com,umac-128-etm@openssh.com

HostKeyAlgorithms ssh-ed25519,rsa-sha2-512,rsa-sha2-256

Ciphers chacha20-poly1305@openssh.com,aes256-gcm@openssh.com,aes128-gcm@openssh.com,aes256-ctr

The SSH configuration was tested and reloaded:

sudo sshd -t
sudo systemctl reload ssh

A second audit was performed:

ssh-audit localhost

Result:

SHA1 MACs removed
Weak MACs removed
NIST curves removed
ECDSA removed
Diffie-Hellman group14 removed
Modern KEX, MAC, and cipher algorithms enforced
Network Exposure Review

The SSH listening interfaces were checked:

sudo ss -tulpen | grep ssh

SSH was listening on all IPv4 and IPv6 interfaces:

0.0.0.0:22
[::]:22

The server interfaces were reviewed:

ip addr

Relevant interfaces:

LAN:       192.168.0.92
Tailscale: 100.93.222.84
Firewall Hardening

UFW status was reviewed:

sudo ufw status verbose

The firewall was active with default deny for incoming traffic, but SSH was still allowed globally.

A Tailscale-specific SSH rule was added:

sudo ufw allow in on tailscale0 to any port 22 proto tcp

The numbered rules were reviewed:

sudo ufw status numbered

The global SSH rules were removed:

sudo ufw delete 4
sudo ufw delete 1

Final firewall state:

Default incoming policy: deny
SSH allowed only on tailscale0
Global SSH access removed
Remote Access Validation

A new SSH session was opened from the Lenovo notebook through Tailscale:

ssh berloga1@100.93.222.84

The login succeeded, confirming that remote access remained functional after hardening.

Final Security State
Authentication:
- Password authentication disabled
- Public key authentication enabled
- Root login disabled

Cryptography:
- Weak SHA1 MACs removed
- Weak KEX algorithms removed
- Modern ciphers enforced
- Ed25519 host key available

Network Exposure:
- SSH no longer globally allowed by UFW
- SSH restricted to tailscale0
- Default incoming firewall policy set to deny

Validation:
- SSH syntax test passed
- SSH service reloaded successfully
- Remote login through Tailscale confirmed
Security Value

This hardening reduces the risk of:

Brute-force attacks
Password-based attacks
Direct root compromise
Weak cryptographic negotiation
Unnecessary network exposure
Unauthorized SSH access from non-Tailscale interfaces
Conclusion

The Ubuntu Server SSH service was successfully audited and hardened. SSH administration is now restricted to the Tailscale VPN interface, password-based authentication is disabled, direct root login is blocked, and weak cryptographic algorithms were removed.

This configuration provides a stronger baseline for secure remote administration in a small business or lab environment.
