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
>`username  ALL=(ALL:ALL) ALL`

Edit sudoers.tmp file as root with the command:

# sudo visudo
And add these default settings as per subject instructions:

Defaults     passwd_tries=3
Defaults     badpass_message="Wrong password. Try again!"
Defaults     logfile="/var/log/sudo/sudo.log"
Defaults     log_input
Defaults     log_output
Defaults     requiretty
If var/log/sudo directory does not exist, mkdir var/log/sudo.

