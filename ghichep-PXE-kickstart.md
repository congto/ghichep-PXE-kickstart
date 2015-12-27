#### Các bước cài đặt

##### Cài đặt DHCP

- Cài đặt dhcp
```sh
sudo apt-get  -y install isc-dhcp-server
```

- Sửa file `/etc/default/isc-dhcp-server` để cấp dhcp qua card eth0
```sh
INTERFACES="eth0"
```

- Sửa file  `etc/dhcp/dhcpd.conf` để cấu hình DHCP cho máy chủ


```sh

- Tìm dòng dưới và sửa thành

option domain-name "labcloud.vnptdata";
option domain-name-servers 8.8.8.8, 8.8.4.4;

- Bỏ comment dòng 

authoritative;


- Thêm vào cuối dòng etc/dhcp/dhcpd.conf

subnet 172.16.69.0 netmask 255.255.255.0 {
range 172.16.69.11 172.16.69.19;
option domain-name-servers 8.8.8.8;
option domain-name "labcloud.vnptdata";
option routers 172.16.69.1;
option broadcast-address 172.16.69.255;
default-lease-time 600;
max-lease-time 7200;
}

```

- Thêm vào cuối cùng của file `/etc/dhcp/dhcpd.conf` các dòng dưới với IP là của máy PXE.

```sh
allow booting;
allow bootp;
option option-128 code 128 = string;
option option-129 code 129 = text;
next-server 172.16.69.216;
filename "pxelinux.0";
```


- Bắt đầu cấu hình cho pxe

- Cài đặt các gói 

```sh
sudo apt-get install apache2 tftpd-hpa inetutils-inetd
```

- Sửa file `/etc/default/tftpd-hpa`
- Chèn vào cuối dòng trên các dòng dưới
```sh
RUN_DAEMON="yes"
OPTIONS="-l -s /var/lib/tftpboot"
```

- Sửa file `/etc/inetd.conf`, thêm vào cuối cùng dòng dưới

```sh
tftp    dgram   udp    wait    root    /usr/sbin/in.tftpd /usr/sbin/in.tftpd -s /var/lib/tftpboot
```

- Copy file iso của OS cần cài và mount vào thư mục /mtn/

```sh
sudo mount -o loop /root/ubuntu-14.04.3-server-amd64.iso /mnt/
```


- Thực hiện copy các file cần thiết

```sh
sudo cp -fr install/netboot/* /var/lib/tftpboot/
```


- Tạo thư mục `ubuntu`

```sh
sudo mkdir /var/www/html/ubuntu
```

- Copy các file trong thư mục /mnt/ vừa mount ở trên

```sh
sudo cp -fr /mnt/* /var/www/html/ubuntu/
```


- Sửa file `/var/lib/tftpboot/pxelinux.cfg/default` để tạo menu boot

```sh
LABEL Ubuntu 14.04 
        MENU LABEL Trusty 14.04
        kernel ubuntu-installer/amd64/linux
        append ks=http://172.16.69.216/ksu14-lvmauto.cfg vga=normal initrd=ubuntu-installer/amd64/initrd.gz --
```

- Tạo file kickstart  đặt trong web root của máy PXE với tên là `ksu14-lvmauto.cfg`

```sh

lang en_US
langsupport en_US
keyboard us
mouse
timezone Asia/Ho_Chi_Minh

rootpw --disabled
user uvnpt --fullname "uvnpt" --iscrypted --password $1$TbmstQN5$4mNHr64I3qtiX.MdyWWi8.

reboot
text
install
# System bootloader configuration
bootloader --location=mbr

# Clear the Master Boot Record
zerombr yes

#Partition clearing information
clearpart --all --initlabel 


#Tu dong phan chia theo LVM
# Tham khao https://gist.github.com/congto/4f4ca5b6e66384dfdfa9
part pv.01 --size 1 --grow
volgroup ubuntu pv.01
logvol swap --fstype swap --name=swap --vgname=ubuntu --size 1024
logvol / --fstype ext4 --vgname=ubuntu --size=1 --grow --name=slash

# hack around Ubuntu kickstart bugs
preseed partman-lvm/confirm_nooverwrite boolean true
preseed partman-auto-lvm/no_boot        boolean true


auth  --useshadow  --enablemd5 

network --bootproto=dhcp --device=eth0
network --bootproto=dhcp --device=eth1
network --bootproto=dhcp --device=eth2



firewall --disabled 
skipx
%packages
@ ubuntu-server
openssh-server
landscape-common
vim

# %post

# hostname ${hostname}
# echo ${hostname} > /etc/hostname
# apt-get update -y && apt-get upgrade -y && apt-get dist-upgrade -y

```






#### Các lệnh lặt vặt

# Mount file iso vào ổ loop
sudo mount -o loop /root/ubuntu-14.04.3-server-amd64.iso /mnt/




- Khai báo cho DHCP
```sh

subnet 10.145.40.0 netmask 255.255.255.0 {
range 10.145.40.20 10.145.40.30;
option domain-name-servers 8.8.8.8;
option domain-name "congto.vn";
option routers 10.145.40.1;
option broadcast-address 10.145.40.255;
default-lease-time 600;
max-lease-time 7200;

}

allow booting;
allow bootp;
option option-128 code 128 = string;
option option-129 code 129 = text;
next-server 10.145.40.59;
filename "pxelinux.0";

```

```sh

LABEL Ubuntu 14.04 Desktop
	MENU LABEL Trusty 14.04 Desktop
	KERNEL ubuntu-installer/amd64/linux
	append vga=normal ks=http://192.168.1.254/ks-1404-64-desktop.cfg initrd=ubuntu-installer/amd64/initrd.gz  hostname=desktop --
  
  
  
  # D-I config version 2.0
include ubuntu-installer/amd64/boot-screens/menu.cfg
default ubuntu-installer/amd64/boot-screens/vesamenu.c32
prompt 0
timeout 0

LABEL Ubuntu 14.04 
        MENU LABEL Trusty 14.04
        kernel ubuntu-installer/amd64/linux
        append ks=http://192.168.100.6/ksubuntu.cfg vga=normal initrd=ubuntu-installer/amd64/initrd.gz --
##########

```
Các chú ý sửa file cho DNS
- Sửa file DHCP

vi /etc/dhcp/dhcpd.conf

- Sua file de khai bao menu cua DIA BOOT

vi /var/lib/tftpboot/pxelinux.cfg/default

#### Link tham khảo
http://www.unixmen.com/install-and-configure-pxe-server-on-ubuntu-15-04/

