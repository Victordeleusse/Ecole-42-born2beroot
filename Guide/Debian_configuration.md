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
Add user to sudo group:

`# sudo usermod -aG sudo <username>`  

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
If `var/log/sudo` directory does not exist, `mkdir var/log/sudo`.

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

## DHCP Setup (To get the IP attribution in static)

To get the "lo" interface which corresponds to the "loopback" interface (local loop), as well as the "ens192" interface corresponding to the network card connected to my local network:  
`$ sudo ifconfig -a`  
10.0.2.15
To know the default gateway currently used by our machine:  
`$ sudo ip route show`  
10.0.2.2
To get the DNS:  
`$ sudo cat /etc/resolv.conf`  
nameserver 10.0.2.3



