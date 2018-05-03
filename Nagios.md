# This Document helps in configuring Nagios server on a Linux machine

## Nagios - IT infrastructure Monitoring Tool

* Also known as Nagios Core
* Open source
* Used for IT Infrastructure monitoring
* Provides monitoring and alerting for Servers, Switches, Applications and Services
* Licenced unde GNU GPL V2
* www.nagios.org

## Nagios Server Installation Steps

1. Configure Host-name in /etc/hostname
2. Configure static IP
3. Install following pre-requisite

```shell
apt-get install apache2 mariadb-server php5-mysqlnd php5 libapache2-mod-php5 php5-cgi php5-gd php5-common php5-curl build-essential libgd2-xpm-dev openssl libssl1.0.0 libssl-dev xinetd apache2-utils unzip curl tree
```

While installing, add a password for MariaDB "root" user.

4. Create and configure a Nagios User account and group.

```shell
useradd nagios
```

The above command will create a new user named "nagios" and a group called "nagios" automatically.

```shell
groupadd nagcmd
```

The above command creates group "nagcmd" which helps clients to communicate.
To check if group or user is created successfully, use command "getent" followed by "group \group name\" or "passwd \user name\" to get information about that group or user respectively. It do not returnes anything if group or user is not present.

5. Add user "nagios" to group "nagcmd" we just created. Run the command.

```shell
usermod -a -G nagcmd nagios
```

6. Create a directory, downlaad and extract Nagios Source code in it.
7. Go to the Nagios extracted directory and run following commands to Configure and compile it.

```shell
./configure --with-nagios-group=nagios --with-command-group=nagcmd
make all
make install
make install-commandmode
make install-init
make install-config
```

8. Configure apache configuration file. Use the following command to copy the template file in apache available sites directory so we can make changes in it.

```shell
/usr/bin/install -c -m 644 sample-config/httpd.conf /etc/apache2/sites-available/nagios.conf
```

9. Add the apache user to nagcmd group

```shell
usermod -G nagcmd www-data
```

## Installing Nagios Plugin

Downlaod and extract Nagios plugin source code. Make sure directory
>/usr/local/nagios/libexec/

is empty. After that run following commands in "nagios-plugins" directory to configure and install it.

```shell
./configure --with-nagios-user=nagios --with-nagios-group=nagios --with-openssl
make
make install
```

Check that the directory
>/usr/local/nagios/libexec/

is not empty now. If it is empty, then installation was not correct.

## Installing NRPE - Nagios Remote Plugin Executor

Download and extract NRPE Source code. Use the following command to configure the installation.

```shell
./configure --enable-command-args --with-nagios-user=nagios --with-nagios-group=nagios --with-ssl=/usr/bin/openssl --with-ssl-lib=/usr/lib/x86_64-linux-gnu
make all
make install-daemon
make install-plugin
```

Now copy the the sample nrpe.cfg file to nagios etc directory to make changes in it.

```shell
cp sample-config/nrpe.cfg /usr/local/nagios/etc/nrpe.cfg
```

Now open the nrpe.cfg file we just copied in an editor and make following changes.
In the line that says "allowed_hosts=", add your Nagios server machine's IPv4 address. For example:
>allowed_hosts=127.0.0.1,::1,192.168.64.128

Now you can run the following command to start NRPE and check if port is open.

```shell
/usr/local/nagios/bin/nrpe -c /usr/local/nagios/etc/nrpe.cfg -d
netstat -ant | grep 5666
```

If the above command returns something like
>tcp        0      0 0.0.0.0:5666            0.0.0.0:*               LISTEN  
>tcp6       0      0 :::5666                 :::*                    LISTEN  

then NRPE is installed and running successfully.
Further, to check the version of NRPE on host machine, use following command with host's IP address.

```shell
/usr/local/nagios/libexec/check_nrpe -H 192.168.64.128
```

If it returns something like NRPE v3.1.0-rc1 then that's the version number. If it doesn't return anything or time out, then NRPE is not running or installed.

Now add following line to "/usr/local/nagios/etc/object/commands.cfg" file in the end to define the command "check_nrpe"
>define command{  
>        command_name    check_nrpe  
>        command_line    $USER1$/check_nrpe -H $HOSTADDRESS$ -c $ARG1$  
>        }  

## Configure Apache

Run following commands:

```shell
a2enmod cgi
a2enmod rewrite
```

Run following command to add username and password for apache. We used "nagiosadmin" as username and "admin" as password

```shell
htpasswd -c /usr/local/nagios/etc/htpasswd.users nagiosadmin
```

>New password:admin  
>Re-type new password:admin  

Run following command to add link of "nagios.conf" in "sites-available" to "site-enabled"

```shell
ln -s /etc/apache2/sites-available/nagios.conf /etc/apache2/sites-enabled/
```

## Verify and Start Nagios

Run the command to verify confgurations are done properly

```shell
/usr/local/nagios/bin/nagios -v /usr/local/nagios/etc/nagios.cfg
```

Restart machine and start Nagios by following command

```shell
service nagios start
```

Check status by following command

```shell
service nagios status
```

To see Nagios dashboard, open browser on a system in same network and open the web address:
>192.168.64.128/nagios  
>use the IP of your host machine instead  

## Connect a windows machine to Nagios server
