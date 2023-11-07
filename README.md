# icinga2
Icinga2 installation on Debian 12. Clean installation only set a static IP.  

  
Install some tools
```
su -
apt update && apt upgrade -y
apt install nano sudo curl gpg software-properties-common
```
Debian Repository
```
wget -O - https://packages.icinga.com/icinga.key | gpg --dearmor -o /usr/share/keyrings/icinga-archive-keyring.gpg
DIST=$(awk -F"[)(]+" '/VERSION=/ {print $2}' /etc/os-release); \
echo "deb [signed-by=/usr/share/keyrings/icinga-archive-keyring.gpg] https://packages.icinga.com/debian icinga-${DIST} main" > \
/etc/apt/sources.list.d/${DIST}-icinga.list
echo "deb-src [signed-by=/usr/share/keyrings/icinga-archive-keyring.gpg] https://packages.icinga.com/debian icinga-${DIST} main" >> \
/etc/apt/sources.list.d/${DIST}-icinga.list
```
Icinga2 installation
```
apt update
apt install apt-transport-https wget gnupg -y
apt update
apt install icinga2 -y
apt install monitoring-plugins -y
```
```
icinga2 api setup
systemctl restart icinga2
apt install icingadb
icinga2 feature enable icingadb
systemctl restart icinga2
apt install icingadb-redis -y 
systemctl enable --now icingadb-redis-server
systemctl restart icingadb-redis 
apt install mariadb-server -y 
mysql_secure_installation
```
Create the icinga2 db and user
```
mysql -u root -p
CREATE DATABASE icingadb;
CREATE USER 'icingadb'@'localhost' IDENTIFIED BY 'CHANGEME';
GRANT ALL ON icingadb.* TO 'icingadb'@'localhost';
QUIT;
```
```
mysql -u root -p icingadb </usr/share/icingadb/schema/mysql/schema.sql
```
> (nano /etc/icingadb/config.yml to change icingadb password)
```
systemctl enable --now icingadb
systemctl restart icingadb
systemctl restart icinga2
apt install icingaweb2 libapache2-mod-php icingacli -y 
apt install icingadb-web
icingacli setup token CREATE
```
Create the Icinga2 web2 db
```
mysql -u root -p 
CREATE DATABASE icingaweb2;
GRANT ALL ON icingaweb2.* TO icingaweb2@localhost IDENTIFIED BY 'CHANGEME';
QUIT;
```
```
apt install icinga-director -y 
chmod -R 775 /var/lib/icingaweb2
chmod -R 775 /etc/icingaweb2
```
PDF reporting support
```
apt install imagemagick imagemagick-doc -y 
apt install php-imagick -y
apt install -y apt-transport-https lsb-release ca-certificates wget 
wget -O /etc/apt/trusted.gpg.d/php.gpg https://packages.sury.org/php/apt.gpg
echo "deb https://packages.sury.org/php/ $(lsb_release -sc) main" | tee /etc/apt/sources.list.d/php.list
apt install imagemagick php7.2-imagick -y 
apt update %% apt upgrade -y
```
```
systemctl restart icingadb icinga2 icingadb-redis 
cat /etc/icinga2/conf.d/api-users.conf
```
> API token for root user
```
ip a
reboot
```
Access the web part of the setup.  

      http://<server ip>/icingaweb2/setup
      http://<server hostname>/icingaweb2/setup
      http://<server fqdn>/icingaweb2/setup




