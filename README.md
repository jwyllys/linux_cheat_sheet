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

[image1]: <data:image/png;base64,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>