# Reimage any VPS without KVM access

This repo conceptually shows how you can use systemctl soft-reboot to switch
to a tmpfs root filesystem, which allows you to reformat and reinstall the
OS preinstalled on your VPS with a different OS.

## Tar based

```bash
mkosi --image=debian-tar build
rsync -zP mkosi.output/debian-tar.tar root@ip:/root/
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

## Image based

```bash
mkosi --image=debian-image build
rsync -zP mkosi.output/debian-image.raw root@ip:/root/image.raw
ssh root@<ip>
mount -o remount,size=1700M /run
cp image.raw /run/image.raw
systemd-dissect -M /run/image.raw /run/nextroot
cp /etc/ssh/ssh_host_* /run/nextroot/etc/ssh/
systemctl soft-reboot
ssh root@<ip>
dmsetup remove root # Close old root cryptsetup mapper if you have that
systemd-repart --copy-from --dry-run=no --empty=force /run/image.raw  /dev/vda
```
