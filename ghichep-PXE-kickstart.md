
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
	append vga=normal ks=http://192.168.1.254/ks-1404-64-desktop.cfg initrd=ubuntu-installer/amd64/initrd.gz live-installer/net-image=http://192.168.1.254/ubuntu-14.04.amd64-server/install/filesystem.squashfs hostname=desktop --
  
  
  
  # D-I config version 2.0
include ubuntu-installer/amd64/boot-screens/menu.cfg
default ubuntu-installer/amd64/boot-screens/vesamenu.c32
prompt 0
timeout 0

LABEL Ubuntu 14.04 
        MENU LABEL Trusty 14.04
        kernel ubuntu-installer/amd64/linux
        append ks=http://192.168.100.6/ksubuntu.cfg file=http://192.168.100.6/ubuntu/preseed/ubuntu-server.seed  vga=normal initrd=ubuntu-installer/amd64/initrd.gz --
##########

```
Các chú ý sửa file cho DNS
- Sửa file DHCP
vi /etc/dhcp/dhcpd.conf

- Sua file de khai bao menu cua DIA BOOT
vi /var/lib/tftpboot/pxelinux.cfg/default

