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

