# Fixing a DHCP issue in Proxmox Ubuntu VMs
In Proxmox, if you create an Ubuntu VM that uses DHCP and after installation, if you clone the VMs, each VM will wind up with the same DHCP address. Here is a script to fix this.

`   `
```
if [ "$EUID" -ne 0 ]
  then echo "Please run as root"
  exit
fi

echo ----------- Update and Upgrade
apt update
apt upgrade -y

echo ----------- Installing agent
apt-get install -y qemu-guest-agent
echo ----------- Restarting agent
systemctl start qemu-guest-agent

echo ----------- Wiping Cloud-init setup
apt purge -y cloud-init
rm -rf /etc/cloud
rm -rf /var/lib/cloud
echo ----------- Reinstalling Cloud-Init
apt install -y cloud-init
echo ----------- Fixing DHCP
rm /etc/machine-id
rm /var/lib/dbus/machine-id
touch /etc/machine-id
ln -s /etc/machine-id /var/lib/dbus/machine-id

echo ----------- Done!
```