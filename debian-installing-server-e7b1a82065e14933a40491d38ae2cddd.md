# Debian Installing Server e7b1a82065e14933a40491d38ae2cddd

## Debian: Installing Server

Tags: debian, linux

Sau khi cài đặt đến phần "**Software Selection**". Chỉ chọn **SSH Server** và **Standard System Utilities**

**------------------------**

**BEGIN VIRTUALBOX**

**------------------------**

**Installing VirtualboxGuestAddition** (bắt buộc vì dùng cho auto update time bên dưới)

```
apt-get update
apt-get install build-essential module-assistant
m-a prepare
```

**Click on Install Guest Additions… from the Devices menu, then run:**

```
mount /media/cdrom
sh /media/cdrom/VBoxLinuxAdditions.run
```

**Setting up auto time synchronization in Windows Host**

```
# sync time every 10 seconds
C:\Program Files\Oracle\Virtualbox\VBoxManage guestproperty set <VM name> "/VirtualBox/GuestAdd/VBoxService/--timesync-interval" 10000
# adjustments if drift > 100 ms
C:\Program Files\Oracle\Virtualbox\VBoxManage guestproperty set <VM name> "/VirtualBox/GuestAdd/VBoxService/--timesync-min-adjust" 100
# sync time on restore
C:\Program Files\Oracle\Virtualbox\VBoxManage guestproperty set <VM name> "/VirtualBox/GuestAdd/VBoxService/--timesync-set-on-restore" 1
# sync time on start
C:\Program Files\Oracle\Virtualbox\VBoxManage guestproperty set <VM name> "/VirtualBox/GuestAdd/VBoxService/--timesync-set-start" 1
# at 1 second drift, the time will be set and not "smoothly" adjusted
C:\Program Files\Oracle\Virtualbox\VBoxManage guestproperty set <VM name> "/VirtualBox/GuestAdd/VBoxService/--timesync-set-threshold" 1000
```

**------------------------**

**END VIRTUALBOX**

**------------------------**

```
apt-get update
apt-get install sudo curl git htop net-tools apt-transport-https lsb-release ca-certificates

dpkg-reconfigure tzdata

systemctl disable apt-daily.service
systemctl disable apt-daily.timer
systemctl disable apt-daily-upgrade.timer
systemctl disable apt-daily-upgrade.service
```

**Installing Sudo**

```bash
apt-get install sudo
adduser <USERNAME>
usermod -aG sudo <USERNAME>
```

## Create a local keygen by puttygen.exe (in Windows) or ssh-keygen (in Linux)

```
touch ~/.ssh/authorized_keys &&chmod 700 ~/.ssh

# Paste the public key into authorized_keys
nano ~/.ssh/authorized_keys

# Modify the file permissions
chmod 600 ~/.ssh/authorized_keys
```

**installing Nginx**

```
curl -s https://nginx.org/keys/nginx_signing.key | apt-key add -
echo "deb https://nginx.org/packages/debian/ $(lsb_release -sc) nginx" | tee /etc/apt/sources.list.d/nginx.list
apt update && apt install nginx
curl -s https://gist.githubusercontent.com/dilongfa/e829fa21f59893b639d7f1979e111596/raw -o /etc/nginx/nginx.conf
usermod -aG nginx long
```

**Installing PHP**

```
curl -s https://packages.sury.org/php/apt.gpg | apt-key add -
echo "deb https://packages.sury.org/php/ $(lsb_release -sc) main" | tee /etc/apt/sources.list.d/php.list

apt update &&
apt install redis-server
sed -i 's/;cgi.fix_pathinfo=1/cgi.fix_pathinfo=0/g' /etc/php/7.3/fpm/php.ini

curl -s https://getcomposer.org/download/1.8.0/composer.phar -o /usr/local/bin/composer && chmod +x /usr/local/bin/composer
apt install zip unzip php-zip

curl -s https://gist.githubusercontent.com/dilongfa/ddc801af207ddf543738445484a85f95/raw | sed "s/\(@USER\|@GROUP\)/long/g" > /etc/php/7.3/fpm/pool.d/www.conf
```

**Installing MySQL 8.0**

```
curl -sL "http://pool.sks-keyservers.net/pks/lookup?op=get&search=0x8C718D3B5072E1F5" | apt-key add -
echo "deb http://repo.mysql.com/apt/debian $(lsb_release -sc) mysql-8.0" | tee /etc/apt/sources.list.d/mysql80.list
apt update && apt install mysql-server
mysql_secure_installation

### Create remote user
mysql -u root -p
CREATE USER 'long'@'localhost' IDENTIFIED BY 'long@1982';
GRANT ALL PRIVILEGES ON *.* TO 'long'@'localhost' WITH GRANT OPTION;
```

**Installing MariaDB**

