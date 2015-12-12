
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