# Todo
## General
1. Change Hostname
2. Properly implement host setup and test the tasks
3. Fix Cronjobs not curling ping URL
4. Create schedule for Docker_prune.yml
6. Template the LibrespeedExporter Backends
7. Add tags to the roles
8. Do docker-compose up -d --force-recreate if files from templates have been changed
10. Add www rule to the http entrypoint
11. Fix Ansible Lint errors
12. In nodeexporter add the hostname as part of the url
13. Remove Healthcheck Network
14. Split up the monitoring network, maybe role name to be the automatic network name
15. Setup blue host
16. variable for host lang? LANG=en_US.UTF-8 czwawka
17. Removal of docker networks is broken


Setup Repo Windows:
1. Import secrets privatekey and vault_pass
2. Remove Permission on vault_pass and just put read/write
3. Export the env variables
export EDITOR=nano
export ANSIBLE_CONFIG=/root/vscode/workspace/homelab/ansible.cfg
Open preview to the Side
4. Git Info hinterlegen
git config --global user.name "Fabio Sauna"
git config --global user.email "github@sauna.re"

installing azure cli:
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
az account set --subscription "<SUBSCRIPTION_ID>"

az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<SUBSCRIPTION_ID>"

 export ARM_CLIENT_ID="<APPID_VALUE>" 
 export ARM_CLIENT_SECRET="<PASSWORD_VALUE>" 
 export ARM_SUBSCRIPTION_ID="<SUBSCRIPTION_ID>"
 export ARM_TENANT_ID="<TENANT_VALUE>"

ansible-galaxy collection install ansibleguy.opnsense
python3 -m pip install --upgrade httpx

Fehlende URLs/Container
home.sauna.re
auth.sauna.re
Authelia-Redis Container
Autoscan Container
Certdumper AZURE Container
Flareresolverr Container
Geoip Container
Ghost-DB Container
Plausible-DB Container
Plausible-Events Container
Rclone AZURE Container
Recyclarr Container
Searxng-Redis Container
Spotify-DB Container
Vaultwarden-DB Container
WAF AZURE Container
Cloudplow Container
AdguardSync Container
Certdumper NAS Container

# Fehlende Services
Calibre #
Photoprism # Fix Import
Remote Backups
UptimeKuma Full Import
Immich
Crowdsec

## Create proper tasks from Linux Documentation

### Plex optimizations
sudo sh
echo  fs.inotify.max_user_watches=262144  >> /etc/sysctl.conf
sysctl -p

### Wireguard
sudo nano /etc/wireguard/wg0.conf

	[Interface]
	PrivateKey = SECRET
	Address = 192.168.251.4/32
	#DNS = 192.168.251.1

	[Peer]
	PublicKey = SECRET
	PresharedKey = SECRET
	AllowedIPs = 192.168.0.0/16
	Endpoint = vpn.sauna.re:51820

sudo chmod 600 /etc/wireguard/wg0.conf
sudo wg-quick up wg0
sudo systemctl enable wg-quick@wg0


### Proxmox Backup Client
wget https://enterprise.proxmox.com/debian/proxmox-release-bullseye.gpg -O /etc/apt/trusted.gpg.d/proxmox-release-bullseye.gpg
wget https://enterprise.proxmox.com/debian/proxmox-release-bookworm.gpg -O /etc/apt/trusted.gpg.d/proxmox-release-bookworm.gpg
nano /etc/apt/sources.list.d/pbs-client.list
deb http://download.proxmox.com/debian/pbs-client bullseye main
wget http://archive.ubuntu.com/ubuntu/pool/main/o/openssl/libssl1.1_1.1.0g-2ubuntu4_amd64.deb
dpkg -i libssl1.1_1.1.0g-2ubuntu4_amd64.deb
apt update
apt install proxmox-backup-client
proxmox-backup-client key create
 nano /etc/environment

### Proxmox Backup Client
PBS_PASSWORD=agent user password bitwarden
PBS_ENCRYPTION_PASSWORD=Bitwarden Encryption Key from earlier creation
crontab -e
	30 22 * * * proxmox-backup-client backup azure.img:/dev/sda --repository agent@pbs@192.168.100.4:8007:NFS && curl -fsS -m 10 --retry 5 -o /dev/null https://uptime.oiba.de/api/push/hPfSvgJlko?status=up&msg=OK&ping=

Tokei command:
.\tokei.exe "C:\Users\Fabio\Documents\VScode\workspace" --exclude **/*.md --exclude **/*.psd --exclude **/geerlingguy.docker/**/* --exclude **/geerlingguy.security/**/* --exclude **/robertdebock.logrotate/**/* --exclude **/githubixx.ansible_role_wireguard/**/* --exclude **/lucasheld.uptime_kuma/**/*


# New User
sudo adduser ansible
sudo usermod -aG docker ansible
echo 'ansible ALL=(ALL) NOPASSWD:ALL' | sudo tee -a /etc/sudoers
Docker SocketProxy traefik the docker GID differs! 993 for ansible, 999 for dmz



# FOr backup mounts:
mkdir /rclone_mount
ln -s / /rclone_mount/blue
ln -s / /rclone_mount/dmz


# Migration Datapoints
1. Crontab
2. Wireguard
3. SSH Keys/Config

## Install Common Programs
apt install ncdu htop nload iotop nano cron git nfs-common net-tools dnsutils iputils-ping curl wireguard lsof qemu-guest-agent

## Adguard commands
sudo systemctl stop systemd-resolved
sudo systemctl disable systemd-resolved
sudo rm /etc/resolv.conf
echo -e "nameserver 192.168.100.20\nnameserver 127.0.0.1\nnameserver 192.168.100.21\n search ." | sudo tee /etc/resolv.conf

# Proxmox New Node Setup
apt install htop lm-sensors lshw powertop
sensors-detect
powertop --auto-tune
wget https://raw.githubusercontent.com/notthebee/AutoASPM/refs/heads/main/autoaspm.py
python3 autoaspm.py
lspci -vv | awk '/ASPM/{print $0}' RS= | grep --color -P '(^[a-z0-9:.]+|ASPM )'