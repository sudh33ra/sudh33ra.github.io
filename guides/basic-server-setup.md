# Setting up a new server

## Setting up the timezone

```bash
timedatectl set-timezone Asia/Colombo
```

## Setting up the MOTD

```bash
vim /etc/motd

#################################################################
##  Anyone using this system expressly consents to monitoring  ##
##  and is advised that if such monitoring reveals possible    ## 
##      evidence of criminal activity, system personnel may    ##
##      provide the evidence from such monitoring to law       ##
##                   enforcement officials.                    ##
#################################################################
```

## Install PHP 7.2
```bash
yum install epel-release yum-utils -y
yum install http://rpms.remirepo.net/enterprise/remi-release-7.rpm
yum-config-manager --enable remi-php72
yum search php | more
yum install php php-common php-opcache php-mcrypt php-cli php-gd php-curl php-mysql -y
php -v
```

## Install mysql 8
```bash
rpm -Uvh https://repo.mysql.com/mysql80-community-release-el7-3.noarch.rpm
sed -i 's/enabled=1/enabled=0/' /etc/yum.repos.d/mysql-community.repo
yum --enablerepo=mysql80-community install -y mysql-community-server
systemctl start mysqld.service
systemctl enable mysqld.service
grep "A temporary password" /var/log/mysqld.log

mysql_secure_installation
```

## Install Tomcat

```bash
yum localinstall jre-8u181-linux-x64.rpm
sh -c "echo export JAVA_HOME=/usr/java/jre1.8.0_181/jre >> /etc/environment"
tar xvf apache-tomcat-9.0.8.tar.gz -C /opt/tomcat/ --strip-components=1
groupadd tomcat
useradd -M -s /bin/nologin -g tomcat -d /opt/tomcat/ tomcat
chgrp -R tomcat /opt/tomcat/
cd /opt/tomcat/
chmod -R g+rx conf
chown -R tomcat webapps/ work/ temp/ logs/
vim /etc/systemd/system/tomcat.service

# Systemd unit file for tomcat
[Unit]
Description=Apache Tomcat Web Application Container
After=syslog.target network.target

[Service]
Type=forking

Environment=JAVA_HOME=/usr/lib/jvm/jre
Environment=CATALINA_PID=/opt/tomcat/temp/tomcat.pid
Environment=CATALINA_HOME=/opt/tomcat
Environment=CATALINA_BASE=/opt/tomcat
Environment='CATALINA_OPTS=-Xms1024M -Xmx3000M -server -XX:+UseParallelGC'
Environment='JAVA_OPTS=-Djava.awt.headless=true -Djava.security.egd=file:/dev/./urandom'

ExecStart=/opt/tomcat/bin/startup.sh
#ExecStart=/usr/bin/authbind  "/opt/tomcat/bin/startup.sh"
#ExecStop=/bin/kill -15 $MAINPID
ExecStop=/opt/tomcat/bin/shutdown.sh

User=tomcat
Group=tomcat
UMask=0007
RestartSec=10
Restart=always

[Install]
WantedBy=multi-user.target

mkdir /usr/lib/jvm && ln -s /usr/java/jre1.8.0_181-amd64/ /usr/lib/jvm/jre
systemctl daemon-reload
systemctl start tomcat.service
systemctl enable tomcat.service
```
