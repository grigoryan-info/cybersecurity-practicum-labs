# Ubuntu Defense Lab Foundation

## Goal

The goal of this lab was to prepare an Ubuntu Linux system as a basic cybersecurity defense lab workstation.

This setup provides the foundation for future labs in Linux administration, networking, packet analysis, remote access, Docker containers, and defensive security practice.

## System Context

General system setup:

- Lenovo laptop
- Intel Core Ultra 7 CPU
- 16 GB RAM
- 1 TB NVMe SSD
- Dual boot:
  - Ubuntu Linux
  - Windows 11

Ubuntu is used as the main working environment for cybersecurity learning and defensive lab practice.

## Preparation

Before installing the tools, the system package list was updated and installed packages were upgraded.

```bash
sudo apt update && sudo apt upgrade -y
Installed Tools

The following basic tools were installed:

sudo apt install -y \
curl \
wget \
git \
vim \
htop \
net-tools \
nmap \
wireshark \
tcpdump \
openssh-client \
openssh-server \
python3-pip \
ca-certificates \
gnupg \
lsb-release \
unzip \
zip
Tool Purpose
Tool	Purpose
curl	Download data from URLs and APIs
wget	Download files from the internet
git	Work with GitHub repositories
vim	Terminal-based text editor
htop	Interactive process viewer
net-tools	Classic network tools such as ifconfig and netstat
nmap	Network scanning and host discovery
wireshark	Graphical packet analysis
tcpdump	Command-line packet capture
openssh-client	Connect to remote systems over SSH
openssh-server	Allow SSH access to this system
python3-pip	Install Python-based tools
ca-certificates	Support trusted HTTPS connections
gnupg	Verify packages and signatures
lsb-release	Show Ubuntu distribution information
unzip / zip	Extract and create compressed archives
Wireshark Configuration

During installation, Wireshark asked whether non-root users should be allowed to capture packets.

The selected option was:

Yes

Reason:

Running packet capture through the Wireshark system group is safer than running Wireshark directly as root.

Docker Installation

Docker was installed from the Ubuntu package repository:

sudo apt install docker.io

The current user was added to the Docker group:

sudo usermod -aG docker $USER

After that, the system was rebooted so the group membership could take effect.

sudo reboot
Verification

After reboot, the installation was verified with:

git --version
nmap --version
wireshark --version
ssh -V
python3 --version
docker --version
docker ps

Docker was verified successfully when docker ps returned a container table without a permission error.

An empty container table is normal if no containers are currently running.

Result

The Ubuntu system is now prepared with a basic cybersecurity lab foundation:

Linux administration tools
Git/GitHub workflow support
Network scanning tools
Packet analysis tools
SSH client and server
Python package support
Docker container support
Lessons Learned

This lab provided practical experience with:

Ubuntu package management
Installing cybersecurity tools
Understanding command-line options
Using apt install -y
Understanding short and long command flags
Configuring Wireshark capture permissions
Installing Docker
Adding a user to the Docker group
Verifying installed tools
Security Notes

Do not publish personal usernames, internal IP addresses, hostnames, screenshots with sensitive data, or private system details in public repositories.
