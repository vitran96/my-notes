# Update stretch -> buster

```bash
apt update 
apt upgrade -y 

apt dist-upgrade -y 

dpkg -C 

#apt-mark showhold 
#dpkg --audit 

sed -i 's/stretch/buster/g' /etc/apt/sources.list 

apt update 
#apt list --upgradable 
apt upgrade -y 
apt dist-upgrade -y
```