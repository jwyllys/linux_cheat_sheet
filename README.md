# My Linux Cheat Sheet
Commands are for reference use only and should be verified or validated before using in production.

# New box deployment

[https://plusbryan.com/my-first-5-minutes-on-a-server-or-essential-security-for-linux-servers](https://plusbryan.com/my-first-5-minutes-on-a-server-or-essential-security-for-linux-servers)  
sudo vi /etc/ssh/sshd\_config    
**look for Root login and uncomment & change to no**  
\#PermitRootLogin yes  \>\>\> PermitRootLogin no  
**harden ssh by adding these lines at the bottom of the config:**  
	\# restricting ciphers  
	MACs hmac-sha2-512,hmac-sha2-256  
	Ciphers aes256-ctr,aes192-ctr,aes128-ctr

Hardening ssh further:   
[https://medium.com/@jasonrigden/hardening-ssh-1bcb99cd4cef](https://medium.com/@jasonrigden/hardening-ssh-1bcb99cd4cef)  
[https://linux-audit.com/audit-and-harden-your-ssh-configuration/](https://linux-audit.com/audit-and-harden-your-ssh-configuration/)  
[https://michael.mckinnon.id.au/2017/03/26/hardening-ssh-on-your-ubuntu-server/](https://michael.mckinnon.id.au/2017/03/26/hardening-ssh-on-your-ubuntu-server/)

this line should be added to /etc/sudoers  
            username ALL=(ALL:ALL) ALL  
%sudo   ALL=(ALL)       ALL  
joe ALL=(ALL) NOPASSWD: /full/path/to/command ARG1 ARG2

\#update your system to the latest everything with   (dist-upgrade would not be used generally)  
sudo apt-get update && sudo apt-get upgrade && sudo apt-get dist-upgrade

\# Disable gui in debian 8  
systemctl set-default multi-user.target  
\------------------------------------------------------------------------------------------------------------------------------------------------------------

# User deployment (adduser useradd)

In our lab, my uid (jdoe) is 1100   
if you start using that uid everytime you make a linux box (at least in our lab) it will create linux harmony in the nfs mounts  
/etc/passwd and /etc/group would reflect your uid and gid, then run a 'chown \-R jdoe:jdoe /home/jdoe'  
syntax for that is 'chown \-R \[recursive\] username:groupname /directory/to/change  
it will keep your permissions in sync

sudo vi /etc/passwd  
sudo vi /etc/shadow  
sudo vi /etc/group

sudo mkdir /home/username  
chown \-R username:group /home/username  
passwd username  
\#then test login

\# change user home dir  
usermod \-m \-d /home/username username  
\# assign a new primary group to user  
usermod \-g primarygroupname username  
\# assign secondary groups to a user (-a keeps already existing secondary groups intact otherwise they'll be removed)  
usermod \-a \-G secondarygroupname username  
\# To assign a new UID of 2005 to user called foo:  
usermod \-u 2005 foo  
\# To assign a new GID of 3000 to group called foo:  
groupmod \-g 3000 foo

\#useradd is native binary compiled with the system and available everywhere, portable.  
\#adduser is a perl script which uses useradd binary as back-end. More user friendly and interactive, not portable.  
useradd \-m \-p \<encryptedPassword\> \-s /bin/bash \<username\>  
\-m, \--create-home: Create user home directory  
\-p, \--password: Specify user password  
\-s, \--shell: Default shell for logon user

\#\# The users.sh script is prefered, but investigate before running.  
\# wget http://files.homelab.example.com/users.sh  
\#\# Run these lines this as root and not with sudo  
addgroup \--gid 1112 bsmith && useradd \-m \-s /bin/bash \-u 1112 \-g 1112 bsmith; usermod \-a \-G sudo bsmith;  
runuser \-l bsmith \-c 'mkdir \~/.ssh && echo \-e "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAAAQwEXAMPLEKEYREDACTED01XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX== bsmith@laptop2  \\n  
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAAAQwEXAMPLEKEYREDACTED02XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX== bsmith@laptop1  
" \> \~/.ssh/authorized\_keys';  
addgroup \--gid 1100 jdoe && useradd \-m \-s /bin/bash \-u 1100 \-g 1100 jdoe; usermod \-a \-G sudo jdoe;  
runuser \-l jdoe \-c 'mkdir \~/.ssh && echo \-e "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAAAQwEXAMPLEKEYREDACTED03XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX== john-doe-2048 \\n  
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAAAQwEXAMPLEKEYREDACTED04XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX== [jdoe@example.com](mailto:jdoe@example.com) \\n  
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAAAQwEXAMPLEKEYREDACTED05XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX== [jdoe@example.com](mailto:jdoe@example.com) \\n  
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAAAQwEXAMPLEKEYREDACTED06XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX== jdoe@laptop3 \\n" \> \~/.ssh/authorized\_keys';  
addgroup \--gid 1104 torrent && useradd \-m \-s /bin/false \-u 1104 \-g 1104 debian-transmission;  
addgroup \--gid 1300 confluence && useradd \-m \-s /bin/bash \-u 1300 \-g 1300 confluence;  
\------------------------------------------------

# System info commands:

user:             whoami  
hostname:    hostname  
Set Hostname: hostnamectl  
os:                cat /etc/\*release  
Os:	        lsb\_release \-a  
kernel:          uname \-a  
shell:            echo $SHELL  
cpu:              cat /proc/cpuinfo  
ram:             free \-m  
disk:             df \-h  
top:              htop     \<or\>    top  
Uptime:	       uptime  
Exit Status: echo $?

\# Most CPU Intensive processes  
ps auxf | sort \-nr \-k 3 | head \-2

\# Most Memory Intensive processes  
sudo ps auxf | sort \-nr \-k 4 | head \-2

\# check load averages  
top  
htop  
load average: number of processes running/waiting to run 1,5,15 minutes single CPU: 1 \= 100% load 2 CPU: 1 \= 50% load

\# Services systemctl journalctl  
sudo systemctl status service.name  
sudo journalctl \-r service.name

\# output top information to an output file:  
top \-b \-n 1 \> top\_output  
top \-b \-n 1 | tee top\_output

* CPU  
  Cpu(s) \- us: user CPU (processes), sy: system CPU (kernel), id: idle, wa: I/O wait  
  wa: I/O wait \- Generally when this is a low value the problem is not disk or network I/O issues.

* Memory  
  MEM available, used, free, buffer  
  SWAP available, used, free, cached  
  used \- cached \= true memory free  
  M \- sort by MEM  
  F \- choose sort column  
  OOM killer log \= /var/log/syslog

\# Keyboard locale settings for standard US setup  
~~sudo locale-gen us\_US.UTF-8~~  
sudo locale-gen "en\_US.UTF-8"

[https://www.thomas-krenn.com/en/wiki/Perl\_warning\_Setting\_locale\_failed\_in\_Debian](https://www.thomas-krenn.com/en/wiki/Perl_warning_Setting_locale_failed_in_Debian)  
\# to correct your locale, do the following 5 lines  
export LANGUAGE=en\_US.UTF-8  
export LANG=en\_US.UTF-8  
export LC\_ALL=en\_US.UTF-8  
locale-gen en\_US.UTF-8  
dpkg-reconfigure locales

\----------------------------------------------------

# Storage / hard drives / filesystem

\#show devices  
fdisk \-l  
\# Show the UUID of the disk, for more robust /etc/fstab mount entries   
blkid   
\# Show available space that has not been added to the partition  
lsblk  
\# Free disk space with human readable option  
df \-h

\# take uuid from blkid, copy to fstab using defaults  
\# then mkdir whatever dir's  
\# finally do "mount \-a" to see if mount works

\#\#\#\#\# Mount a drive over 2TB \#\#\#\#\#  
parted /dev/sda3  
p  \#show partition table  
d  \#delete existing partitions  
mklabel gpt  \#sets partition type, require for \>2TB  
unit TB  \#sets units  
mkpart primary 0.00TB 4.50TB  \#creates partition  
print  \#verify your changes  
w  \#write changes and exit  
mkfs /dev/sda3  
mkdir /mnt/hdd5000  
mount /dev/sda3 /mnt/hdd5000  
\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#

### **partition a new drive with ext3/4**

sudo fdisk \-l   \# locate the drive you want to partition (/dev/vdc)  
sudo fdisk /dev/vdc  
p  \#print partition table (verify that nothing exists)  
g  \# create a new GPT disk label (accept defaults)  
n  \# Create a new partition (accept defaults 3x)  
w  \# write changes to the disk and exit  
sudo mkfs.ext4 /dev/vdc1  
\# Refactor the following line and add to /etc/fstab for a persistent mount  
/dev/vdc1	/mnt/hdd500               ext4     defaults 0       0  
\# Use UUID’s generated for disks to avoid disks moving around in their a, b, c naming  
sudo blkid  
\# copy UUID=”xxxx” portion for our /dev/vdc1 disk  
\# Use the following line inplace of the previous example   
UUID=b9c071a5-46c2-4c03-8cc4-0e788e6e1198 /mnt/hdd500               ext4     defaults 0       0  
\# You’ll need to create the directory, use \-p to make parent directories automatically  
sudo mkdir \-p /mnt/hdd500  
sudo mount \-a                                     \# mount \-a   ( a for all mounts )  
\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#  
mount all volumes  
mount \-a    
\# should come back quickly

### **Resize ext3/4 partition**

Add disk space to vm through aws/azure/vmware  
sudo fdisk /dev/sda  
\# press “d” to delete the partition \#1  
\# Press “n” to create a new partition  
\# press “p”  to accept the default primary partition type  
\# press “1” to accept the default 1st partition and the rest of the defaults  
\# press “w” to write the changes

sudo resize2fs /dev/xvdf

### **fstab samples**

192.168.2.25:/mnt/hdd500 /mnt/hdd500   nfs     defaults,auto   0       0

\# A large logfile that is still being written to is taking up all disk space  
df \-h    \# is showing full / or /var partition   
\# stop the process that is writing to the log file  
\# lsof | grep process\_name\_generating\_logfile  
\# kill process or kill forcefully with kill \-9 process\_id  
\> /var/log/file.log         \# write nothingness into the log file as a workaround instead of deleting

### **Disk Usage of directory and files**

du \-shc \* 

## **NFS Mounts**

[https://www.howtoforge.com/install\_nfs\_server\_and\_client\_on\_debian\_wheezy](https://www.howtoforge.com/install_nfs_server_and_client_on_debian_wheezy)

\# Server   
apt-get install nfs-kernel-server nfs-common  
vi /etc/exports  
	/path/to/shared-stuff      \*(no\_wdelay,rw,no\_root\_squash,async,no\_subtree\_check)  
service nfs-kernel-server restart

\# Client   
\# Backup /etc/fstab  
cp \-p "/etc/fstab" "/etc/fstab.back-$(date \+%F)"  
apt-get install nfs-common  
\# temp mount until reboot  
mount 192.168.0.100:/home/client1 /mnt/nfs/home/client1  
\# persistent mount  
vi /etc/fstab  
	192.168.2.246:/path/to/shared-stuff /path/to/mount/locally nfs rw,sync 0 0  
sudo mount \-a

\# If you need to restart NFS due to lock or otherwise:  
/etc/init.d/portmap restart  
/etc/init.d/nfslock restart

## **AWS NFS/EFS Mounts**

Add security group / port **TCP 2049 inbound** on both ends (efs volume and instance)  
sudo mount \-t nfs4 \-o nfsver=4.1 remotehostname:/path/to/mount /local/mount/point  
[http://docs.aws.amazon.com/efs/latest/ug/mounting-fs.html](http://docs.aws.amazon.com/efs/latest/ug/mounting-fs.html) 

\---

\# Find Space Used by Still Open Deleted Files  
sudo /usr/sbin/lsof | awk '$10 \== "(deleted)" {sum+=$7} END {print sum}'

\# reserved blocks  
sudo tune2fs \-l /dev/sda1 | grep \-i "block count"

\# largest directories  
cd /  
sudo du \-ckx | sort \-n \> /tmp/duck-root  
sudo tail /tmp/duck-root  
sudo ls \-ls    \#sort by size  
sudo sh \-c "\> /var/log/messages"  \#erase contents

\# inodes  
df \-i

\# More inode details  
tune2fs \-l /dev/xvda1 | grep \-i inode

\# inode usage per sub directory   
find . \-printf "%h\\n" | cut \-d/ \-f-2 | sort | uniq \-c | sort \-rn

\# locate all small files  
for i in /\*; do echo $i; find $i |wc \-l; done

\# remount disk as Read+Write  
sudo mount \-o remount,rw /home

\# repair disk, unmount first, then   
fsck \-y \-C /dev/sda5

\# find superblock  
mke2fs \-n /dev/sda5  
fsck \-b 8193 \-y \-C /dev/sda5

\----------------------------------------------------

# Bash commands

location of bash history: 		echo $HISTFILE  
view bash history:		history  
delete specific line in history:	history \-d \<number\>  
clear bash history:  		history \-c && history \-w  
toggle history off:		set \+o history  
toggle history on:		set \-o history	  
show alias':  			alias  
make alias: 			alias \-p name='command'  
Set $path:			export PATH=$PATH:/opt/bin  
				export PATH=/opt/bin:$PATH  
\----------------------------------------------------  
**\# Make the bash history work over multiple tabs**                                                                                 
export HISTCONTROL=ignoredups:erasedups  \# Avoid duplicates                                                    
export HISTSIZE=100000                   \# big history   
export HISTFILESIZE=100000               \# big big history   
shopt \-s histappend                      \# append to history, don't overwrite it  
export PROMPT\_COMMAND="${PROMPT\_COMMAND:+$PROMPT\_COMMAND$'\\n'}history \-a; history \-c; history \-r" \#After each command, append to the history file and reread it   
\----------------------------------------------------

# Networking / Routing:

ifconfig \-a  
ifconfig eth0     \#check specific interface  
ifconfig eth0:1 192.168.1.69 netmask 255.255.255.0    \#virtual interface on eth0:1  
ifconfig eth0:1 192.168.1.69 netmask 255.255.255.0 gw 192.168.1.1  \# Adds the gateway  
Debian network interface file location: /etc/network/interfaces  

\# What is my WAN Ip Address  
dig \+short myip.opendns.com @resolver1.opendns.com

See current bandwidth: bwm-ng  
IP Tables cheat sheet: [http://www.cyberciti.biz/tips/linux-iptables-examples.html](http://www.cyberciti.biz/tips/linux-iptables-examples.html)

hosts file \- /etc/hosts  
\# see default gateway  
netstat \-nr 

\# ADD virtual IP (eth0:1)  
ifconfig eth0:1 192.168.1.25 netmask 255.255.255.0  
\# CHANGE eth0 IP  
ifconfig eth0 192.168.1.250 netmask 255.255.0.0 gw 192.168.1.254

\#ip command notes  
ip a   
ip addr add 10.1.1.2/24 dev eth1  
ip link set eth1 up

\# ADD a route  
sudo route add \-net 192.168.2.0 gw 192.168.1.254 netmask 255.255.255.0 dev eth1  
\# DELETE a route  
sudo route del \-net 192.168.2.0 gw 192.168.1.254 netmask 255.255.255.0 dev eth1  
\# ADD **DEFAULT** route (if “network: not connected” message returns, you need the default route)  
**ip route add default via 10.42.1.254 dev ens192**  
route add default gw 192.168.1.254  
ip route add {NETWORK/MASK} via {GATEWAYIP}  
ip route add 10.42.1.0/24 via 10.42.1.254  
ip route show  
\#Debian/Ubuntu static network conf  
\# /etc/network/interfaces  
\# /etc/sysconfig/network\_scripts/ifcfg-\<interface\>  
auto eth0  
iface eth0 inet static  
	address 192.168.2.XXX  
	hostname avocado  
	netmask 255.255.255.0  
	gateway 192.168.2.10  
\# virtual interface static /etc/network/interfaces  
iface eth0:0 inet static  
address 10.1.2.3  
netmask 255.0.0.0  
broadcast 123.255.255.255

\#check duplex  
sudo ethtool eth0

\# Change to full duplex  
sudo ethtool \-s eth0 speed 1000 duplex full  
sudo ethtool \-s eth0 autoneg off duplex full

\#speed test (download)  
./speedtest-cli 

\#bash alias for quickly displaying your ip  
alias ip='ifconfig en0 | awk '\\''$1 \== "inet" {print $2}'\\'''

\-------

## **networking**

\# interface not up?   
sudo ifup eth0

\# Is it on the local network? Find gateway:  
sudo route \-n

\# check ping to gateway  
ping \-c 5 gateway ip

\# can I route to the remote host?  
traceroute 10.1.2.5

\# Is the remote port open?  
telnet 10.1.2.5 80  
nmap \-p 80 10.1.2.5  
nc \-zv 192.168.1.15 22

\# listening ports? (open ports  
sudo netstat \-lnp | grep :80  
iptables \-L INPUT \-v \-n | grep 80

\# firewall rules blocking?  
sudo /sbin/iptables \-L

\# what is using network bandwidth? [http://www.ex-parrot.com/pdw/iftop/](http://www.ex-parrot.com/pdw/iftop/)   
\# install instructions: [http://www.tecmint.com/install-iftop-bandwidth-monitoring-tool-in-rhel-centos-fedora/](http://www.tecmint.com/install-iftop-bandwidth-monitoring-tool-in-rhel-centos-fedora/)  
iftop  
iftop \-i eth1  
iftop \-n (disable name resolution)  
P \= toggle port  
\----------------------------------------------------

## **Network traffic**

\# traffic capture  
sudo tcpdump \-n  
sudo tcpdump \-n host web1  
sudo tcpdump \-n not host web1  
sudo tcpdump \-n port 53  
sudo tcpdump \-n port 80 or port 53 \> outputfile  
sudo tcpdump \-n \-l host web1 | tee outputfile

\# raw packets / wireshark  
sudo tcpdump \-C 10 \-W 5 \-w output.pcap

## **NMAP** 

\# network scan local ip ranges for active hosts/ports, net scan network  
nmap 192.168.1.0-192.168.3.0  
\# Very Slow  
nmap 192.168.2.0/24  
\# Probably the fastest  
nmap 192.168.2.\*  
\# Scan subnet for active SSH clients  
nmap \-p 22 \--open \-sV 192.168.2.0/24   
\----------------------------------------------------

## **iperf**

\# Test network throughput  
iperf3 \-c ping.online.net \-p 5200  
\---------------------------------------------------

## **mtr**

\# provides a complete overview of the connection between two hosts on the Internet.  
sudo mtr \-rwc 20 \-o LSDAV 10.1.2.3

# DNS / Whois Stuff

Check what IP's a domain has  
root@server1:\~\# host \-t a example.com  
example.com has address 10.10.10.1

root@server1:\~\# whois 10.10.10.1 | grep CIDR  
CIDR:           10.0.0.0/8

\# DNS working?  
cat /etc/resolv.conf  
nslookup yahoo.com

\# DNS Changing too much?  
\# make the file immutable w/ chattr  
rm \-f /etc/resolv.conf  
editor /etc/resolv.conf  
chattr \+i /etc/resolv.conf

\# to stop resolv.conf from changing  
/etc/dhcp/dhclient.conf  
/etc/network/interfaces  
/etc/network/interfaces.d/  
/etc/resolveconf/  
   
ping \-c 5 DNS server  
dig web1.domain.com @10.1.1.1  
dig web1.domain.com \+trace

\# flush DNS cache:  
sudo /etc/init.d/nsdc restart

\----------------------------------------------------

# Apt Notes apt-get Debian Packages:

\# To get a list of packages installed locally do this in your terminal:  
dpkg \--get-selections | grep \-v deinstall

\# To update the repository and upgrade the packages  
sudo apt-get update && sudo apt-get upgrade

\# Search available packages and descriptions in repos  
sudo apt-cache search searchTerm

\# Show the packages wanting to be upgraded but don’t upgrade them; prompts to confirm  
\# Show upgraded packages; print out a list of all packages that are to be upgraded/  
apt-get \-u upgrade

\#  No action; perform a simulation of events that would occur but do not actually change the system;   
\# aka \--simulate, \--just-print, \--dry-run  
apt-get upgrade \-s           

\# Get version number of packageName  
apt-show-versions packageName  
apt-cache policy packageName

\# Hold/Lock a version of package-name  
sudo apt-mark hold \<package-name\>

\# Unlock a version of  \<package-name\>  
sudo apt-mark unhold \<package-name\>

\----------------------------------------------------

# Yum Notes rpm rhel Packages:

\# To install an rpm without dependency resolving:  
rpm \-i package.rpm

\# use yum to install rpm and resolve dependencies   
yum \--nogpgcheck localinstall \-y package.rpm

\----------------------------------------------------

# Dpkg

\# Used to install a local .deb file   
dpkg \-i my\_package.deb  
\# To lock a version of package-name  
echo "\<package-name\> hold" | sudo dpkg \--set-selections  
\# to unlock a version of package-name  
echo "\<package-name\> install" | sudo dpkg \--set-selections

\----------------------------------------------------

# SNMP:

apt-get install snmpd snmp  
yum install net-snmp net-snmp-utils net-snmp-devel

Create v3 user for net-snmp environments:  
/usr/bin/net-snmp-config \--create-snmpv3-user \-ro \-a passwordhere \-X DES \-x Encryptedpasswordhere \-A MD5 username  
\----------------------------------------------------

# SSH & SCP Stuff

belkin usb to serial com  
apt-get install screen  
screen /dev/ttyUSB0

\# create ssh key \[sshkey ssh-key\]  (it will prompt you for the name and location to save)  
ssh-keygen \-t rsa "your\_email@example.com"  
ssh-keygen \-t rsa \-b 4096 \-C "your\_email@example.com"  
ssh-keygen \-t rsa \-b 4096 \-C "[your\_email@example.com](mailto:your_email@example.com)" \-f \~/.ssh/someOutputFile.pem

(press enter a few times for defaults, don't enter a pw)  
It puts the 2 files into localhost:/\~/.ssh  id\_rsa and id\_rsa.pub

you need to ssh to the remote host (that you want passwordless  
authentication to)  
paste the local contents of your \~/.ssh/id\_rsa.pub into the  
\~/.ssh/authorized\_keys file of the remote host

If you'd prefer to use the key itself to gain access use:  
ssh \-i \~/.ssh/id\_rsa username@hostname  
[https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys--2](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys--2)  
\# SSH Key Permissions  
chmod 600 private\_key

Obtain public key from a private key  
ssh-keygen \-y \-f \~/.ssh/id\_rsa \> \~/.ssh/id\_rsa.pub

scp /path/to/local/file remoteHostname.com:/path/to/dropFile  
scp \-rp /path/to/local/dir remoteHostname.com:/path/to/dropDirectory  
\# SCP options:  
 \-C (compress the ssh connection)  
\-rp (recursive and preserve \[file permissions/ownership\]) Use this when scp’ing an entire directory

\#\# \~/.ssh/config file samples \[ssh config, ssh user config\]

\# external port 2265 routes to 22  
Host raspberry  
	Hostname homelab.example.com  
	User bsmith	  
	Port	2265  
\# Raspberry does not have external access, routing through homelab.example.com (also need to be defined)  
Host raspberry  
	Hostname 192.168.2.65  
	User bsmith	  
             ProxyCommand ssh homelab.example.com nc %h %p 2\> /dev/null  
\# This is needed for the above ProxyCommand routing  
Host homelab.example.com  
        Hostname homelab.example.com  
        User bsmith  
\# AWS sample w/ .pem key  
Host amazonHost  
	Hostname 10.1.2.3  
	User ubuntu  
	IdentityFile \~/.ssh/service-tower.pem

\----------------------------------------------------

# SSL Generation and Steps

1. Generate CSR  
   1. You will generate a .key and a .csr, keep both in folder for this project  
2. Send domainname.csr file to SSL provider  
3. SSL provider will return:   
   1. DigiCertCA.crt  
   2. Star\_domain\_com.crt  
   3. TrustedRoot.crt  
4. Add SSL Options to Apache (wildcard cert?)  
   1. SSLCertificateFile 	/etc/httpd/certs/acl/star\_wholefoods\_com.crt   	\# File from step 3b  
   2. SSLCertificateKeyFile	 /etc/httpd/certs/acl/domainname.key        	 \# File from step 1a  
   3. SSLCertificateChainFile /etc/httpd/certs/acl/DigiCertCA.crt         		 \# File from step 3a  
5. Add SSL Options to Nginx  
   1. ssl\_certificate   		 /etc/nginx/ssl/2015-homelab/cert\_chain.crt;	  
   2. ssl\_certificate\_key  	 /etc/nginx/ssl/2015-homelab/netcam.homelab.example.com.key;  
      1. You may need to create the cert chain file by  
      2. cat \*yourdomainname\*.crt \*yourdomainname\*.ca-bundle \>\> cert\_chain.crt  
      3. Cert\_chain.crt will be your ssl\_certificate  
6. Don’t break Apache/Nginx, do a config test before restarting  
   1. sudo service apache configtest  
   2. sudo service httpd configtest  
   3. sudo service nginx configtest  
7. Security\!

   

\# Generate unique key with messages file for added randomness  
openssl genrsa \-rand /var/log/messages \-out intrac-qa.wholefoodsmarket.com.key 2048

\# Generate the csr with sha256, do not enter a challenge password  
openssl req \-new \-sha256 \-key intrac-qa.wholefoodsmarket.com.key \-out intrac-qa.wholefoodsmarket.com.csr

\# Generate a basic server key file using aes256 and 2048bit length  
openssl genrsa \-aes256 \-out server.key 2048

\# Generate a basic csr   
[https://www.digicert.com/easy-csr/openssl.htm](https://www.digicert.com/easy-csr/openssl.htm)  
openssl req \-new \-newkey rsa:2048 \-nodes \-out domainname.csr \-keyout domainname.key

\# Generate basic crt:  
openssl x509 \-req \-days 365 \-in server.csr \-signkey server.key \-out server.crt

\# Check a certificate is correctly being served  
\# Access http://www.sslshopper.com/ssl-checker.html and put the hostname, for example http://www.sslshopper.com/ssl-checker.html\#hostname=jira.example.com.   
\# Can be used to verify if the intermediate chain is served with the public certificate. Also this can be done with:  
openssl s\_client \-connect [www.paypal.com:443](http://www.paypal.com:443)

\# Export a Certificate (public key) from JKS to PEM  
keytool \-exportcert \-alias \<alias\> \-keypass changeit \-rfc \-file public.crt \[-keystore \<keystore\[.jks\]\>\]

\# Export and convert a Private Key from JKS to PEM  
keytool \-importkeystore \-srckeystore \<keystore\[.jks\]\> \-srcstoretype JKS \-destkeystore tmp.p12 \-deststoretype PKCS12 \-srcstorepass changeit \-deststorepass changeit \-srcalias \<alias\> \-destalias \<alias\> \-srckeypass changeit \-destkeypass changeit  
openssl pksc12 \-in tmp.p12 \-out private.key \-passin pass:changeit \-passout pass:changeit

\# Fetch a Certificate and put it into a PEM  
openssl s\_client \-connect \<host\>:443 \< /dev/null | sed \-ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' \> public.crt

\# Output details of a Certificate  
openssl x509 \-in public.crt \-text \-noout

\# Output details of a Private Key  
openssl rsa \-in private.key \-text \-noout

\# Verify a Certificate exists in a JKS  
keytool \-v \-list \-keystore \<keystore\> \-alias \<alias\> \-storepass \<pass\>

\# Create a self-signed openSSL certificate  
openssl req \-x509 \-nodes \-days 365 \-newkey rsa:2048 \-keyout private.key \-out public.crt

\# Convert public.crt to PEM format  
openssl x509 \-in public.crt \-out server.pem \-outform PEM

\# Convert openSSL certificate into PKCS12  
openssl pkcs12 \-export \-in public.crt \-inkey private.key \> hostname.p12

\# Verify the contents of a PKCS12  
openssl pkcs12 \-in host.p12

\# Convert PKCS12 into JKS  
keytool \-importkeystore \-srckeystore host.p12 \-srcstoretype pkcs12

\# Grab an entire cert chain (including CA, intermediate, leaf) in one line:  
echo "" | openssl s\_client \-host foo.bar.com \-port 443 \-showcerts | awk '/BEGIN CERT/ {p=1} ; p==1; /END CERT/ {p=0}' \> allcerts.pem  
\# Take that allcerts.pem file and add it to the Java truststore (or wherever you need it), and you're in business.   
\------------------------------------------------------------------------------------------------------------------------------------

# Encryption/Decryption gpg

sudo apt install gpg  
\# Generate Encryption keys ( in \~/.gnupg)  
gpg \--gen-key  
\# If it complains about not enough entropy, run the following in another session on the same host  
find / \> /dev/null        \# Used to Generate more Enthropy  
\# Encrypt a file  (entering a password is unnecessary imo)  
gpg \-c SomeFile.txt  
\# Decrypting gpg file  
gpg SomeFile.txt.gpg  
\# List Secret Keys  
 gpg \--list-secret-keys \--keyid-format LONG

# CA Certs

With a little research you can usually find the CA root cert you need online  
sudo mkdir /usr/local/share/ca-certificates/cacert.org

sudo wget \-P /usr/local/share/ca-certificates/cacert.org http://www.cacert.org/certs/root.crt http://www.cacert.org/certs/class3.crt

sudo update-ca-certificates  
\------------------------------------------------------------------------------------------------------------------------------------

# Logging 

count how many lines are returned for a grep in a file:  
sudo grep thingtofind target |wc \-l

Check auth log for invalid usernames  
grep Invalid /var/log/auth.log | awk '{ print $8 }'

Check auth log for failed login attempts  
sudo cat auth.log | grep failed

View last 10 or X number of fail2ban log entries:  
tail /var/log/fail2ban.log  
tail \-15 /var/log/fail2ban.log

\----------------------------------------------------------------------------------------------------------------------------

# Log Parsing with grep

\# Give a sorted list of all ERRORs by frequency descending  
grep \-i ERROR logname.log | sort \-k8|uniq \-cf8|sort \-gr|less  
grep \-i Exception logname.log | sort \-k8|uniq \-cf8|sort \-gr|less

\# Find count of exception types descending  
grep \-o "\\w\*Exception" myLog.log | sort \-r | uniq \-c

\# Find exceptions by class  
grep \-o "w\* \-|\\w\*Exception" myLog.log | sed 'N; s/ \-n/ /' | sort \-r | uniq \-c

\# Find more details about above exceptions  
grep \-e "MyClass.\*Exception" myLog.log

\----------------------------------------------------------------------------------------------------------------------------

# VNC vncserver vncviewer

vncserver :5950 \-geometry 1024x768 \-depth 24

\----------------------------------------------------------------------------------------------------------------------------

# Install from source

Git clone http://git.somepackage.com  
./configure  
make  
make install  
\#\#   ./configure && make && sudo make install  
\----------------------------------------------------------------------------------------------------------------------------

# Tarball

Tarball create:  
tar \-zcvf vm1January2015.tar.gz /mnt/slave3/slave3keep/esxi/vm1Janurary  
Tarball Extract:  
tar \-xzvf vm1January2015.tar.gz 

* \-z: Zip/compress archive using gzip program  
* \-c: Create archive  
* \-x: Extract files  
* \-v: Verbose i.e display progress while creating archive  
* \-f: Archive File name

Tar and exclude directories:  
tar \--exclude='./folder' \--exclude='./upload/folder2' \-zcvf /backup/filename.tgz .

\# unzip a zip file to a specific folder  
unzip file.zip \-d destination\_folder  
\# zip up an entire directory   
zip \-r file.zip source\_folder

\-----------------------------------------------------------------------------------------------------------------------------------  
hdparm SD card speed testing  
\# test speed of an SD card:   
sudo hdparm \-t /dev/sdb

\-----------------------------------------------------------------------------------------------------------------------------------

# Rsync

\# Without Deleting:  
rsync \-avzi \--progress \--log-file=/home/bsmith/rsync/server1\_vm1\_$(date \+%Y%m%d).log  \-e "ssh \-i /home/bsmith/server1-key" /mnt/slave3/slave3keep/esxi/vm1 bsmith@backup.example.com:/mnt/hdd500/esxi/vm1  
\# Over SSH  
rsync \-avzi bsmith@device1:/mnt/slave1/Movies \~/Movies/

\# With Deleting:  
rsync \-avi \--progress \--log-file=/var/log/rsync/slave\_$(date \+%Y%m%d).log \--delete /mnt/slave1/ /mnt/slave3/

\# your file .rsyncexcludelist is a list of folders or files that are untouched for syncing or deleting.

I use a simple bash script to check for proper mounts (If source mount isn’t there, you’ll delete everything on the destination end when using \--delete)

\#\!/bin/bash  
if mountpoint \-q "/mnt/slave3/"   
    then  
      echo "Rsyncing” hostname  
rsync \-avzi \--progress \--log-file=./rsynclog\_$(hostname)\_$(date \+%Y%m%d).log \-e "ssh \-i \~/.ssh/server1.pem" /mnt/backup/ bsmith@backup.example.com:/mnt/backup/  
fi

\----------------------------------------------------------------------------------------------------------------------------------

# Bash/Command Line Navigation/CLI [http://cupfullofcode.com/blog/2013/07/03/efficient-command-line-navigation/index.html](http://cupfullofcode.com/blog/2013/07/03/efficient-command-line-navigation/index.html)

Ctrl+a		Skip to beginning of command line  
Ctrl+e		Skip to end of command line  
Ctrl+u		Erase the command line  
Ctrl+Shift+v	Paste into the Command line  
cd \-		Go to previous directory  
cd \~		Go current user’s home directory  
cp file.txt{,.bk}	Copy file.txt to file.txt.bk 

ls \-larS		List files in reverse order of size(S)

List files in reverse order of date modified (t)  
ls \-lart

\----------------------------------------------------------------------------------------------------------------------------------

# Find command and locate

[http://man7.org/linux/man-pages/man1/find.1.html](http://man7.org/linux/man-pages/man1/find.1.html)  
\# Find files modified within the last 5 days:  
find /var/log \-mtime \-5  
\# Find files with 4 characters in the beginning of file name, followed by 16, followed by anything; Move it into the folder named 2016  
find . \-name "????16\*" \-exec mv \-i {} \-t ./2016 \\;  
\# find directories (-type d) created (ctime) 10 or more (+) days ago and remove them forcefully  
find /path/to/base/dir/\* \-type d \-ctime \+10 | xargs rm \-rf

find /tmp \-type f \-name "\*.txt" \-exec rm \-f {} \\;

\-name pattern  
	Pattern will be matched with case sensitivity  
\-iname	pattern  
Pattern will be matched without case sensitivity   
Numeric arguments can be specified as  
       \+n     for greater than n,  
       \-n     for less than n,  
       n      for exactly n.  
\-mtime	n  
File’s data was last modified n\*24 hours ago  
\-atime	n  
	File’s data was last accessed n\*24 hours ago  
\-ctime	n  
	File’s data was created n\*24 hours ago  
\-type  
\-d   directory  
\-f    file  
\-l    symbolic link

\#use locate instead of find for quicker searches  
sudo updatedb && locate panel.js

\----------------------------------------------------------------------------------------------------------------------------------

# Diff

\# find the differences between two directory trees:  
diff \--brief \-r dir1/ dir2/  
\# If you also want to see differences for files that may not exist in either directory:  
diff \--brief \-Nr dir1/ dir2/  
\# Show both files side by side  
diff \-y file1 file2

\----------------------------------------------------------------------------------------------------------------------------------

# VI Notes vim

\# vim notes  
\# Substitue within vi  (g)lobally with (c)onfirmation options:  
:%s/search\_string/replacement\_string/gc  
\# Set the numbers on the left hand side:  
:set number  
\# To Jump to line number 93:  
93G  
\# Skip forward by word:  
w  
\# Skip backward by word:  
b  
\# Delete everything from here till the end of line:  
D  or  d$  
\# Delete the next word:  
dw  
\# Skip to beginning of line:  
^  
\# Delete till the next space:  
Dt  
\# Indent (tab) the following 3 lines  
5\>\>  
\# undo last change (can be repeated to undo preceding commands)  
u:   
\# Redo changes which were undone (undo the undos).   
Ctrl-R:   
\# Delete all lines containing the string “profile”  
:g/profile/d

**Visual Block Mode**    (needs reformatting)

First, move the cursor to the first char of the first line in block code you want to comment, then type:

CTRL \+ V  
then vim will go in to VISUAL BLOCK mode. Use j to move the cursor down until you reach the last line of your code block. Then type:

Shift \+ I  
now vim go to INSERT mode and the cursor is at the first char of the firts line. Finally, type \# then ESC and the code block is now commented.

To decomment, do the same things but instead of type Shift \+ I, you just type x to remove all \# after highlight them in VISUAL BLOCK mode.

[http://unix.stackexchange.com/questions/120615/how-to-comment-multiple-lines-at-once](http://unix.stackexchange.com/questions/120615/how-to-comment-multiple-lines-at-once)

\----------------------------------------------------------------------------------------------------------------------------------

# Grep

Search recursively for files containing the word “bark” in the directory /tmp.   
grep \-ri "bark" /tmp  
r- Recursive  
i- Case Insensitive  
\-w search full words only, not partial or within strings  
\-v exclude/invert  
\--color=auto highlights results  
\-n show line number  
\-c count

\# grep for a string in a text file inside a zip file (or multiple zips)  
unzip \-c \\/path/to/file/\*.zip | grep stringtofindinsidetextfile

\# Give a sorted list of all ERRORs by frequency descending  
grep ERROR filename | sort \-k8|uniq \-cf8|sort \-gr|less

\# search for multiple words  
grep "error\\|warn\\|fatal"

\# search all files in a directory  
grep \-r thingtofind .  
grep \-R ./\* thingtofind

\# search all files recursively and case insensitive  
grep \-ri “tcp” /var/log/

\----------------------------------------------------------------------------------------------------------------------------------

# Git Notes

\# Pull/Clone/Download git repository  
git clone 

\# Push local origin changes to remote branch named Master  
git push origin master

\# Commit changes with message  
git commit \-m “Good notes go here”

\# Switch branches  
git checkout \-b MyBranchName  
git fetch && git checkout dev

\# To revert changes to a single file   
git checkout path/to/filename

\# Reset local origin to remote  
git fetch \--all  \# Will get everything but not actually update the working directory

\# list all branches  
git branch \-a

\# create a new branch and switch to it  
git branch branchname && git checkout branchname

\# open existing branch (fetch any new data first)  
git fetch && git checkout branchname  
\# Delete local branch  
git branch \-d the\_local\_branch

\# to checkout another existing branch, like dev  
git fetch && git checkout dev

\# git pull to incorporate changes made in remote branch  
git pull origin the\_remote\_branch\_name

\# edit a commit message that has not been pushed yet  
git commit \--amend  
git reset \--hard \#( hard means replace all in working directory) origin/master 

\#\#\#\#\# this might be better done with a git rebase  \#\#\#\#\#  
git stash                                            \# save local changes  
git reset \--hard origin/master     
git stash apply                                  \#re-apply the stashed changes  
\# 

\# To find the tag name for a version:   
git tag | grep 5.10.7

\# To find something in a pom file:   
git show project-5.10.7:pom.xml | grep 'some\_shit'

\# Configure your user when attempting to commit code for bitbucket  
git config \--global user.email "[jdoe@example.com](mailto:jdoe@example.com)"  
git config \--global user.name "jdoe"  
\-----------------------------------------------------------------------------------------------------------------------------------

# Unix Permissions

[http://en.wikipedia.org/wiki/File\_system\_permissions\#Classes](http://en.wikipedia.org/wiki/File_system_permissions#Classes)

[http://permissions-calculator.org/](http://permissions-calculator.org/)

\# Add e\[x\]ecutable permissions for \[u\]ser for file.sh  
chmod u+x file.sh  
\# Add executable permissions for user for file.sh  
chmod 0100 file.sh  
\-rwxr-xr-x    \-\[rwx\]\[r-x\]\[r-x\]  
                      \[u\]  
u the owner user  
g the owner group  
o others (neither u, nor g)

                           

\-----------------------------------------------------------------------------------------------------------------------------------

# Processes / Services

\# show current top processes  
top  
htop

\# find pid of running process by name  
ps \-ef | grep name  
ps aux | grep name  
pgrep name

\# see every process on the system  
ps \-A    
ps \-e  
ps aux       
ps auxfwww     (bob’s favorite) 

\# terminating processes  
kill \-9 pid   \#force kill signal  
kill \-15 pid   \#clean kill

\# check dd status  
sudo kill \-USR1 $(pgrep ^dd)

\# In debian install sysv-rc-conf to visually disable and enable services from running during given runlevels or boot  
sudo apt-get install  sysv-rc-conf  
sudo sysv-rc-conf

\# Disable services [http://askubuntu.com/questions/19320/how-to-enable-or-disable-services](http://askubuntu.com/questions/19320/how-to-enable-or-disable-services)

\-----------------------------------------------------------------------------------------------------------------------------------

#  NTP

\# There are 2 ntp packages, ntp(daemon) and ntpdate(single update)  
ntp uses a drift file, pid file, conf file

\# NTPd is probably already installed and needs to be started/enabled  
sudo systemctl restart ntpd  
sudo systemctl enable ntpd

ntpdate \-s time.nist.gov  
\# Test ntp 

ntpq \-p  
ps aux |grep ntp   \# Always check that the service is actually running 

\-----------------------------------------------------------------------------------------------------------------------------------

# iptables

\# List iptables rules with line numbers  
iptables \-L INPUT \--line-numbers | grep 192.168  
iptables \-L INPUT \-n \-v \--line-numbers

\# Delete iptable rule by line number  
iptables \-D INPUT 2

\# allow a specific ip to have ssh  
iptables \-I INPUT \-p tcp \-s 10.1.2.3 \--dport 22 \-j ACCEPT

\# allow access from anywhere on 80 http  
iptables \-A INPUT \-p tcp \-m tcp \--dport 80 \-j ACCEPT

\# disable access from specific port and IP Address  
/sbin/iptables \-A INPUT \-i eth0 \-p tcp \--destination-port {PORT-NUMBER-HERE} \-s {IP/SUBNET-HERE} \-j DROP  
/sbin/iptables \-A INPUT \-i eth0 \-p tcp \--destination-port 22 \-s {IP/SUBNET-HERE} \-j DROP

\#block traffic that does not come from user pia or is not on port 80 or is not destined to the local subnet or is not going out through an interface starting with tun  
Iptables \-A OUTPUT \-m owner \! \--uid-owner pia \! \-p 80 \! \-d 192.168.1.0/24 \! \-o tun+ \-j DROP

\# redirect inbound traffic on 80 to another port, e.g. 8090  
iptables \-t nat \-A PREROUTING \-i eth0 \-p tcp \--dport 80 \-j REDIRECT \--to-port 8090

\# allow internal communications between multiple services through NAT on some port, ex 443 \> 443 internally:  
iptables \-t nat \-A PREROUTING \-s 127.0.0.1 \-p tcp \--dport 443 \-j REDIRECT \--to 443

\# save iptables rules  
iptables-save \> /etc/iptables.conf  
sh \-c "iptables-save \> /etc/iptables.conf"

\# persist iptables rules  
vi /etc/rc.local  
	\# Add these lines  
\# Load iptables rules from this file   
iptables-restore \< /etc/iptables.conf  
\# drop all traffic from IP address  
iptables \-A INPUT \-s 10.1.2.3 \-j DROP  
iptables \-A OUTPUT \-d 10.1.2.3 \-j DROP

\#For a pi  
\# [http://blog.mxard.com/persistent-iptables-on-raspberry-pi-raspbian](http://blog.mxard.com/persistent-iptables-on-raspberry-pi-raspbian)  
rpi-update   
apt-get dist-upgrade   
apt-get update  
apt-get install iptables-persistent  
\# confirm config  
vim /etc/iptables/rules.v4  
reboot  
\-----------------------------------------------------------------------------------------------------------------------------------

# SWAP Usage

\# Pipe the output to "sort \-nk3" to get sorted output

SUM=0  
OVERALL=0  
for DIR in \`find /proc/ \-maxdepth 1 \-type d \-regex "^/proc/\[0-9\]+"\`  
do  
    PID=\`echo $DIR | cut \-d / \-f 3\`  
    PROGNAME=\`ps \-p $PID \-o comm \--no-headers\`  
    for SWAP in \`grep VmSwap $DIR/status 2\>/dev/null | awk '{ print $2 }'\`  
    do  
        let SUM=$SUM+$SWAP  
    done  
    if (( $SUM \> 0 )); then  
        echo "PID=$PID swapped $SUM KB ($PROGNAME)"  
    fi  
    let OVERALL=$OVERALL+$SUM  
    SUM=0  
done  
echo "Overall swap used: $OVERALL KB"

\-----------------------------------------------------------------------------------------------------------------------------------

# Monitorix

[http://ip:8080/monitorix-cgi/monitorix.cgi?mode=localhost\&graph=all\&when=1day\&color=black](http://ip:8080/monitorix-cgi/monitorix.cgi?mode=localhost&graph=all&when=1day&color=black)

wget [http://www.monitorix.org/monitorix\_3.9.0-izzy1\_all.deb](http://www.monitorix.org/monitorix_3.9.0-izzy1_all.deb)  
sudo apt-get update  
sudo apt-get install rrdtool perl libwww-perl libmailtools-perl libmime-lite-perl librrds-perl  
libdbi-perl libxml-simple-perl libhttp-server-simple-perl libconfig-general-perl  
libio-socket-ssl-perl  
sudo dpkg \-i monitorix\*.deb  
sudo apt-get \-f install  
/etc/monitorix/monitorix.conf  
http://ip:8080/monitorix-cgi/monitorix.cgi?mode=localhost\&graph=all\&when=1day\&color=black  
\------------------------------------------------------------------------------------------------------------------------------------

# Fastest mirror sources.list 

for apt debian  
[http://www.unixmen.com/find-fastest-mirror-debian-derivatives/](http://www.unixmen.com/find-fastest-mirror-debian-derivatives/)  
sudo apt-get install netselect-apt  
sudo netselect-apt  
sudo cp /etc/apt/sources.list{,.old}  
sudo cp sources.list /etc/sources.list  
sudo apt-get update  
\------------------------------------------------------------------------------------------------------------------------------------

# cron

crontab \-e  
7 23 \* \* \* /home/jdoe/etcbackup.sh

\# Minute   Hour   Day of Month       Month          Day of Week        Command      
\# (0-59)   (0-23)   (1-31)        (1-12 or Jan-Dec)  (0-6 or Sun-Sat)                  
    0           2         12                                \*                \*                        /usr/bin/find

\------------------------------------------------------------------------------------------------------------------------------------

# dd command

Write an ISO to a usb stick:  
dd if=/mnt/tmp/ubuntu.iso of=/dev/sdb bs=4M; sync  
dd bs=4M if=2017-09-07-raspbian-stretch.img of=/dev/sdX conv=fsync  
sudo dd bs=4M if=/path/to/image of=/dev/sdX oflag=sync  
Notes:  
http://www.noah.org/wiki/Dd\_-\_Destroyer\_of\_Disks  
\------------------------------------------------------------------------------------------------------------------------------------

# Apache / MySQL / PHP

\#apache  
apt-get install apache2  
\#check your ip:  
ifconfig eth0 | grep inet | awk '{ print $2 }'  
\#test apache by going to:  
curl http://192.168.1.236/

\#MySQL  
apt-get install mysql-server  
mysql\_secure\_installation

\#php  
apt-get install php5 php-pear php5-mysql  
service apache2 restart  
sudo vi /var/www/html/info.php  
\#insert these 3 lines:  
\<?php  
phpinfo();  
?\>  
\#end

\# How to create an htpasswd file  
httpasswd \-

Visit your page at [http://192.168.1.236/info.php](http://ipaddress/info.php)

## **apache logs**

 /var/log/apache(http)(apache2)  
\# Apache access log fu using grep and perl:  
egrep '^10.1.2.3 ' /var/log/apache2/access.log  
egrep '^10.1.2.3 ' /var/log/apache2/access.log | wc \-l  
egrep '^10.1.2.3.\*04\\/Jul\\/2014' /var/log/apache2/access.log  
egrep '^10.1.2.3.\*04\\/Jul\\/2014:12' /var/log/apache2/access.log

perl \-e 'while(\<\>){ if( m|(^\\d+\\.\\d+\\.\\d+\\.\\d+).\*?04/Jul/2014|){ $v{$1}++; } } foreach( keys %v ){ print "$v{$\_}\\t$\_\\n"; }' /var/log/apache2/access.log | sort \-n

perl \-e 'while(\<\>){ if( m|(^\\d+\\.\\d+\\.\\d+\\.\\d+).\*?07/Jul/2014|){ $v{$1}++; } } foreach( keys %v ){ print "$v{$\_}\\t$\_\\n"; }' /usr/local/jira/app-jira-6.0.6-standalone/logs/access\_log.2014-07-07 | sort \-n

\# apache config problem?  
sudo apache2ctl configtest

## **Curl \- Website Down** 

\# Curl command usage  
curl \-slixget https://website.org

\# remote nmap-  
nmap \-p 80 10.1.2.5

\# local  
sudo netstat \-lnp | grep :80  
sudo /sbin/iptables \-L

curl \-iL [http://www.website.com](http://www.website.com/)  
\# Parameter    Description  
\-i   Include the HTTP-header in the output. The HTTP-header includes things like server-name, date of the document, HTTP-version and more…  
\-L   If the server reports that the requested page has moved to a different location (indicated with a Location: header and a 3XX response code), this option will make curl redo the request on the new place.

curl "%{http\_code}\\n" [http://www.website.com](http://www.website.com/)  
curl "%{http\_code} %{time\_total} %{size\_download} %{content\_type}\\n" [http://www.website.com](http://www.website.com/)

telnet [www.website.com](http://www.website.com/) 80  
GET / HTTP/1.1  
host: [www.website.com](http://www.website.com/)  
Ctrl-\]  
\------------------------------------------------------------------------------------------------------------------------------------

# Nginx

vhosts file to server a redirect  
server {  
        listen 80;  
        server\_name aiglobal.marketplace-dev.insights.ai;  
        return 302 https://$host$request\_uri;  
}

\------------------------------------------------------------------------------------------------------------------------------------

# Apache httpd proxy (mod\_proxy) 

\#changes below made in httpd.conf 

LoadModule proxy\_module /usr/lib/apache2/modules/mod\_proxy.so  
LoadModule proxy\_http\_module /usr/lib/apache2/modules/mod\_proxy\_http.so  
LoadModule ssl\_module /usr/lib/apache2/modules/mod\_ssl.so

**\# HTTP Proxy**  
ProxyRequests Off  
ProxyPreserveHost On  
\<Proxy \*\>  
  \# Auth changes in 2.4 \- see http://httpd.apache.org/docs/2.4/upgrading.html\#run-time  
  Require all granted  
\</Proxy\>  
ProxyPass "/contextpath" "http://localhost:8080/contextpath"  
ProxyPassReverse "/contextpath" "http://localhost:8080/contextpath"  
\<Location /contextpath\>  
  Require all granted  
\</Location\>  
\#end

**\# Terminate SSL and forward as HTTP Proxy**  
\#httpd.conf  
Listen 443  
\<VirtualHost \*:443\>  
  SSLEngine On  
  SSLCertificateFile "/etc/apache2/ssl/server.crt"  
  SSLCertificateKeyFile "/etc/apache2/ssl/server.key"  
  ProxyPass / http://mydomain.com:8080/contextpath connectiontimeout=5 timeout=300  
  ProxyPassReverse / http://mydomain.com:8080/contextpath  
\</VirtualHost\>  
\#end

\# note \- may be needed in older version of apache, but seems to be deprecated in apache 2.4:  
SSLProxyEngine          On

**\# Apache redirect HTTP to HTTPS (mod\_reqwrite)**  
\#httpd.conf:  
LoadModule rewrite\_module libexec/apache2/mod\_rewrite.so  
\#place in http virtualhost  
RewriteEngine On  
RewriteCond %{HTTPS} off  
RewriteRule (.\*) https://%{HTTP\_HOST}%{REQUEST\_URI}  
\#end

\------------------------------------------------------------------------------------------------------------------------------------

# Apache AJP proxy (mod\_proxy\_ajp)

\#changes below made in httpd.conf   
LoadModule proxy\_module modules/mod\_proxy.so  
LoadModule proxy\_ajp\_module modules/mod\_proxy\_ajp.so

**\# HTTP AJP proxy**  
ProxyRequests Off  
ProxyPreserveHost On  
\<Proxy \*\>  
   Require all granted  
\</Proxy\>  
ProxyPass /contextpath ajp://localhost:8080/contextpath  
ProxyPassReverse /contextpath ajp://localhost:8080/contextpath  
\<Location /contextpath\>  
   Require all granted  
\</Location\>  
\#end 

**\# SSL AJP proxy**  
Listen 443  
\<VirtualHost \*:443\>  
  SSLEngine On  
  SSLCertificateFile "/etc/apache2/ssl/server.crt"  
  SSLCertificateKeyFile "/etc/apache2/ssl/server.key"  
  ProxyPass / ajp://mydomain.com:8080/contextpath connectiontimeout=5 timeout=300  
  ProxyPassReverse / ajp://mydomain.com:8080/contextpath  
\</VirtualHost\>  
\#end

\------------------------------------------------------------------------------------------------------------------------------------

# Tomcat

**\# Tomcat handling http on 8080 config:**  
\#server.xml  
\<Context path="/yourpath" docBase="../yourpath" debug="0" reloadable="true"\>  
\<Connector port="8080" connectionTimeout="20000" secure="false" scheme="http" maxThreads="200" minSpareThreads="10" enableLookups="false" acceptCount="10" debug    \="0" URIEncoding="UTF-8" protocol="org.apache.coyote.http11.Http11NioProtocol" proxyName=”mydomain.com” proxyPort=”443”/\>  
\#end

\#note \- for AJP you must use:  
protocol="AJP/1.3"

\#note \- for ssl you must use  
secure="true" scheme="https"

**\# Tomcat handling ssl on 8443 config:**  
\#server.xml  
\<Context path="/yourpath" docBase="../yourpath" debug="0" reloadable="true"\>  
\<Connector port="8443" maxHttpHeaderSize="8192"  
                   maxThreads="150" minSpareThreads="25"  
                   protocol="org.apache.coyote.http11.Http11NioProtocol"  
                   enableLookups="false" disableUploadTimeout="true"  
                   acceptCount="100" scheme="https" secure="true"  
                   clientAuth="false" sslProtocols="TLSv1,TLSv1.1,TLSv1.2"  
       sslEnabledProtocols="TLSv1,TLSv1.1,TLSv1.2" SSLEnabled="true"  
                   URIEncoding="UTF-8" keystorePass="changeit"/\>  
\#end

**\# Tomcat SSL Certs**  
\# self signed  
$JAVA\_HOME/bin/keytool \-genkeypair \-keysize 2048 \-alias tomcat \-keyalg RSA \-sigalg SHA256withRSA  
\#both pw’s must match (default is ‘changeit’)

\# adjust expiration date in days (default 90\)  
\-validity 365

\#default keystore location:  
\~/.keystore

**\# Tomcat security constraint to redirect all URLs to HTTPS**  
\#../WEB-INF/web.xml  
\# place at end before \</web-app\> tag  
\<security-constraint\>  
  \<web-resource-collection\>  
    \<web-resource-name\>Restricted URLs\</web-resource-name\>  
    \<url-pattern\>/\</url-pattern\>  
  \</web-resource-collection\>  
  \<user-data-constraint\>  
    \<transport-guarantee\>CONFIDENTIAL\</transport-guarantee\>  
  \</user-data-constraint\>  
\</security-constraint\>

## **Tomcat log parsing**

\# Extract all thread dumps from catalina.out  
awk '/^\[0-9\]{4}-\[0-9\]{2}-\[0-9\]{2} \[0-9\]{2}:\[0-9\]{2}:\[0-9\]{2}$/{n++;td=1;lastLine=$0;outFile=("thread\_dump\_" n ".txt")}; {if (td) {print $0 \>\> outFile; close(outFile)}}; /object space/{if (lastLine \~ /PSPermGen/ || lastLine \~ /Metaspace/) {td=0}}' catalina.out

\# Count the caused bys in a log  
grep "Caused by:" catalina.out | sort | uniq \-c

\# Split large file into 20mb chunk files for easier parsing  
split \-b 20m catalina.out split-output\_

\# Search a log for a string and output all the lines prior to it  
\# Great for checking out what is throwing a particular exception  
grep \-B1 "ClientAbortException.\*Broken pipe" catalina.out

\------------------------------------------------------------------------------------------------------------------------------------

# Nginx+Php-fpm

[http://www.rackspace.com/knowledge\_center/article/installing-nginx-and-php-fpm-setup-for-nginx](http://www.rackspace.com/knowledge_center/article/installing-nginx-and-php-fpm-setup-for-nginx)

Nginx Errors

403 Forbidden:  
response headers are intentionally returned in many cases such as:  
Nginx hasn’t permission to read / write to your website files  
User tries to access a directory but autoindex is set to off. Or index declaration is wrong.

\#auto-troubleshoot with sweet info  
nginx \-t    
\# error in /var/log/nginx/error.log 

* could not build optimal proxy\_headers\_hash, you should increase either proxy\_headers\_hash\_max\_size: 512 or proxy\_headers\_hash\_bucket\_size: 64; ignoring proxy\_headers\_hash\_bucket\_size

Add these 2 lines to http section of /etc/nginx/nginx.conf  
      proxy\_headers\_hash\_bucket\_size 128;  
      proxy\_headers\_hash\_max\_size 512;

\# graceful restart  
ALWAYS DO A CONFIGTEST\!\!\!\!\!   
service nginx configtest  
nginx \-s reload 

\# Count User Agents in Apache/ngnix logs  
cat log.txt |awk \-F\\"  '{print $6}' |sed 's/ .\*//g' |sort |uniq \-c |sort \-gr

\# Check for occurrence of a particular user agent in ngnix logs  
grep Jakarta start.txt |sed 's/:\[0-9\]\[0-9\] / /g' |awk '{print $4 " " $7}' |sed 's/?.\*//g' |sort |uniq \-c |sort \-nr |head \-n20

## **Nginx access log parsing**

\# Turn a file of ips into a regex to search all of them in grep \-E  
cat ips.txt | tr '\\n' '|' | sed 's/|$/\\n/' | sed 's/\\./\\\\./g'

\# Find all the HTTP referrers  
grep \-Eo '"http(s?)://\[^ \]+"' | sort | uniq

\# Find all the HTTP referrers (ignoring URL params)  
grep \-Eo 'http(s?)://\[^ \]+\\?' | sed \-E 's/^"|\\?$//' | sort | uniq

\# Find all directly accessed pages (not referrers)  
grep \-Eo " /\[^ \]+"

\# Find all directly accessed pages (ignore GET params)  
grep \-Eo " /\[^ ?\]+\\??" | sed 's/\\?$//'

\# Count User Agents in Apache/ngnix logs  
cat log.txt |awk \-F\\"  '{print $6}' |sed 's/ .\*//g' |sort |uniq \-c |sort \-gr

\# Check for occurrence of a particular user agent in ngnix logs  
grep Jakarta start.txt |sed 's/:\[0-9\]\[0-9\] / /g' |awk '{print $4 " " $7}' |sed 's/?.\*//g' |sort |uniq \-c |sort \-nr |head \-n20

\------------------------------------------------------------------------------------------------------------------------------------

# Connect to mysql or postgress database

[Bob's SQL Notes](http://drive.google.com/open?id=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX)

## **Postgres**

psql \-U username \-d myDataBase \-a \-f myInsertFile  
psql \-d mydb \-U myuser  
psql \-h myhost \-d mydb \-U myuser  
\# Add \-W if you are not prompted for a password above

\# Check total db size  
SELECT pg\_size\_pretty( pg\_database\_size('dbname') );

\# Dump a postgress db  
pg\_dump \-h db\_server \-U db\_user \-W db\_name \> db.20070725.dump

\# dump postgres db to gzip  
pg\_dump databasename | gzip \> filename.sql.gz

\# Dump a mysql db  
mysqldump \--opt \--user=root \--password my\_db \> my\_db.sql  
mysqldump \-u username \-p \-h hostname dbname \< \~/nameOfFile\_12.31.1999.sql

\# connect to postgres on AWS RDS instance  
psql \--host=your-aws-instance.blah.us-west-2.rds.amazonaws.com \--port=5432 \--username=BobMarley \--password \--dbname=BeHappy

## 

## **\# Run a .sql file in linux**

## **mysql \-u username \-p \< db.sql** **mysql \--database=wordpress \-u wordpress \-p \< db.sql**

## **MySQL stuff**

\# mysql logs  
/var/log  
/var/log/mysql  
/var/lib/mysql/error.log

\#mysql status  
sudo service mysql status  
ps \-ef | grep mysql  
sudo netstat \-lnp | grep :3306

\# mysql Metrics  
mysqladmin \-u root \-p status  
mysqladmin \-u root \-p extended-status

\# mysql Slow queries  
\# log\_slow\_queries and long\_query\_time(seconds) in my.cnf file  
\# /var/log/mysql/mysql-slow.log  
mysqladmin \-u root \-p processlist \-i 15  
mysqladmin \-u root \-p kill 2663

\# add a mysql user with all privs, access from all locations:  
GRANT ALL PRIVILEGES ON \*.\* TO 'SOMEUSER'@'%' IDENTIFIED BY 'SOMEPASSWORD' WITH GRANT OPTION;  
FLUSH PRIVILEGES;

## **Postgres Stuff**

\# Output a PSQL result set to a CSV file  
postgres=\>\\o query.csv  
postgres=\>SELECT \* FROM users;  
postgres=\>\\q

\# show largest postgres db’s   
SELECT pg\_database.datname, pg\_database\_size(pg\_database.datname), pg\_size\_pretty(pg\_database\_size(pg\_database.datname)) FROM pg\_database ORDER BY pg\_database\_size DESC;

\# postgres status  
sudo service postgresql-8.4 status  
ps \-ef | grep postgres  
sudo netstat \-lnp | grep :5432

\# postgres logs  
/var/log  
/var/log/postgresql/postgresql-8.4-main.log

\# Enable addition tracking in postgres postgresql.conf file  
track\_activities  
track\_counts  
su \- postgres  
select \* from pg\_stat\_activity;  
select \* from pg\_stat\_activity where procid=4689;  
select \* from pg\_stat\_database;  
select \* from pg\_stat\_all\_tables;  
select \* from pg\_stat\_user\_tables;  
select \* from pg\_stat\_sys\_tables;

\# postgres Slow queries  
log\_min\_duration\_statement \= 100 (millisecods)

\#defaults after installing 9.5  
  config /etc/postgresql/9.5/main  
  data   /var/lib/postgresql/9.5/main  
  locale en\_US.UTF-8  
  socket /var/run/postgresql  
  port   5433

\------------------------------------------------------------------------------------------------------------------------------------

# Recreate /dev/null and /dev/zero

rm /dev/null ; mknod \-m 0666 /dev/null c 1 3 ;  
rm /dev/zero ; mknod \-m 0666 /dev/zero c 1 5 ;

\------------------------------------------------------------------------------------------------------------------------------------

# SWAP 

**Debian:**  
mkdir /swap  
dd if=/dev/zero of=/swap/swapfile bs=1M count=2048  
chmod 0600 /swap/swapfile  
ls \-al /swap/swapfile  
mkswap /swap/swapfile  
swapon /swap/swapfile

**Persistent:**  
vi /etc/fstab  
/swap/swapfile none swap sw 0 0

**One-liner to install and use 2gb swap on Ubuntu**  
sudo fallocate \-l 2G /swapfile ; sudo chmod 600 /swapfile ; sudo mkswap /swapfile ; sudo swapon /swapfile ; sudo cp /etc/fstab /etc/fstab.bak ; echo '/swapfile none swap sw 0 0' | sudo tee \-a /etc/fstab

\------------------------------------------------------------------------------------------------------------------------------------

# Server Status One-liner  

echo ""; echo "Server Status One-liner"; echo ""; echo "Storage: "; df \-h | sed \-n '2,2p' | awk '{print "Disk:",$3"/"$2,$5}'; df \-i | sed \-n '2,2p' | awk '{print "Inodes:",$3"/"$2,$5}'; echo ""; echo "Load Average: "; cat /proc/loadavg; echo \-ne "Thread Count: "; cat /proc/cpuinfo | grep processor | wc \-l; echo ""; echo "Usage: "; mpstat | tail \-2; echo ""; echo "Memory: "; free \-m; echo ""; echo "Vmstat: "; vmstat; echo ""; echo "Services: ";ps cax | grep mysqld \> /dev/null; if \[ $? \-eq 0 \]; then echo "mysql is running"; else echo "mysql is not running"; fi; ps cax | grep httpd \> /dev/null; if \[ $? \-eq 0 \]; then echo "httpd is running"; else echo "httpd is not running"; fi; ps cax | grep exim \> /dev/null; if \[ $? \-eq 0 \]; then echo "exim is running"; else echo "exim is not running"; fi; ps cax | grep named \> /dev/null; if \[ $? \-eq 0 \]; then echo "named is running"; else echo "named is not running(Are they root?)"; fi; ps cax | grep pure-ftpd \> /dev/null; if \[ $? \-eq 0 \]; then echo "ftpd is running"; else echo "ftpd is not running"; fi; ps cax | grep courier \> /dev/null; if \[ $? \-eq 0 \]; then echo "courier is running"; else echo "courier is not running"; fi; netstat \-tunap | grep \-v 0.0.0.0 | awk '/.\*\[0-9\]+.\[0-9\]+.\[0-9\]+.\[0-9\].\*/{gsub(/::ffff:/,"",$0);print $4"\\t" $5 "\\t" $6"\\t" $7}' | awk \-F"/" '{print $1"\\t"$2}' \> netstat.log; echo ""; echo "Connections:";echo "Number of connections to each port:";cat netstat.log | awk {'print $1'} | cut \-d: \-f 2 | sort | uniq \-c | sort \-nk 1;echo;echo "Number of connections from each IP:";cat netstat.log | awk {'print $2'} | cut \-d: \-f 1 | sort | uniq \-c | sort \-nk 1;echo;echo "Number of instances of a particular IP connecting to particular port with connection states:";cat netstat.log | awk \-F":" {'print $2 "\\t" $3'} | awk {'print $1 "\\t" $2 "\\t" $4 "\\t" $6'} | sort | uniq \-c | sort \-nk 1;echo;echo "SYN\_RECV connections:";cat netstat.log | awk \-F":" {'print $2 "\\t" $3'} | awk {'print $1 "\\t" $2 "\\t" $4 "\\t" $6'} | sort | uniq \-c | sort \-nk 1 | grep SYN\_RECV; echo "Most CPU Intensive:"; ps auxf | sort \-nr \-k 3 | head \-2;echo; echo "Most Memory Intensive:"; ps auxf | sort \-nr \-k 4 | head \-2;

\--------------------------------------------------------------------------------------------------------------------------------

#  GRUB:

\# boot loader for linux, currently at version grub2.  
\# Change the boot wait timeout:  
sudo cp /etc/default/grub /root/grub.bk  
sudo vi /etc/default/grub  
GRUB\_TIMEOUT=10  
\# After Changing the timeout, run-  
sudo update-grub  
\--------------------------------------------------------------------------------------------------------------------------------

# JAVA

## **Install java jdk on debian**

\#download  
wget \--header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u152-b16/aa0333dd3019491ca4f6ddbe78cdb6d0/jdk-8u152-linux-x64.tar.gz

\#create dir  
mkdir /opt/jdk

\# extract to above dir  
tar \-xzvf /home/jdoe/jdk-8u74-linux-x64.tar.gz \-C /opt/jdk

\# set java defaults  
update-alternatives \--install /usr/bin/java java /opt/jdk/jdk1.8.0\_131/bin/java 100  
update-alternatives \--install /usr/bin/javac javac /opt/jdk/jdk1.8.0\_131/bin/javac 100

\#confirm defaults  
update-alternatives \--display java  
update-alternatives \--display javac

\#reboot

\#confirm version  
java \-version  
\#should see the latest version you installed

\#create java home dir  
sudo vi /etc/profile

\#add these lines:  
export JAVA\_HOME=/opt/jdk/jdk1.8.0\_74/  
export PATH=$JAVA\_HOME/bin:$PATH

\#reload bash   
\#test java home dir  
echo $JAVA\_HOME  
\#should see your java dir you entered in /etc/profile above

**\# Examples:**  
root@WimpyDragon:\~\# java \-version  
java version "1.8.0\_74"  
Java(TM) SE Runtime Environment (build 1.8.0\_74-b02)  
Java HotSpot(TM) 64-Bit Server VM (build 25.74-b02, mixed mode)

root@WimpyDragon:\~\# echo $JAVA\_HOME  
/opt/jdk/jdk1.8.0\_74/

## **Install JDK on Ubuntu**

\#java quick install ubuntu  
sudo add-apt-repository ppa:webupd8team/java  
sudo apt-get install oracle-java8-installer

## **Install Java on RHEL**

Great article: [https://tecadmin.net/install-java-8-on-centos-rhel-and-fedora/](https://tecadmin.net/install-java-8-on-centos-rhel-and-fedora/) 

## **Set $JAVA\_HOME on Ubuntu quickly**

\#add these to .bash\_profile  
\#comment out the one you don’t use  
\#java jre home  
JAVA\_HOME=$(readlink \-f /usr/bin/java | sed "s:bin/java::")

\#java jdk home  
JAVA\_HOME=$(readlink \-f /usr/bin/javac | sed "s:/bin/javac::")

## **HEAP**

/java/bin/jmap \-dump:format=b,file=/tmp/api-views-heapdump.bin 12246  
[https://techblog.example.com/2013/03/so-you-want-your-jvms-heap/](https://techblog.example.com/2013/03/so-you-want-your-jvms-heap/)  
   
\# app server logs  
\# look at java class  
\# write operations \- lock file  
\# gc logs \- full collection \- how long it took when frozen  
\# verbose gc time/date stamps location

grep 'Full GC' gc.log | awk '{print $1, $16}'

\# gc tuning \- concurrent, add MEM, add vCPU  
\# thread dumps \- RUNNABLE threads what jvm is doing  
java \-jar \-Xmx2048m tda nameofdump

## **Scripted auto-install of java**

\#\!/bin/bash  
set \-e \-x  
export DEBIAN\_FRONTEND=noninteractive  
apt-get update && apt-get upgrade \-y  
apt-get install \-y software-properties-common   
add-apt-repository ppa:webupd8team/java && apt-get update   
echo "oracle-java8-installer shared/accepted-oracle-license-v1-1 select true" | debconf-set-selections   
echo "oracle-java8-installer shared/accepted-oracle-license-v1-1 seen true" | debconf-set-selections   
apt-get install \-y oracle-java8-installer

## 

## **One-liner auto install of java**

sudo \-i  
apt-get install \-y software-properties-common && \\  
add-apt-repository \-y ppa:webupd8team/java && apt-get update &&\\  
echo "oracle-java8-installer shared/accepted-oracle-license-v1-1 select true" | debconf-set-selections && \\  
echo "oracle-java8-installer shared/accepted-oracle-license-v1-1 seen true" | debconf-set-selections && \\  
apt-get install \-y oracle-java8-installer && \\  
echo JAVA\_HOME=/usr/lib/jvm/java-8-oracle \>\> /etc/environment && \\  
echo export JAVA\_HOME \>\> /etc/environment && \\  
. /etc/environment

\--------------------------------------------------------------------------------------------------------------------------------

# sysctl.conf 

**\-n:** Use this option to disable printing of the key name when printing values.  
**\-e:** Use this option to ignore errors about unknown keys.  
**\-w:** Use this option when you want to change a **sysctl** setting.  
**\-p:** Load **sysctl** settings from the file specified or **/etc/sysctl.conf** if none was given.  
**\-a:** Display all values currently available.  
**\-A:** Display all values currently available in table form.

To modify an existing single value, you can use sysctl \-w setting=value, for example sysctl \-w net.ipv4.tcp\_keepalive\_time=300.

sysctl.conf parameters for better overall performance  
\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\# SYSCTL.CONF FILE \#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#  
\# File below is a merge of existing parameters with recommended parameters to provide   
**\# For better overall performance in the environment**

**\#\#\#\# NETWORK SECURITY HARDENING \#\#\#\#**  
\# Routing information  
\# Disable response to broadcasts.  
\# You don't want yourself becoming a Smurf amplifier.  
net.ipv4.icmp\_echo\_ignore\_broadcasts \= 1

* This will ignore all ICMP ECHO and TIMESTAMP requests sent to the nic via broadcast or multicast (rather than unicast). Configuring this will help prevent your system from being used to amplify a DDOS attack.

\# enable route verification on all interfaces  
net.ipv4.conf.all.rp\_filter \= 1  
net.ipv4.icmp\_echo\_ignore\_all \= 0  
net.ipv4.icmp\_ignore\_bogus\_error\_responses \= 1  
net.ipv4.conf.all.log\_martians \= 1  
net.ipv4.tcp\_fin\_timeout \= 30  
net.core.netdev\_max\_backlog \= 3000

* **net.ipv4.conf.all.rp\_filter** – enables/disables source validation be reversed path as specified in RFC1812 for all interfaces.  
* **net.ipv4.icmp\_echo\_ignore\_all** – set to 0 allows the nic to respond to *unicast* ICMP ECHO and TIMESTAMP requests.  
* **net.ipv4.icmp\_ignore\_bogus\_error\_responses** – Sometimes you will come across routers that send out invalid responses to broadcast frames. This is a violation of RFC 1122, "Requirements for Internet Hosts – Communication Layers". As a result, these events are logged by the kernel. To avoid filling up your logfile with unnecessary clutter, you can tell the kernel not to issue these warnings.  
* **net.ipv4.conf.all.log\_martians** – Packets that have source addresses with no known route are referred to as "martians". For example, if you have two different subnets plugged into the same hub, the routers on each end will see each other as martians. To log such packets to the kernel log, which should never show up in the first place, enable this (1).  
* **net.ipv4.tcp\_fin\_timeout** – This decreases the TIME\_WAIT timeout in seconds (30 seconds is recommended). Setting this will help your server recycle sockets more quickly.  
* **net.core.netdev\_max\_backlog** – This sets the maximum number of packets, queued on the INPUT side, when the interface receives packets faster than the kernel can process them.

\# Controls IP packet forwarding  
net.ipv4.ip\_forward \= 0  
\# Do not accept source routing  
net.ipv4.conf.default.accept\_source\_route \= 0

* **net.ipv4.ip\_forward** – Setting this to 0 disables IP forwarding on this server, which is a good idea if it is not a gateway or vpn server. If the setting is 1 then the Linux system will start forwarding IP packets even if they are not destined to any of its own network interfaces.  
* **net.ipv4.conf.default.accept\_source\_route** – The accept\_source\_route option causes network interfaces to accept packets with the Strict Source Route (SSR) or Loose Source Routing (LSR) option set. This ties in with the above setting to disable IP forwarding. This allows the source (the sending host) to specify the route, loosely or strictly, ignoring the routing tables of some or all of the routers. It can allow a user to redirect network traffic for malicious purposes. Therefore, source-based routing should be disabled.

\# Disable netfilter on bridges.  
net.bridge.bridge-nf-call-ip6tables \= 0  
net.bridge.bridge-nf-call-iptables \= 0  
net.bridge.bridge-nf-call-arptables \= 0

* Most virtual guests configured using libvirt connect to the network via a Linux host bridge; the behavior of the bridge changes depending on the setting of the above 3 "tunables" recognized by the kernel bridge module. These control whether or not packets traversing the bridge are sent to iptables for processing. In the case of using bridges to connect virtual machines to the network, generally such processing is not desired, as it results in guest traffic being blocked due to host iptables rules that only account for the host itself, and not for the guests.

\# avoid deleting secondary IPs on deleting the primary IP  
net.ipv4.conf.default.promote\_secondaries \= 1  
net.ipv4.conf.all.promote\_secondaries \= 1  
kernel.msgmni=131072  
kernel.msgmnb=4194304  
kernel.msgmax=4194304  
kernel.shmmax=1234567890123456789  
kernel.shmall=1234567890123456789

* **net.ipv4.conf.default.promote\_secondaries** and **net.ipv4.conf.all.promote\_secondaries** – If you use more than one IP address in the same CIDR network on your Linux box, you may have noticed, that some addresses are labeled as ‘secondary’. Outgoing traffic is routed through the primary IP address and the secondaries are ready to receive traffic. But if you remove the primary IP address, all secondary addresses are purged by default as well. This is to make sure that if the primary IP address is unplumbed, the secondary IP address for a network is promoted to the primary address.  
* **kernel.msgmni** – Specifies the maximum number of system-wide System IPC message queue identifiers (one per queue). The default is 16\.  
* **kernel.msgmnb** – Specifies the maximum allowable total combined size of all messages queued in a single System V IPC message queue at one time, in bytes. The default is 16384\.  
* **kernel.msgmax** – Specifies the maximum allowable size of any single message in a System V IPC message queue, in bytes. msgmax must be no larger than msgmnb (the size of the queue).  
* **kernel.shmmax** – Maximum size of a shared memory segment on a Linux system.  
* **kernel.shmall** – Maximum allocation of shared memory pages on a system.

\# TCP SYN Flood Protection  
net.ipv4.tcp\_syncookies \= 1  
net.ipv4.tcp\_max\_syn\_backlog \= 65535  
net.ipv4.tcp\_max\_orphans \= 65535  
net.ipv4.tcp\_synack\_retries \= 3  
net.core.somaxconn \= 1024

* **Hardening your TCP/IP Stack Against SYN Floods** – A SYN Attack is a denial of service DoS attack that consumes all the resources on your machine, forcing you to reboot.  
* Denial of service (DoS) attacks launch via SYN floods can be very problematic for servers that are not properly configured to handle them. Proper firewall filtering policies are certainly usually the first line of defense, however the Linux kernel can also be hardened against these types of attacks. This type of hardening is useful for SYN floods that attempt to overload a particular service with requests (such as http) as opposed to one that intends to saturate the server's network connection, for which a firewall is needed to guard against.  
* The Linux kernel allows you to directly change the various parameters needed to mitigate against SYN flood attacks.  
  * TCP connections are established using a 3-way handshake. Attackers desiring to start a SYN flood will spoof their IP address in the header of the SYN packet sent to the server, so that when the server responds with it's SYN-ACK packet, it never reaches the destination (from which an ACK would be sent and the connection established). The server leaves these unestablished connections in a queue for a pre-determined period of time after which they are simply discarded. However if enough of these “fake” connections gum up the queue (backlog), it can prevent new, legitimate requests from being handled.  
  * Linux has a relatively small backlog queue by default, and keeps half-open requests in the queue for up to 3 minutes\! Thus the need for tweaking the way the Linux kernel handles these requests is born.  
* Configuring the above setting values as stated will prevent a TCP SYN flood on your server.

\# Protect Against TCP Time-Wait  
net.ipv4.tcp\_rfc1337 \= 1

* This enables a fix for 'time-wait assassination hazards in tcp', described in RFC 1337\. If enabled, this causes the kernel to drop RST packets for sockets in the time-wait state.

\# Increase the tcp-time-wait buckets pool size to prevent simple DOS attacks  
net.ipv4.tcp\_max\_tw\_buckets \= 1440000  
net.ipv4.tcp\_tw\_recycle \= 1  
net.ipv4.tcp\_tw\_reuse \= 1

* **net.ipv4.tcp\_max\_tw\_buckets** – This is the maximum number of timewait sockets held by the system simultaneously. If this number is exceeded, a time-wait socket is immediately destroyed and a warning is printed. This limit exists *only* to prevent simple DoS attacks. You must not lower the limit, but rather increase it (after increasing installed memory), if your network conditions require more than the default value.  
* **net.ipv4.tcp\_tw\_recycle** – Enable fast recycling of TIME-WAIT sockets. Default value is 1\. This helps to reduce the chance of TCP port exhaustion.  
  * However in some instances can present a problem when used in conjuntion with NAT as it won’t handle connections from two different computers behind the same NAT device, which is a problem hard to detect.  
* **net.ipv4.tcp\_tw\_reuse** – Allow the system to reuse TIME-WAIT sockets for new connections when it is safe from a protocol viewpoint. Default value is 0\.

**\#\#\#\# GENERAL SYSTEM AND MEMORY  MANAGEMENT \#\#\#\#**  
\# Controls the System Request debugging functionality of the kernel  
kernel.sysrq \= 0

* This disables the [Magic Sysrq Key](https://en.wikipedia.org/wiki/Magic_SysRq_key) on your system. If enabled, certain key combos can be used to reboot the system.

\# Controls whether core dumps will append the PID to the core filename.  
\# Useful for debugging multi-threaded applications.  
kernel.core\_uses\_pid \= 1

* This setting forces core dump files to include a PID, which is handy for debugging multi-threaded applications. If this option is disabled, the PID will not be appended to the core dump filename, making debugging a little more difficult.

\# Inotify watches  
fs.inotify.max\_user\_watches \= 65536  
fs.mqueue.msg\_max \= 16384  
fs.mqueue.queues\_max \= 4096

* **fs.inotify.max\_user\_watches** – Listen uses inotify by default on Linux to monitor directories for changes. It's not uncommon to encounter a system limit on the number of files you can monitor. When this limit is not enough to monitor all files inside a directory, the limit must be increased for Listen to work properly.  
* **fs.mqueue.msg\_max** – This file can be used to view and change the ceiling value for the maximum number of messages in a queue. This value acts as a ceiling on the attr-\>mq\_maxmsg argument given to mq\_open(3). The default value for msg\_max is 10\. The minimum value is 1 (10 in kernels before 2.6.28). The upper limit is HARD\_MSGMAX. The msg\_max limit is ignored for privileged processes (CAP\_SYS\_RESOURCE), but the HARD\_MSGMAX ceiling is nevertheless imposed.  
* **fs.mqueue.queues\_max** – This file can be used to view and change the system-wide limit on the number of message queues that can be created. The default value for queues\_max is 256\. No ceiling is imposed on the queues\_max limit; privileged processes (CAP\_SYS\_RESOURCE) can exceed the limit.

\# Do less swapping (reduces disk thrashing) and increases performance  
vm.swappiness \= 10  
vm.dirty\_ratio \= 60  
vm.dirty\_background\_ratio \= 2

* **vm.swappiness** – This is a parameter that helps guide the kernel in making decisions about memory. This helps the kernel decide what's important and needs to be kept in RAM vs what can be sent to the swap file when memory is scarce.  
  * “vm” means “virtual memory,” which refers to the addressing scheme the Linux kernel uses to handle memory (this doesn't refer to memory allocated to a hypervisor).  
  * By setting it to 0 the kernel will only swap to protect against an out-of-memory condition. The default is 60 which means that some swapping will occur.  
  * Lowering this setting to 10 means the kernel will be less aggressive in swapping and thus more RAM will be used, which can lead to increased performance of applications (when sufficient memory exists on the system).  
* **vm.dirty\_ratio** – This is the absolute maximum amount of system memory that can be filled with dirty pages before everything must get committed to disk.  
  * When the system gets to this point all new I/O is blocked until dirty pages have been written to disk.  
  * This is often the source of long I/O pauses, but is a safeguard against too much data being cached unsafely in memory.  
* **vm.dirty\_background\_ratio** – This is the percentage of system memory that can be filled with “dirty” pages (memory pages that still need to be written to disk) before the pdflush/flush/kdmflush background processes kick in to write it to disk.

**\#\#\#\# GENERAL NETWORK PERFORMANCE TUNING \#\#\#\#**  
\# Increase size of file handles and inode cache  
fs.file-max \= 2097152

* The **fs.file-max** value sets the maximum number of file-handles that the Linux kernel will allocate. What this means is the maximum File Descriptors (FD) are enforced on a kernel level, which cannot be surpassed by all processes without increasing.  
* This differs from the **ulimit** max, which is enforced on a process level and can be less than **fs.file-max**.  
* We generally tune this to increase the max number of allowed open files. The default is 4096 which is much too low in many production environments.  
* There is no performance impact risk by increasing **fs.file-max**  
* The per-process configuration often needs tuned for serving a particular daemon, for example a database or web server.  
  * If you remove the limit entirely, a daemon could potentially exhaust all available system resources, which would render the system in a state of failure.

\# Decrease the time default value for tcp\_fin\_timeout connection  
net.ipv4.tcp\_fin\_timeout \= 15

* With highly utilized database and web servers, you typically open and close connections very often and rapidly. This means we have very short-lived connections to the server. This can lead to a situation in which the maximum number of TCP ports are exhausted.  
* When a TCP connection closes a port, the port can't be immediately reused until the O/S has waited for the duration of the configured TIME\_WAIT interval.  
* The reason this window is kept open for a period of time is in case of packets arriving late (or out of order), or any that need to be retransmitted after the connection has been closed. This allows delayed packets to be handled appropriately.  
* You can mitigate the port exhaustion risk by decreasing the **net.ipv4.tcp\_fin\_timeout** to 15 seconds, as in the above example.

\# Decrease the time default value for connections to keep alive  
net.ipv4.tcp\_keepalive\_time \= 300  
net.ipv4.tcp\_keepalive\_probes \= 5  
net.ipv4.tcp\_keepalive\_intvl \= 15

* What this means is that the keepalive process waits for 5 minutes (300 secs) for socket activity before sending the first keepalive probe, and then resend it every 5 seconds. If no ACK response is received for 15 consecutive times, the connection is marked as broken.  
* **net.ipv4.tcp\_keepalive\_time** – This is the interval between the last *data* packet sent (ACKs are not considered data) and the first keepalive probe.  
  * After the connection is marked to need keepalive, this counter is not used.  
* **net.ipv4.tcp\_keepalive\_probes** – The number of unacknowledged probes to send before considering the connection dead and notifying the application layer.  
* **net.ipv4.tcp\_keepalive\_intvl** – The interval between subsequential keepalive probes, regardless of what the connection has exchanged in the meantime.

**\#\#\#\# FURTHER NETWORK PERFORMANCE  TUNING \#\#\#\#**  
\# Default Socket Receive Buffer  
net.core.rmem\_default \= 31457280

* This controls the *default* size of receive buffers used by all sockets. This should not exceed the **rmem\_max** value.

\# Maximum Socket Receive Buffer  
net.core.rmem\_max \= 12582912

* This controls the maximum socket receive buffer size of all sockets.

\# Default Socket Send Buffer  
net.core.wmem\_default \= 31457280

* This controls the default setting in bytes of the socket send buffer. This value should not exceed the configured **wmem\_max** value.

\# Maximum Socket Send Buffer  
net.core.wmem\_max \= 12582912

* This controls the maximum socket send buffer size of all sockets

\# Increase number of incoming connections  
net.core.somaxconn \= 65536

* This controls the maximum number of backlogged sockets. The default is 128\. This in conjunction with the other options above will help prevent Connection Refused errors on the client side due to a full backlog of connections. Instead, the user would see increased latency (in regards to a full backlog; not under normal circumstances) rather than a failure to load the page.

\# Increase number of incoming connections backlog  
net.core.netdev\_max\_backlog \= 65536

* This increases the number of incoming connections which can be backlogged (in case of high traffic load on the server).

\# Increase the maximum amount of option memory buffers  
net.core.optmem\_max \= 25165824

* This increases the maximum amount of option memory buffers.

\# Increase the maximum total buffer-space allocatable  
\# This is measured in units of pages (4096 bytes)  
net.ipv4.tcp\_mem \= 65536 131072 262144  
net.ipv4.udp\_mem \= 65536 131072 262144

* **net.ipv4.tcp\_mem** – The tcp\_mem variable defines how the TCP stack should behave when it comes to memory usage.  
  * The first value specified in the tcp\_mem variable tells the kernel the low threshold. Below this point, the TCP stack will not bother to put any pressure on the memory usage by different TCP sockets.  
  * The second value tells the kernel at which point to start pressuring memory usage down.  
  * The final value tells the kernel how many memory pages it may use (maximum). If this value is reached, TCP streams and packets start getting dropped until it reaches a lower memory usage again. This value includes all TCP sockets currently in use.  
* **net.ipv4.udp\_mem** – As with the tcp\_mem parameter, this is a vector of three integers governing the number of pages allowed for queueing by all UDP sockets. The same principles apply to this as with tcp\_mem.

\# Increase the read-buffer space allocatable  
net.ipv4.tcp\_rmem \= 8192 87380 16777216  
net.ipv4.udp\_rmem\_min \= 16384

* **net.ipv4.tcp\_wmem** – Similar to the above tcp\_mem, but is solely the read-buffer rather than total buffer.  
  * The first value tells the kernel the minimum receive buffer for each TCP connection, and this buffer is always allocated to a TCP socket, even under high pressure on the system.  
  * The second value specified tells the kernel the default receive buffer allocated for each TCP socket. This value overrides the /proc/sys/net/core/rmem\_default value used by other protocols.  
  * The third and last value specified in this variable specifies the maximum receive buffer that can be allocated for a TCP socket.  
* **net.ipv4.udp\_rmem\_min** – Minimal size, in bytes, of receive buffers used by UDP sockets in moderation. Each UDP socket is able to use the size for receiving data, even if total pages of UDP sockets exceed udp\_mem pressure.

\# Increase the write-buffer-space allocatable  
net.ipv4.tcp\_wmem \= 8192 65536 16777216  
net.ipv4.udp\_wmem\_min \= 16384

* **net.ipv4.tcp\_wmem** – This variable takes 3 different values which holds information on how much TCP sendbuffer memory space each TCP socket has to use. Every TCP socket has this much buffer space to use before the buffer is filled up. Each of the three values is used under different conditions.  
  * The first value in this variable tells the minimum send buffer space available for a single TCP socket.TCPsend  
  * The second value in the variable tells us the default buffer space allowed for a single TCP socket to use.  
  * The third value tells the kernel the maximum TCP buffer space.  
* **net.ipv4.udp\_wmem\_min** – This changes (increases) the amount of write-buffer-space available to the UDP buffer.

\#\#\#\#\#\#\#\#\# end of sysctl.conf \#\#\#\#\#\#\#\#\#

\--------------------------------------------------------------------------------------------------------------------------------

# Large file split

Split large files  
\# split \--bytes=600m filename filename.

To combine the split files back to the original file:  
\# cat filename.\* \> filename  
\--------------------------------------------------------------------------------------------------------------------------------

# Sed command

\# operates line by line  
\# does not edit source file by default for safety  
\# output to a new file if you need to save results  
[http://sed.sourceforge.net/sed1line.txt](http://sed.sourceforge.net/sed1line.txt) 

\# print only lines which match regular expression (emulates "grep")  
sed \-n '/regexp/p'           \# method 1  
sed '/regexp/\!d'             \# method 2

\# print 1 line of context before and after regexp, with line number  
\# indicating where the regexp occurred (similar to "grep \-A1 \-B1")  
sed \-n \-e '/regexp/{=;x;1\!p;g;$\!N;p;D;}' \-e h

\# grep for AAA and BBB and CCC (in any order)  
sed '/AAA/\!d; /BBB/\!d; /CCC/\!d'

\# grep for AAA and BBB and CCC (in that order)  
sed '/AAA.\*BBB.\*CCC/\!d'

\# grep for AAA or BBB or CCC (emulates "egrep")  
sed \-e '/AAA/b' \-e '/BBB/b' \-e '/CCC/b' \-e d    \# most seds  
gsed '/AAA\\|BBB\\|CCC/\!d'                        \# GNU sed only

\# create backup prior to editing  
sed \-i.bak \<commands\> filename

\# replace first instance of xyz on each line with abc  
sed ‘s/oldtext/newtext/’ filename

\# add i after last slash to ignore case  
sed ‘s/oldTEXT/newtext/i’ filename

\# replace second instance of xyz on each line with abc  
sed ‘s/oldtext/newtext/2’ filename

\# replace every instance of xyz in a file with abc  
sed ‘s/oldtext/newtext/g’ filename

\# multiple commands  
sed \-e ‘firstcommand’ \-e ‘secondcommand’ filename  
sed ‘firstcommand’;’secondcommand’ filename

OPTIMIZING FOR SPEED: If execution speed needs to be increased (due to  
large input files or slow processors or hard disks), substitution will  
be executed more quickly if the "find" expression is specified before  
giving the "s/.../.../" instruction. Thus:

   sed 's/foo/bar/g' filename         \# standard replace command  
   sed '/foo/ s/foo/bar/g' filename   \# executes more quickly  
   sed '/foo/ s//bar/g' filename      \# shorthand sed syntax

\--------------------------------------------------------------------------------------------------------------------------------

# AWK command

[http://www.pement.org/awk/awk1line.txt](http://www.pement.org/awk/awk1line.txt)

\# print and sort the login names of all users  
awk \-F ":" '{print $1 | "sort" }' /etc/passwd

\# substitute "foo" with "bar" ONLY for lines which contain "baz"  
awk '/baz/{gsub(/foo/, "bar")}; 1'

\# matching a field against a regular expression  
awk '$7  \~ /^\[a-f\]/'    \# print line if field \#7 matches regex  
awk '$7 \!\~ /^\[a-f\]/'    \# print line if field \#7 does NOT match regex

\# grep for AAA and BBB and CCC (in any order on the same line)  
awk '/AAA/ && /BBB/ && /CCC/'

\# grep for AAA and BBB and CCC (in that order)  
awk '/AAA.\*BBB.\*CCC/'

\--------------------------------------------------------------------------------------------------------------------------------

# Cut command

\#Remove the first 4 characters from the line  
cut \-c 5-

\# display characters 1-15  
cut \-c 1-15  
\--------------------------------------------------------------------------------------------------------------------------------

# at command

\# manually schedule a command once without crontab  
 at now \+ 30 minutes  
\> sudo pm-suspend  
\# press ctrl+d to leave the prompt and save

\# Make the computer sleep in 30 minutes, by the alias sss  
alias sss='echo "sudo pm-suspend" \> /tmp/suspend.sh && chmod \+x /tmp/suspend.sh && at now \+ 30 minutes \-f /tmp/suspend.sh'  
\--------------------------------------------------------------------------------------------------------------------------------

# Linux stack 

![][image1]  
\--------------------------------------------------------------------------------------------------------------------------------

## IO WAIT

\# display io every 2 seconds  
sudo iostat 2

\# iotop (2.6.20 kernel and up only)  
Usually will need to be installed. Red Hat:  
sudo yum install iotop  
sudo iotop

Gives you information on exactly what processes are using I/O on the disk.   
   
detective sysstat  
/etc/default/sysstat ENABLED="true"  
/var/log/sysstat or /var/log/sa  
/etc/cron.d/sysstat

sar  
sar \-r (RAM)  
sar \-b (Disk)  
sar \-A (ALL)  
sar \-s 20:00:00 \-e 20:30:00 (specific times)  
sar \-f /var/log/sysstat/sa06 (different day)

\--------------------------------------------------------------------------------------------------------------------------------

# LDAP

\#search for user with ldapsearch  
ldapsearch \-x \-h localhost \-p 389 \-D uid=admin,ou=system \-b o=sevenSeas \-w secret '(objectClass=person)' uid

\--------------------------------------------------------------------------------------------------------------------------------

# Symbolic link

Hint: use the full path for the target and symbolic as in the example  
ln \-s /path/to/file /other/path/to/symlink

\--------------------------------------------------------------------------------------------------------------------------------

# Environment variables

One liner to show environment variables  
PS4='+$BASH\_SOURCE\> ' BASH\_XTRACEFD=7 bash \-xl 7\>&2

\--------------------------------------------------------------------------------------------------------------------------------

# etc config files

| File | Standard usage |
| ----- | ----- |
| /etc/environment | specifically meant for environment variables |
| /etc/env.d/\* | environment variables, split in multiple files |
| /etc/profile | all types of initialisation scripts |
| /etc/profile.d/\* | initialization scripts |
| /etc/bashrc | meant for functions and aliases |
| /etc/cron.d/\* | Cron files (not crontab) |

\--------------------------------------------------------------------------------------------------------------------------------

# Unattended security updates ubuntu

sudo dpkg-reconfigure \-plow unattended-upgrades

[http://www.howtogeek.com/204796/how-to-enable-automatic-security-updates-on-ubuntu-server/](http://www.howtogeek.com/204796/how-to-enable-automatic-security-updates-on-ubuntu-server/)

\--------------------------------------------------------------------------------------------------------------------------------

[http://www.nico.schottelius.org/blog/reboot-linux-if-task-blocked-for-more-than-n-seconds/](http://www.nico.schottelius.org/blog/reboot-linux-if-task-blocked-for-more-than-n-seconds/)

\--------------------------------------------------------------------------------------------------------------------------------

# Measuring response time of web applications

for i in $(seq 10); do curl \-Ik \-w "HTTPCode=%{http\_code} TotalTime=%{time\_total}\\n" http://some-url/ \-so /dev/null; sleep 5; done

\-- change the 10 in $(seq 10\) to the number of times you want the script to run  
\-- change the 5 in sleep 5 to how long it should wait before running again (in seconds)

\--------------------------------------------------------------------------------------------------------------------------------

asd \- Anything Sync Daemon \- Tool for syncing a directory to ramdisk directory

\--------------------------------------------------------------------------------------------------------------------------------

# Image and Media Management 

exiv2 \- Image metadata library and tools \- Rename images based on EXIF date/timestamp  
Jhead \- Renames images basd on EXIF date/timestamp   
\# This will rename files matched by \*.jpg in the format YYYYMMDD-HHMMSS  
jhead \-n%Y%m%d-%H%M%S \*.jpg

\--------------------------------------------------------------------------------------------------------------------------------

# OpenBox / LXDE 

LXDE is a lightweight desktop environment  
OpenBox is a lightweight window manager

\# How to add selectable print screen shortcut for F18  
\# XF86Launch9 is F18 for some reason, scrot saves screenshots in \~ by default  
\# [https://help.ubuntu.com/community/Scrot](https://help.ubuntu.com/community/Scrot)

\# Add the following 5 lines before the \</keyboard\> line in  \~/.config/openbox/lxde-rc.xml  
\# May need to manually replace the single quotes in the scrot line  
    \<keybind key="XF86Launch9"\>  
      \<action name="Execute"\>  
          \<command\>scrot \-s ‘%Y-%m-%d\_$wx$h.png’ \-e ‘mv $f \~/Pictures/screenshots/’\</command\>  
      \</action\>  
    \</keybind\>  
\# run the following command to have openbox re-read the config file  
openbox \--reconfigure  
\# Determine what linux thinks the keys are named  
xev  
\# Sleep Suspend Shortcut  
\[Desktop Entry\]  
Encoding=UTF-8  
Version=1.0                                                 \# version of an app.  
Name\[en\_US\]=suspend                             \# name of an app.  
GenericName=Suspend                             \# longer name of an app.  
Exec=pm-suspend  
Terminal=false                                             \# whether an app requires to be run in a terminal.  
\#Icon\[en\_US\]=/opt/yed-3.11.1/icons/yicon32.png   \# location of icon file.  
Type=Application                                          \# type.  
Categories=Application

   
\--------------------------------------------------------------------------------------------------------------------------------

# Fail2Ban Notes

[http://stuffphilwrites.com/2013/03/permanently-ban-repeat-offenders-fail2ban/](http://stuffphilwrites.com/2013/03/permanently-ban-repeat-offenders-fail2ban/)

Files needed for the ssh-repeater block:  
/etc/fail2ban/action.d/iptables-repeater.conf  
/etc/fail2ban/jail.d/ssh-repeater.conf  
/etc/fail2ban/ip.blocklist.ssh

fail2ban-client status  
fail2ban-client set ssh unbanip 10.1.2.3

\#Troubleshoot fail2ban that won't start  
fail2ban-client \-vvv \-x start  
\--------------------------------------------------------------------------------------------------------------------------------

# Screenshots

shutter \-s \-o '\~/Pictures/screenshots/%y-%m-%d\_%T.png' \-e  
scrot \-s '%Y-%m-%d\_$wx$h.png' \-e 'mv $f \~/Pictures/screenshots/'  
gnome-screenshot \-a  
\--------------------------------------------------------------------------------------------------------------------------------

# Logstalgia

\# Visualize your access logs  
\# install on mac  
brew install logstalgia  
\# Connect to remote host, process access.log locally with logstagia   
 ssh user@example.com tail \-f /var/log/access.log  | logstalgia \--sync

[http://www.pc-freak.net/blog/fun-apache-nginx-webserver-log-visualize-webserver-access-log-real-time/](http://www.pc-freak.net/blog/fun-apache-nginx-webserver-log-visualize-webserver-access-log-real-time/)

\--------------------------------------------------------------------------------------------------------------------------------

# Send a running process to the background/detach and logout of SSH

[https://stackoverflow.com/questions/954302/how-to-make-a-programme-continue-to-run-after-log-out-from-ssh](https://stackoverflow.com/questions/954302/how-to-make-a-programme-continue-to-run-after-log-out-from-ssh)  
Assuming that you have a program running in the foreground, 

press ctrl-Z, then:

\[1\]+  Stopped                 myprogram  
$ disown \-h %1  
$ bg 1  
\[1\]+ myprogram &                                  \# This is the output  
$ logout                                                  \# It’s now safe to logout  
If there is only one job, then you don't need to specify the job number. Just use disown \-h and bg.

Explanation of the above steps:

You press ctrl-Z. The system suspends the running program, displays a job number and a "Stopped" message and returns you to a bash prompt.

You type the disown \-h %1 command (here, I've used a 1, but you'd use the job number that was displayed in the Stopped message) which marks the job so it ignores the SIGHUP signal (it will not be stopped by logging out).

Next, type the bg command using the same job number; this resumes the running of the program in the background and a message is displayed confirming that.

You can now log out and it will continue running..

\--------------------------------------------------------------------------------------------------------------------------------

# Hostnamectl

\# Cloud.init in ubuntu18 reverts the hostname after a reboot  
sudo vi /etc/cloud/cloud.cfg   
\# change preserve\_hostname from false to true  
\# maybe more steps todo in cloud.init ?  
sudo hostnamectl set-hostname newHostNameHere  
sudo systemctl restart cloud-init  
sudo reboot

\--------------------------------------------------------------------------------------------------------------------------------

# Setting static ip

\# view network adapters  
sudo ifconfig \-a  
\# test setting with ifconfig  
sudo ifconfig eth0:0 192.168.2.69 netmask 255.255.255.0 gw 192.168.2.1

\# Centos/RHEL   
sudo vi /etc/sysconfig/network-scripts/ifcfg-eth0

DEVICE=ens192  
TYPE=Ethernet  
ONBOOT=yes  
BOOTPROTO=none  
IPADDR=10.33.136.40  
NETMASK=255.255.255.0  
\# optional maybe necessary values below  
DNS1=10.0.0.1  
DNS2=10.0.0.2  
PREFIX=24  
GATEWAY=10.0.0.1

\--------------------------------------------------------------------------------------------------------------------------------

# SSH keys

## **John’s** 

ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAAAQwEXAMPLEKEYREDACTED03XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX== john-doe-2048  
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAAAQwEXAMPLEKEYREDACTED04XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX== jdoe@example.com  
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAAAQwEXAMPLEKEYREDACTED05XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX== jdoe@example.com  
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAAAQwEXAMPLEKEYREDACTED06XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX== jdoe@laptop3

## **bob’s**

ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAAAQwEXAMPLEKEYREDACTED01XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX== laptop2  
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAAAQwEXAMPLEKEYREDACTED02XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX== bsmith@laptop1  
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAAAQwEXAMPLEKEYREDACTED07XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX==  
ssh-rsa 

\--------------------------------------------------------------------------------------------------------------------------------

# Command Line Audit Logging

\# Add these lines to /etc/profile  
function log2syslog  
{  
   declare COMMAND  
   COMMAND=$(fc \-ln \-0)  
   logger \-p local1.notice \-t bash \-i \-- "${USER}:${COMMAND}"  
}  
trap log2syslog DEBUG

\#Add this line to /etc/rsyslog.conf  
local1.\* \-/var/log/cmdline  
\---------------------------------------------\-----------------------------------------------------------------------------------

# Let's Encrypt (lets encrypt, letsencrypt)

[https://certbot.eff.org/\#ubuntuxenial-other](https://certbot.eff.org/#ubuntuxenial-other)  
[https://certbot.eff.org/\#ubuntuxenial-nginx](https://certbot.eff.org/#ubuntuxenial-nginx)

\# setup:  
$ sudo apt-get update  
$ sudo apt-get install software-properties-common  
$ sudo add-apt-repository ppa:certbot/certbot  
$ sudo apt-get update  
$ sudo apt-get install certbot

\# Obtain certs  
\# Example:  
$ sudo certbot certonly \--standalone \-w /etc/nginx/ssl/ionreflux \-d ionreflux.com \-d www.ionreflux.com \-d ip.ionreflux.com \-d wiki.ionreflux.com

\# Test automation before scheduling cron  
$ sudo certbot renew \--dry-run

\# Obtain certs, update nginx config  
sudo certbot \--nginx \-d homelab.example.com \-d logs.homelab.example.com \-d magicmirror.homelab.example.com \-d device1.homelab.example.com \-d confluence.homelab.example.com \-d plexis.homelab.example.com \-d guacamole.homelab.example.com

\# example cron every 2 months  
0 5 1 \*/2 \* certbot renew  
\# more elaborate example provided automagically by certbot  
SHELL=/bin/sh  
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

0 \*/12 \* \* \* root test \-x /usr/bin/certbot \-a \\\! \-d /run/systemd/system && perl \-e ‘sleep int(rand(43200))’ && certbot \-q renew

\--------------------------------------------------------------------------------------------------------------------------------

# Meltdown / Spectre vulnerability

\# How to check if your ubuntu box is patched:  
grep CONFIG\_PAGE\_TABLE\_ISOLATION=y /boot/config-\`uname \-r\` && \\  
echo "patched :)" || echo "unpatched :("

\# or  
dmesg | grep 'page tables isolation'

Update using normal apt-get method.

[https://askubuntu.com/questions/992137/how-to-check-that-kpti-is-enabled-on-my-ubuntu/992186\#992186](https://askubuntu.com/questions/992137/how-to-check-that-kpti-is-enabled-on-my-ubuntu/992186#992186)  
[https://askubuntu.com/questions/992232/what-is-ubuntus-status-on-the-meltdown-and-spectre-vulnerabilities](https://askubuntu.com/questions/992232/what-is-ubuntus-status-on-the-meltdown-and-spectre-vulnerabilities)  
[https://wiki.ubuntu.com/Security/Upgrades](https://wiki.ubuntu.com/Security/Upgrades)   
\--------------------------------------------------------------------------------------------------------------------------------

# Slow ssh login

\# ssh logins to remote server take forever but ultimately succeed  
\# Update /etc/ssh/sshd\_config  
UseDNS yes

[https://jrs-s.net/2017/07/01/slow-ssh-logins/](https://jrs-s.net/2017/07/01/slow-ssh-logins/)

# Sleep/suspend wakes immediately

\# pm-suspend does not keep the computer asleep for more than 1-5 seconds  
[https://delightlylinux.wordpress.com/2017/12/05/fix-the-immediate-resume-following-suspend/](https://delightlylinux.wordpress.com/2017/12/05/fix-the-immediate-resume-following-suspend/)

sudo cat /proc/acpi/wakeup  
\# add any entries that have enabled  
/etc/rc.local
