# Commands used to configure cpanel servers
Source for some

- https://www.digitalocean.com/community/tutorials/how-to-set-up-a-firewall-using-firewalld-on-centos-7
- https://docs.oracle.com/cd/E20815_01/html/E20821/gisry.html
- https://forums.cpanel.net/threads/cpanel-license-activation-issue.97409/
- https://forums.cpanel.net/threads/add-a-global-mail-filter-for-whm-and-accounts.55504/

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
sudo firewall-cmd --zone=public --permanent --add-service=http
sudo firewall-cmd --zone=public --permanent --add-service=https
sudo firewall-cmd --zone=public --permanent --add-service=ftp
sudo firewall-cmd --zone=public --permanent --add-service=smtp
sudo firewall-cmd --zone=public --permanent --add-service=smtps
sudo firewall-cmd --zone=public --permanent --add-service=dns
sudo firewall-cmd --zone=public --permanent --add-port=53/tcp
sudo firewall-cmd --zone=public --permanent --add-port=25/tcp
sudo firewall-cmd --zone=public --permanent --add-port=2087/tcp
sudo firewall-cmd --zone=public --permanent --add-port=2083/tcp
sudo firewall-cmd --zone=public --permanent --add-port=2089/tcp
sudo firewall-cmd --zone=public --permanent --add-port=2096/tcp

sudo firewall-cmd --reload
sudo systemctl restart network
sudo systemctl reload firewalld

cd /home && curl -o latest -L https://securedownloads.cpanel.net/latest && sh latest

/usr/local/cpanel/cpkeyclt
```

# DNS Resolvers
```
199.101.134.121
208.88.224.54
108.61.10.10
8.8.8.8
8.8.4.4

```

# Testing
```
host -t ns domain.com
whois domain.com
telnet domain.com 25
nc domain.com 25
netstat -tulpn
```

# Other
```
screen -ls 
screen -r screen id 
screen -rd
tail -f /var/log/cpanel-install.log

SELINUX=disabled
SELINUXTYPE=targeted
iptables -A INPUT -m state --state NEW -p tcp --dport 8080 -j ACCEPT
sudo iptables -I INPUT -p tcp --dport 3030 -j ACCEPT
sudo service iptables save
```

# Links
Remove IP from banned office emails
- https://sender.office.com/

# File Changes
```
touch /var/log/filter.log
chown mailnull:mail
chmod go+rw /var/log/filter.log

perl -0777 -i.original -pe 's/if error_message and \$header_from: contains "Mailer-Daemon@"\nthen\n  finish\nendif/\# Special config to remove errors\nlogfile \/var\/log\/filter.log 0644\nif (\n  \$header_from: contains "Mailer-Daemon@" or\n  \$header_from: contains "Mail Delivery System" or\n  \$message_body: contains "" or\n  \$message_body: contains "protection.outlook.com"\n)\nthen\n  logwrite "\$tod_log \$message_id from \$sender_address contained blocked keywords or email addresses"\n  seen finish\nendif/igs' /etc/cpanel_exim_system_filter


# Special config to remove error messages
logfile /var/log/filter.log 0644
if (
	$header_from: contains "Mailer-Daemon@" or
	$header_from: contains "Mail Delivery System" or
	$message_body: contains "" or
	$message_body: contains "protection.outlook.com"
	)
then 
	logwrite "$tod_log $message_id from $sender_address contained blocked keywords or email addresses"
	seen finish
endif
```
