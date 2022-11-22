# Born2beRoot Installation

Installing the Born2beroot VM has several steps involved: the creation of the virtual machine (VM) in VirtualBox, the installation of Debian OS with the creation of the root user, the partitionning of disks (including LVM) and the choice of initial software.

## Creating a Virtual Machine in Virtualbox

1. Launch VirtualBox & click New.
2. Name Born2beroot, sotre it in `/sgoinfre/goinfre/Perso/your_login` if at 42, or else on a large USB stick (important tip given by a stud !). Choose Linux & Debian.
3. `1024MB` memory is good.
4. Create a virtual hard disk now.
5. `VDI`
6. `Dynamically allocated`
7. `10 to 13 GB` is enough for both mandatory and bonus parts.
8. Start Born2beroot virtual machine.

## Installing Debian [more informations](https://www.debian.org/releases/stretch/s390x/ch06s03.html.fr#:~:text=Avec%20LVM%20avec%20chiffrement%2C%20l,traces%20d'une%20installation%20pr%C3%A9c%C3%A9dente.)

1. Select Debian ISO image as startup disk.
2. When Debian starts, choose `Install`.
3. Choose language, geographical & keyboard layout settings.
4. Hostname must be `your_login42`.
5. Domain name leave empty.
6. Choose strong root password & confirm.
7. Create user (`your_login` works for username & name).
8. Choose password for new user.

## Partitioning disk [more informations](https://www.debian.org/releases/stretch/s390x/apc.html.fr)

1. Choose `Manual partitionning`.
2. Choose `sda Harddisk - SCSI (0,0,0) (sda) ...`
3. `Yes create partition table`.
4. We will crete 2 partitions, the first will be for an unencrypted /boot partition, the other for the encrypted logical volumes :
  - `pri/log xxGB FREE SPACE` >> `Create a new partition` >> `500 MB` >> `Primary` >> `Beginning` >> `Mount point` >> `/boot` >> `Done`.
  - `pri/log xxGB FREE SPACE` >> `Create a new partition` >> `max` >> `Logical` >> `Mount point` >> `Do not mount it` >> `Done`.

### Encrypting disks

1. `Configure encrypted volumes` >> `Yes`.
2. `Create encrypted volumes`
3. Choose `sda5` ONLY to encrypt. We DO NOT want to encrypt the sda /boot partition.
4. `Done` >> `Finish` >> `Yes`.
... wait for formatting to finish...
5. Choose a strong password for disk encryption. DO NOT forget it!

### Logical Volume Manager (LVM)

1. Create a volume group:

`Configure the Logical Volume Manager` >> `Yes`.  
`Create Volume Group` >> `LVMGroup` >> `/dev/mapper/sda5_crypt`.
Create Logical Volumes:

Create Logical Volume >> LVMGroup >> root >> 2.8G
Create Logical Volume >> LVMGroup >> home >> 2G
Create Logical Volume >> LVMGroup >> swap >> 1G
Create Logical Volume >> LVMGroup >> tmp >> 2G
Create Logical Volume >> LVMGroup >> srv >> 1.5G
Create Logical Volume >> LVMGroup >> var >> 2G
Create Logical Volume >> LVMGroup >> var-log >> max 
When done, Display configuration details to double check & Finish.

2. Set filesystems and mount points for each logical volume:

Under "LV home", #1 xxGB >> Use as >> Ext4 >> Mount point >> /home >> Done
Under "LV root", #1 xxGB >> Use as >> Ext4 >> Mount point >> / >> Done
Under "LV swap", #1 xxGB >> Use as >> swap area >> Done
Under "LV srv", #1 3GB >> Use as >> Ext4 >> Mount point >> /srv >> Done
Under "LV tmp", #1 3GB >> Use as >> Ext4 >> Mount point >> /tmp >> Done
Under "LV var", #1 3GB >> Use as >> Ext4 >> Mount point >> /var >> Done
Under "LV var-log", #1 4GB >> Use as >> Ext4 >> Mount point >> Enter manually >> /var/log >> Done
Scroll down & Finish partitioning and write changes to disk. Yes.


mdp : Cla pour vde et Par pour root
