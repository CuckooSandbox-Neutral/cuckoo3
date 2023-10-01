Create clear host Ubuntu Server 20.04 / 22.04

Minimum requirements:
+ 2 CPU cores (+ 2 to every VM inside)
+ 2 GB memry (+4 every VM inside)
+ 10 GB storage (+15GB every VM inside)

Create non-root user `cuckoo` or other.
Enable `Open-SSH server` for remote access.

Refresh system:
```
sudo apt update && sudo apt upgrade -y
```

Install dependencies:
```
apt install git build-essential python3-dev python3.8-venv libhyperscan5 libhyperscan-dev libjpeg8-dev zlib1g-dev unzip p7zip-full rar unace-nonfree cabextract yara tcpdump genisoimage qemu-system-x86 qemu-utils qemu-system-common -y
```

Fix the permissions for KVM access:
```
adduser cuckoo kvm
chmod 666 /dev/kvm
```

Add existing user `cuckoo` to pcap group:
```
groupadd pcap
adduser cuckoo pcap
chgrp pcap /usr/sbin/tcpdump
```

Allow creation of pcaps for non-root users:
```
setcap cap_net_raw,cap_net_admin=eip /usr/sbin/tcpdump
```

Ubuntu uses Apparmor, so we need to disable the profile for tcpdump (Alternatively, adding an exception would also do):
```
ln -s /etc/apparmor.d/usr.sbin.tcpdump /etc/apparmor.d/disable/
apparmor_parser -R /etc/apparmor.d/disable/usr.sbin.tcpdump
```

Reload the apparmor profile:
```
apparmor_parser -r /etc/apparmor.d/usr.sbin.tcpdump
```
