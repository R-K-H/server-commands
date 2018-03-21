# Commands used to configure cpanel servers
Source for some

- https://www.digitalocean.com/community/tutorials/how-to-set-up-a-firewall-using-firewalld-on-centos-7
- https://docs.oracle.com/cd/E20815_01/html/E20821/gisry.html

# Physical Needs
Contact hosting and request opening of port 25 for sending emails.

# Code
```
hostname server.domain.com

service ipchains stop
service iptables stop

chkconfig ipchains off
chkconfig iptables off

sudo firewall-cmd --list-all
sudo firewall-cmd --zone=public --add-service=http
sudo firewall-cmd --zone=public --add-service=https
sudo firewall-cmd --zone=public --add-service=ftp
sudo firewall-cmd --zone=public --add-service=smtp
sudo firewall-cmd --zone=public --add-service=smtps
sudo firewall-cmd --zone=public --add-service=dns
sudo firewall-cmd --zone=public --permanent --add-port=53/tcp
sudo firewall-cmd --zone=public --permanent --add-port=25/tcp

cd /home && curl -o latest -L https://securedownloads.cpanel.net/latest && sh latest
```

# Testing
```
host -t ns domain.com
whois domain.com
```

# Other
```
screen -ls 
screen -r screen id 
screen -rd
tail -f /var/log/cpanel-install.log
netstat -tulpn
SELINUX=disabled
SELINUXTYPE=targeted
iptables -A INPUT -m state --state NEW -p tcp --dport 8080 -j ACCEPT
```
