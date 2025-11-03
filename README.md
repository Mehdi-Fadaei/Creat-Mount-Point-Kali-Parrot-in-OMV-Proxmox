# Creat-Mount-Point-Kali-Parrot-in-OMV-Proxmox
Create mount point

sudo mkdir -p /mnt/omv
sudo chown root:root /mnt/omv
sudo chmod 755 /mnt/omv


Create SMB credentials file

sudo tee /root/.smbcred > /dev/null <<'EOF'
username=REPLACE_WITH_USERNAME
password=REPLACE_WITH_PASSWORD
EOF
sudo chmod 600 /root/.smbcred


Manual mount (test)

sudo mount -t cifs //192.xxx.xxx.xxx/Data /mnt/omv -o credentials=/root/.smbcred,vers=3.0
# or try different SMB versions if needed:
# sudo mount -t cifs //192.xxx.xxx.xxx/Data /mnt/omv -o credentials=/root/.smbcred,vers=3.1.1
ls /mnt/omv


Persistent mount via /etc/fstab
Edit /etc/fstab and add one line (use sudo nano /etc/fstab):

# OMV SMB share
//192.xxx.xxx.xxx/Data /mnt/omv cifs credentials=/root/.smbcred,uid=1000,gid=1000,iocharset=utf8,vers=3.0 0 0


Then apply:

sudo mount -a


NFS alternative (if OMV exports NFS)

sudo apt update
sudo apt install -y nfs-common
sudo mkdir -p /mnt/omv
sudo mount -t nfs 192.xxx.xxx.xxx:/export/Data /mnt/omv
# persistent fstab entry:
# 192.xxx.xxx.xxx:/export/Data /mnt/omv nfs defaults 0 0


Create desktop shortcut to open mount in GUI (works on XFCE/GNOME)

tee ~/Desktop/OMV-NAS.desktop > /dev/null <<'EOF'
[Desktop Entry]
Name=OMV NAS
Comment=Open OMV NAS mount
Exec=xdg-open /mnt/omv
Icon=folder
Terminal=false
Type=Application
Categories=Utility;
EOF
chmod +x ~/Desktop/OMV-NAS.desktop


Useful commands

# show mounts
mount | grep omv

# unmount
sudo umount /mnt/omv

# debug mount errors
dmesg | tail -n 50
journalctl -xe --no-pager

