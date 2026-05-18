SSH Audit and SSH Hardening Lab
Objective

The objective of this lab was to analyze and harden the local OpenSSH server on an Ubuntu 24.04 system using ssh-audit. The process included identifying weak algorithms, enabling SSH key authentication, disabling insecure authentication methods, and verifying the hardened configuration safely without interrupting remote administration capabilities.

Lab Environment
Component	Details
System	Ubuntu 24.04.4 LTS
Device	Lenovo Defense Lab Workstation
SSH Server	OpenSSH 9.6p1
VPN Platform	Tailscale
Audit Tool	ssh-audit
Access Method	Localhost SSH + Remote Administration Preparation
Initial SSH Service Verification

The SSH service was verified before starting the audit process.

systemctl status ssh

Result:

Active: active (running)

This confirmed that the OpenSSH server was operational.

Initial SSH Audit

The local SSH server was audited using:

ssh-audit localhost
Initial Findings

The audit identified several weak or unnecessary algorithms and settings:

SHA1-based MAC algorithms enabled
Weak UMAC-64 algorithms enabled
NIST ECDSA algorithms enabled
Password authentication enabled
Root login partially allowed
Compression enabled
Older Diffie-Hellman group available
Effective SSH Configuration Review

The active SSH configuration was reviewed using:

sudo sshd -T

Important findings:

PasswordAuthentication yes
PermitRootLogin without-password
Compression yes
SSH Key Authentication Verification

A local SSH login test was performed:

ssh localhost

The system requested a password, indicating that SSH key authentication was not yet configured correctly.

The .ssh directory was inspected:

ls -la ~/.ssh

Finding:

authorized_keys

The file existed but was empty.

Enabling SSH Key Authentication

The existing Ed25519 public key was added to authorized_keys.

cat ~/.ssh/id_ed25519.pub >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys

SSH login was tested again:

ssh localhost

Result:

Successful login without password
SSH key authentication confirmed
SSH Configuration Backup

Before hardening changes, the original SSH configuration was backed up.

sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.backup-before-hardening
SSH Hardening Configuration

A separate hardening configuration file was created:

sudo nano /etc/ssh/sshd_config.d/99-hardening.conf
Hardening Configuration
KexAlgorithms sntrup761x25519-sha512@openssh.com,curve25519-sha256,curve25519-sha256@libssh.org,diffie-hellman-group16-sha512,diffie-hellman-group18-sha512,diffie-hellman-group-exchange-sha256

HostKeyAlgorithms ssh-ed25519,rsa-sha2-512,rsa-sha2-256

MACs umac-128-etm@openssh.com,hmac-sha2-256-etm@openssh.com,hmac-sha2-512-etm@openssh.com

PasswordAuthentication no

PermitRootLogin no

Compression no
Configuration Validation

The SSH configuration syntax was validated before reloading the service.

sudo sshd -t

No output indicated valid syntax.

Safe SSH Reload

The SSH service configuration was safely reloaded:

sudo systemctl reload ssh

A reload was used instead of a restart to reduce the risk of interrupting active SSH sessions.

Post-Hardening SSH Audit

The hardened SSH server was audited again:

ssh-audit localhost
Post-Hardening Improvements

The following insecure or weak settings were removed successfully:

SHA1 MAC algorithms
UMAC-64 algorithms
ECDSA NIST host keys
Password authentication
Root login
Compression
Weak Diffie-Hellman group14
Verification of Active Hardened Policy

The active hardened configuration was verified using:

sudo sshd -T | grep -E 'passwordauthentication|permitrootlogin|compression|macs|kexalgorithms|hostkeyalgorithms'

Result:

permitrootlogin no
passwordauthentication no
compression no
macs umac-128-etm@openssh.com,hmac-sha2-256-etm@openssh.com,hmac-sha2-512-etm@openssh.com
kexalgorithms sntrup761x25519-sha512@openssh.com,curve25519-sha256,curve25519-sha256@libssh.org,diffie-hellman-group16-sha512,diffie-hellman-group18-sha512,diffie-hellman-group-exchange-sha256
hostkeyalgorithms ssh-ed25519,rsa-sha2-512,rsa-sha2-256
Security Impact

This hardening process reduced the SSH attack surface by:

Eliminating password-based authentication
Disabling root SSH login
Removing deprecated SHA1-based MAC algorithms
Removing weaker UMAC-64 algorithms
Restricting SSH to modern cryptographic algorithms
Disabling SSH compression
Enforcing SSH key-based authentication

The system is now significantly more resistant to:

Brute-force attacks
Credential stuffing
Weak cryptographic negotiation
Legacy SSH downgrade risks
Lessons Learned
Always verify SSH key authentication before disabling passwords
Use sshd -t before reloading SSH configuration
Use /etc/ssh/sshd_config.d/ for modular and safer configuration management
Always create a backup before modifying SSH configuration
Use ssh-audit before and after hardening to verify security improvements
Reloading SSH is safer than restarting during remote administration scenarios
