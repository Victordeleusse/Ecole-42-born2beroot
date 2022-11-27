# Debian Configuration

This guide covers the installation of sudo, UFW, SSH, Password Policy, monitoring.sh.

## Sudo setup

Log in as root:  
`$ su root`  

Install sudo:
```
apt update    
apt upgrade    
apt install sudo    
```
Add user to sudo / user42 group:
```
# sudo usermod -aG sudo <username>
# sudo usermod -g user42 <username>` (to get "user42 as your prime group)
```
Then `exit` root session and `exit` again to return to login prompt. Log in again as user. 
Let's check if this user has sudo privileges: `$ sudo whoami`

It should answer `root`.   
If not, modify sudoers file as explained below and add this line:  
> `username  ALL=(ALL:ALL) ALL`

Edit `sudoers.tmp` file as root with the command:
`$ sudo visudo`

Add these default settings as per subject instructions:
```
Defaults     passwd_tries=3
Defaults     badpass_message="AIE AIE AIE wrong password!"
Defaults     logfile="/var/log/sudo/sudo.log"
Defaults     log_input
Defaults     log_output
Defaults     requiretty
```
If `var/log/sudo` directory does not exist, `mkdir var/log/sudo` and add:  
```
Defaults	logfile="/var/log/sudo/sudo.log"
Defaults	log_input,log_output
```

## UFW Setup

Install and enable UFW:
```
$ sudo apt update
$ sudo apt upgrade
$ sudo apt install ufw
$ sudo ufw enable
```
Check UFW status:
```
$ sudo ufw status verbose
```
Allow or deny ports:
```
$ sudo ufw allow <port>
$ sudo ufw deny <port>
```
Remove port rule:
```
$ sudo ufw status numbered
$ sudo ufw delete <port index number>
```
Be careful with the numbered, index are reinitialized after a deletion (check between deletes to get the correct port index number!)

In order to check where the connexion are establish : `$ sudo ss -tunlp` -> Allow ports to enable connexions ;)

## SSH Setup

Install OpenSSH:  
```
$ sudo apt update
$ sudo apt upgrade
$ sudo apt install openssh-server
```
Check SSH status:  
`$ sudo systemctl status ssh`  
Change SSH listening port to 4242:  
`$ sudo nano /etc/ssh/sshd_config`  
Find this line:  
`#Port 22`  
And uncomment (delete #) and change it to `4242`    

Restart SSH service:  
`$ sudo systemctl restart ssh`  
Don't forget to add a UFW rule to allow port 4242!  

### Forward the host port 4141 to the guest port 4242: 
in VirtualBox, go to:  
`VM` >> `Settings` >> `Network` >> `Adapter 1` >> `Advanced` >> `Port Forwarding`.    
add a rule: `Host port 4242` and `guest port 4242`.  
Restart SSH service after this change.

In the host terminal, connect like this:  
`$ ssh <username>@localhost -p 4242`

To quit the ssh connection, just exit.


## DHCP Setup (To get the IP attribution in static)

To get the "lo" interface which corresponds to the "loopback" interface (local loop), as well as the "ens192" interface corresponding to the network card connected to my local network:  
`$ sudo ifconfig -a`  
> `10.0.2.xx`  

To know the default gateway currently used by our machine:  
`$ sudo ip route show`  
> `10.0.2.xx`  

To get the DNS:  
`$ sudo cat /etc/resolv.conf`  
> `nameserver 10.0.2.xx`

Then, to change the actual setup:  
`$ sudo nano /etc/network/interfaces`
```
iface enp0s3 inet -> static
 adress 10.0.2.xx/24 (24 correspond to the netmask)
 gateway 10.0.2.xx
 dns-nameservers 10.0.2.xx
```
You are now unable to get any connexion from "outside", the DNS service is "unreachable".  
Iniatialize DNS server from Google:  
`$ sudo nano /etc/resol.conf`  
add `nameserver 8.8.8.8 8.8.4.4`  

Check your connexion by getting an answer from `ping google.fr`  

## Password policy

Edit `/etc/login.defs` and find `"password aging controls"`. Modify them as per subject instructions:
```
PASS_MAX_DAYS 30
PASS_MIN_DAYS 2
PASS_WARN_AGE 7
```
These changes aren't automatically applied to existing users, so use `chage` command to modify for any users and for root:
```
$ sudo chage -M 30 <username/root>
$ sudo chage -m 2 <username/root>
$ sudo chage -W 7 <username/root>
```
Use `chage -l <username/root>` to check user settings.

Install password quality verification library:
`$ sudo apt install libpam-pwquality`

Change the parameters -> from the subject:  
`$ sudo nano /etc/pam.d/common-password`

Find the following line:
`password [success=2 default=ignore] pam_unix.so obscure`  
And add:
`retry=3 minlen=10 lcredit=-1 ucredit=-1 dcredit=-1 maxrepeat=3 no_username enforce_for_root difok=7` placing difok after enforce_for_root to get root out of this setting

## Hostname, Users and Groups
The hostname must be your_intra_login42, but the hostname must be changed during the Born2beroot evaluation. The following commands might help:  
```
$ sudo hostnamectl set-hostname <new_hostname>
$ hostnamectl status
```
There must be a user with your_intra_login as username. During evaluation, you will be asked to create, delete, modify user accounts. The following commands are useful to know:  
`useradd` : creates a new user.  
`usermod` : changes the user’s parameters: `-l` for the username, `-c` for the full name, `-g` for groups by group ID as mentioned before.  
`userdel -r` : deletes a user and all associated files.    
`id -u` : displays user ID.  
`users` : shows a list of all currently logged in users.  
`cat /etc/passwd | cut -d ":" -f 1` : displays a list of all users on the machine.  
`cat /etc/passwd | awk -F '{print $1}'` : same as above.  

The user named your_intra_login must be part of the `sudo` and `user42` groups. You must also be able to manipulate user groups during evaluation with the following commands:
`groupadd` : creates a new group.  
`gpasswd -a` : adds a user to a group.  
`gpasswd -d` : removes a user from a group.  
`groupdel` : deletes a group.  
`groups` : displays the groups of a user.  
`id -g` : shows a user’s main group ID.  
`getent group` : displays a list of all users in a group.  

## Monitoring.sh
Write `monitoring.sh` file as root and put it in `/root directory`.

Check the following commands to figure out how to write the script:
```
uname : architecture information
/proc/cpuinfo : CPU information
free : RAM information
df : disk information
top -bn1 : process information
who : boot and connected user information
lsblk : partition and LVM information
/proc/net/sockstat : TCP information
hostname : hostname and IP information
ip link show / ip address : IP and MAC information
```

Remember to give the script execution permissions, i.e.:  
`chmod 755 monitoring.sh`  

The wall command allows us to broadcast a message to all users in all terminals. This can be incorporated into the monitoring.sh script or added later in cron.

To schedule the broadcast every 10 minutes, we need to enable cron:

# systemctl enable cron
Then start a crontab file for root:

# crontab -e
And add the job like this:

*/10 * * * * bash /root/monitoring.sh



