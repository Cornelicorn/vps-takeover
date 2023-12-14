# Reimage any VPS without KVM access 

```bash
mkosi build
rsync -zP image.tar root@ip:/root/
ssh root@<ip>
mkdir -p /run/nextroot
cd /run/nextroot
tar -xf /local_scratch/image.tar
cp /etc/ssh/ssh_host_* etc/ssh/
systemctl soft-reboot
ssh root@<ip>
cryptsetup close root # Close old root cryptsetup mapper if you have that
# Repartition hard drive and install your system
```
