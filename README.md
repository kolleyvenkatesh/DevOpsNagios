# How to Install Nagios Monitoring in RHEL

Step 1: Install Apache and PHP Packages

```
# yum install -y httpd httpd-tools php gcc glibc glibc-common gd gd-devel make net-snmp openssl-devel unzip wget

```


Step 2: Create Nagios User and Group

```
# useradd nagios
# groupadd nagcmd
```
Next, add both the nagios user and the apache user to the nagcmd group using the

```
# usermod -G nagcmd nagios
# usermod -G nagcmd apache
```

Step 3: Download Nagios Core and Nagios Plugin

```
# mkdir /root/nagios
# cd /root/nagios
# wget https://assets.nagios.com/downloads/nagioscore/releases/nagios-4.4.9.tar.gz
# wget https://nagios-plugins.org/download/nagios-plugins-2.3.3.tar.gz
```


Step 4: Extract Nagios Core and Nagios Plugins
```
# tar -xf nagios-4.4.9.tar.gz
# tar -xf nagios-plugins-2.3.3.tar.gz
```

Step 5: Installing and Configuring Nagios Core

```
# cd nagios-4.4.9/
# ./configure --with-command-group=nagcmd

# make all
# make install
# make install-init
# make install-init
# make install-config
```

Step 6: Customizing Nagios Configuration
```
# vi /usr/local/nagios/etc/objects/contacts.cfg
```
![image](https://github.com/kolleyvenkatesh/DevOpsNagios/assets/59635638/5b5193eb-62e1-4eda-ac34-3a3113aad04d)

Step 7: Install and Configure the Web Interface for Nagios
```
# make install-webconf
# htpasswd -s -c /usr/local/nagios/etc/htpasswd.users nagiosadmin
# systemctl restart httpd
```
Step 8: Compile and Install Nagios Plugin
```
# cd /root/nagios
# cd nagios-plugins-2.3.3
# ./configure --with-nagios-user=nagios --with-nagios-group=nagios
# make
# make install
```
Step 9: Verify Nagios Configuration Files
```
# /usr/local/nagios/bin/nagios -v /usr/local/nagios/etc/nagios.cfg
```
Step 10: Add Nagios Services to System Startup
```
# systemctl enable nagios
# systemctl enable httpd
```
Next, restart Nagios to make the new settings take effect.
```
# systemctl restart nagios
```
Step 11: Allow Nagios Web in Firewall (Optional)
```
# firewall-cmd --permanent --zone=public --add-service=http
# firewall-cmd --permanent --zone=public --add-service=https
# firewall-cmd --reload
```
Step 12: Log in to the Nagios Web Interface

http://Your-server-IP-address/nagios and Provide the username “nagiosadmin” and password.






# How to Add Linux Host to Nagios Monitoring Server Using NRPE Plugin

## Installing Nagios Plugins and NRPE On Remote Linux Host
Step 1: Install Required Dependencies
```
# yum install -y gcc glibc glibc-common gd gd-devel make net-snmp openssl-devel tar wget
```

Step 2: Create Nagios User

```
# useradd nagios
# passwd nagios
```

Step 3: Install the Nagios Plugins

```
# mkdir /root/nagios
# cd /root/nagios
# wget https://nagios-plugins.org/download/nagios-plugins-2.3.3.tar.gz
```

Step 4: Extract Nagios Plugins
```
# tar -xvf nagios-plugins-2.3.3.tar.gz
```
Step 5: Compile and Install Nagios Plugins
```
# cd nagios-plugins-2.3.3
# ./configure 
# make
# make install
```

Set the permissions on the plugin directory using the chown command.

```
# chown nagios.nagios /usr/local/nagios
# chown -R nagios.nagios /usr/local/nagios/libexec
```

Step 6: Installing NRPE Plugin
```
# cd /root/nagios
# wget https://github.com/NagiosEnterprises/nrpe/releases/download/nrpe-4.0.2/nrpe-4.0.2.tar.gz
# tar xzf nrpe-4.0.2.tar.gz
# cd nrpe-4.0.2
# ./configure --disable-ssl
# make all
```

Next, install the NRPE plugin daemon, and sample config files.

```
# make install-plugin
# make install-daemon
# make install-config
# make install-init
```
Step 7: Configuring NRPE Plugin
```
allowed_hosts=127.0.0.1,192.168.102
```

Now open /usr/local/nagios/etc/nrpe.cfg file and add the local host and IP address of the Nagios Monitoring Server.
![image](https://github.com/kolleyvenkatesh/DevOpsNagios/assets/59635638/aee0e45c-f229-4cb2-b082-59d52b0206e8)

```
# systemctl enable nrpe
# systemctl restart nrpe
```

Step 8: Open NRPE Port in Firewall (Optional)

```
# firewall-cmd --zone=public --add-port=5666/tcp
# firewall-cmd --zone=public --add-port=5666/tcp --permanent
```

Step 8: Verify NRPE Daemon Locally

```
service nrpe status
```

# Installing NRPE On Nagios Monitoring Server

Step 1: Install NRPE Plugin in Nagios

```
# cd /root/nagios
# wget https://github.com/NagiosEnterprises/nrpe/releases/download/nrpe-4.0.2/nrpe-4.0.2.tar.gz
# tar xzf nrpe-4.0.2.tar.gz
# cd nrpe-4.0.2
# ./configure --disable-ssl
# make all
# make install-plugin
# make install-daemon
# make install-init
```

# Adding Remote Linux Host to Nagios Monitoring Server

Step 1: Creating Nagios Host and Services File
```
# cd /usr/local/nagios/etc/
# touch hosts.cfg
# touch services.cfg
```

Now add these two files to the main Nagios configuration file. Open the nagios.cfg file with any editor.
```
# vi /usr/local/nagios/etc/nagios.cfg
```

![image](https://github.com/kolleyvenkatesh/DevOpsNagios/assets/59635638/c4c55296-bada-4bfb-b417-988857a63175)

You can specify individual object config files as shown below:
```

cfg_file=/usr/local/nagios/etc/hosts.cfg
cfg_file=/usr/local/nagios/etc/services.cfg
```
Step 2: Configuring Nagios Host and Services File

```
 vi /usr/local/nagios/etc/hosts.cfg
```
```
## Default Linux Host Template ##
define host{
name                            linux-box               ; Name of this template
use                             generic-host            ; Inherit default values
check_period                    24x7
check_interval                  5
retry_interval                  1
max_check_attempts              10
check_command                   check-host-alive
notification_period             24x7
notification_interval           30
notification_options            d,r
contact_groups                  admins
register                        0                       ; DONT REGISTER THIS - ITS A TEMPLATE
}

## Default
define host{
use                             linux-box               ; Inherit default values from a template
host_name                       NRPE                    ; chnage name as your wish
alias                           CentOS 6                ; A longer name for the server
address                         172.31.95.25            ; PRIVATE IP address of Remote Linux host
}
```

![image](https://github.com/kolleyvenkatesh/DevOpsNagios/assets/59635638/cb2b3186-e1a7-455b-9067-2bdebe9b9048)

Next open services.cfg file and add the following services to be monitored.

```
# vi /usr/local/nagios/etc/services.cfg
```
```
define service{
        use                     generic-service
        host_name               NRPE
        service_description     CPU Load
        check_command           check_nrpe!check_load
        }

define service{
        use                     generic-service
        host_name               NRPE
        service_description     Total Processes
        check_command           check_nrpe!check_total_procs
        }

define service{
        use                     generic-service
        host_name               NRPE
        service_description     Current Users
        check_command           check_nrpe!check_users
        }

define service{
        use                     generic-service
        host_name               NRPE
        service_description     SSH Monitoring
        check_command           check_nrpe!check_ssh
        }

define service{
        use                     generic-service
        host_name               NRPE
        service_description     FTP Monitoring
        check_command           check_nrpe!check_ftp
        }
```

![image](https://github.com/kolleyvenkatesh/DevOpsNagios/assets/59635638/6c8d4786-330e-481c-812f-1157a352cc73)

Step 3: Configuring NRPE Command Definition

```
# vi /usr/local/nagios/etc/objects/commands.cfg
```
```
define command{
        command_name check_nrpe
        command_line $USER1$/check_nrpe -H $HOSTADDRESS$ -c $ARG1$
        }
```

![image](https://github.com/kolleyvenkatesh/DevOpsNagios/assets/59635638/8cbd88b6-3ac9-43a3-8fbe-cf49d17275d2)

Finally, verify Nagios Configuration files for any errors.
```
# /usr/local/nagios/bin/nagios -v /usr/local/nagios/etc/nagios.cfg
```
```
# systemctl restart nagios
```
