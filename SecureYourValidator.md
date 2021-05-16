## How to secure your Solana Validator ( WIP )
Security is an important responsibility of a Validator Owner.   We recommend that all validators take the basic steps to lock down their servers to prevent attacks on the network and to avoid loss of keys etc.

#### Update your software
The most basic thing you should do to secure your server is to update the local repositories and upgrade the operating system and installed applications by applying the latest patches. You should make this a habit once a month, think of it like the patch tuesday every month.
```
$ sudo apt update
$ sudo apt upgrade -y
```

#### Use a Firewall to secure network
Getting started with ufw ( uncomplicated firewall ) is easy. For example, to enable firewall, allow ssh access, enable logging, and check the status of the firewall, perform:
```
$ sudo apt install ufw
$ sudo ufw allow ssh/tcp
$ sudo ufw logging on
$ sudo ufw enable
$ sudo ufw status
```

#### Create a non root privileged account
*Golden Rule : Do not use root account for software installation and general management*
- Most installs of ubuntu come with a default account named ubuntu which is already sudo enabled.  If your server doesnt have a ubuntu default account, please create one and add the sudo privileges to that account.

```  #run these commands as root
$ adduser ubuntu # adds a user ubuntu, follow the prompts. 
$ usermod -a -G sudo ubuntu #adds ubuntu to sudoers file
```

#### Setup SSH key based authentication & Disable password based authentication
*This process is slightly complicated for beginners and could lock you out of your servers if not done right.. We are working on detailed instructions, please bear with us

#### Optional

###### Use Fail2Ban  
Warning : This may also have some false positiives and bloc ips of other validators unexpectedly. Please be careful in deploying this software. 
Fail2ban scans log files (e.g. /var/log/apache/error_log) and bans IPs that show the malicious signs -- too many password failures, seeking for exploits, etc. 
```
$ sudo apt install fail2ban  #install the fail2ban software
$ sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local #copy the default config file 
$ sudo service fail2ban restart #restart the service to pickup new config
$ sudo fail2ban-client status ssh #check status of banned ip addresses on your ssh port
$ sudo fail2ban-client status 8899 #check on your rpc port
```

###### Disable unused unix services

###### Use 2FA for Authentication
