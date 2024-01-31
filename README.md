How to Install Nagios Monitoring in RHEL

Step 1: Install Apache and PHP Packages

```# yum install -y httpd httpd-tools php gcc glibc glibc-common gd gd-devel make net-snmp openssl-devel unzip wget```
Step 2: Create Nagios User and Group
# useradd nagios
# groupadd nagcmd
Next, add both the nagios user and the apache user to the nagcmd group using the
# usermod -G nagcmd nagios
# usermod -G nagcmd apache

Step 3: Download Nagios Core and Nagios Plugin
# mkdir /root/nagios
# cd /root/nagios

# wget https://assets.nagios.com/downloads/nagioscore/releases/nagios-4.4.9.tar.gz
# wget https://nagios-plugins.org/download/nagios-plugins-2.3.3.tar.gz

Step 4: Extract Nagios Core and Nagios Plugins

# tar -xf nagios-4.4.9.tar.gz
# tar -xf nagios-plugins-2.3.3.tar.gz

Step 5: Installing and Configuring Nagios Core

# cd nagios-4.4.9/
# ./configure --with-command-group=nagcmd

# make all
# make install
# make install-init
# make install-init
# make install-config
Step 6: Customizing Nagios Configuration
# vi /usr/local/nagios/etc/objects/contacts.cfg
![image](https://github.com/kolleyvenkatesh/DevOpsNagios/assets/59635638/5b5193eb-62e1-4eda-ac34-3a3113aad04d)

Step 7: Install and Configure the Web Interface for Nagios
# make install-webconf
# htpasswd -s -c /usr/local/nagios/etc/htpasswd.users nagiosadmin
# systemctl restart httpd
Step 8: Compile and Install Nagios Plugin
# cd /root/nagios
# cd nagios-plugins-2.3.3
# ./configure --with-nagios-user=nagios --with-nagios-group=nagios
# make
# make install

Step 9: Verify Nagios Configuration Files
# /usr/local/nagios/bin/nagios -v /usr/local/nagios/etc/nagios.cfg
Step 10: Add Nagios Services to System Startup
# systemctl enable nagios
# systemctl enable httpd
Next, restart Nagios to make the new settings take effect.

# systemctl restart nagios

Step 11: Allow Nagios Web in Firewall (Optional)

# firewall-cmd --permanent --zone=public --add-service=http
# firewall-cmd --permanent --zone=public --add-service=https
# firewall-cmd --reload

Step 12: Log in to the Nagios Web Interface

http://Your-server-IP-address/nagios and Provide the username “nagiosadmin” and password.