```
curl -sL "http://keyserver.ubuntu.com/pks/lookup?op=get&search=0xF1656F24C74CD1D8" | apt-key add -
echo "deb http://sgp1.mirrors.digitalocean.com/mariadb/repo/10.3/debian $(lsb_release -sc) main" | tee /etc/apt/sources.list.d/mariadb.list
apt update && apt install mariadb-server
systemctl stop mysql && mysql_install_db && systemctl start mysql
mysql_secure_installation

# Create default remote user
mysql -u root -p
GRANT ALL PRIVILEGES ON *.* TO 'long'@'localhost' IDENTIFIED BY 'long@1982' WITH GRANT OPTION;
```

**Error during install MariaDB**

```
# if encounter similar error like:
demo@server$ sudo mysql_install_db
Installing MariaDB/MySQL system tables in '/var/lib/mysql' ...
131204  8:43:27 [ERROR] mysqld: Can't lock aria control file '/var/lib/mysql/aria_log_control' for exclusive use, error: 11. Will retry for 30 seconds
...
# Do:
sudo killall mysqld
sudo rm /var/lib/mysql/aria_log_control
sudo rm -rf /var/lib/mysql/ib_logfile*
# Re-run:
sudo mysql_install_db
sudo service mysql start
sudo mysql_secure_installation

# Use ssh tunnel with RSA publickey for Remote connect mysql from client software
nano /etc/mysql/my.cnf
```

**Installing Nodejs**

```
curl -s https://deb.nodesource.com/gpgkey/nodesource.gpg.key | apt-key add -
echo "deb https://deb.nodesource.com/node_10.x $(lsb_release -sc) main" | tee /etc/apt/sources.list.d/nodejs.list
apt update && apt install nodejs
```

**Installing Golang**

```
curl -s https://dl.google.com/go/go1.11.4.linux-amd64.tar.gz | tar -C /usr/local -xz
echo "export PATH=\$PATH:/usr/local/go/bin" >> /etc/profile
```

***

**FOR DEVELOPMENT**

**-----------------------**

**Installing dnsmasq**

```
apt-get update && apt-get install dnsmasq
curl -s https://gist.githubusercontent.com/dilongfa/ec9dc96509a249e8a8eb14179e16c397/raw | sed "s/@IP/10.0.0.3/g" > /etc/dnsmasq.conf
systemctl restart dnsmasq
```

**Installing samba**

```
apt-get update && apt-get install samba
curl -s https://gist.githubusercontent.com/dilongfa/36e6d2fbc0d6ada6cbd5e1ce2743dc79/raw -o /etc/samba/smb.conf

# Creating a new user for Networking Mapping Drive on Windows
smbpasswd -a long

systemctl restart smbd
```

**Cusomizing nano editor**

```
curl -s https://gist.githubusercontent.com/dilongfa/af43aa19bd13c8225662969a81da8a95/raw -o /etc/nanorc
curl -s https://gist.githubusercontent.com/dilongfa/556f2822b507865ac16f60fc33df6182/raw -o /usr/share/nano/conf.nanorc
```

**Setting up SSH for working with Git**

```
ssh-keygen -t ed25519 -C "you@example.com"
# Copy the publickey in ~/.ssh/id_ed25519.pub
# Paste this key into github or gitlab...
# Github: https://github.com/settings/keys
# Gitlab: https://gitlab.com/profile/keys
# then test:
ssh -T git@gitlab.com
ssh -T git@github.com
```

**Optimizing Bash History**

```
nano ~/.bashrc

# Add these lines to bottom
HISTCONTROL=ignorespace:erasedups
HISTTIMEFORMAT="%F %T  "
```

***

Solution to sync time between guest & host without installing Virtualbox guest addition :

1. Install NTP on your guest, and de-comment these lines in `/etc/ntp.conf`.

```
disable auth
broadcastclient
```

1. Activate broadcast on your host. For linux users, edit your `/etc/ntp.conf` file and configure the line

```
broadcast 192.168.123.255

```

For Windows users, activate the “Windows Time” service. You can then [read this page](https://social.technet.microsoft.com/Forums/windowsserver/en-US/04865e36-d43d-4167-a07d-9f28fd4f22fa/how-do-you-configure-windows-time-synchronization-in-a-workgroup-with-no-servers?forum=winserverDS) to configure it to broadcast time

**FOR PRODUCTION**

**-----------------------**

**Installing fail2ban**

```
apt update && apt install fail2ban
nano /etc/fail2ban/jail.d/jail-debian.local

# Input the following lines:
[sshd]
port = [CURRENT YOUR SSH PORT]
maxentry = 5

systemctl restart fail2ban
```

**Installing UFW**

```
apt update && apt install ufw
# Get SSH Port
# netstat -lt4pn | grep ssh | awk 'NR==1{split($4,v,":"); print v[2]}'

# config some basic rules include ssh,http,https and mysql...
# Allow SSH
# Allow HTTP
# Allow HTTPS
# Allow MySQL

# Enable UFW
sudo ufw enable
sudo ufw status verbose
```
