# ansible-rpi
This repository setups raspberry pi's headless with wifi

## About
This is an ansible repository used to setup raspberry pi's in a headless state. You should need a basic knowledge of ansible and linux/mac/\*nix to use this.

## Prerequisites
- ansible 2.x
- an ssh key [Guide here](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/ "Generating a new SSH key and adding it to the ssh-agent")
- latest Raspbian image [Download](https://www.raspberrypi.org/documentation/installation/installing-images/ "INSTALLING OPERATING SYSTEM IMAGES")
- a Raspberry Pi with some kind of wifi

## Pre Ansible Steps
All of these need to be done before we can run the ansible playbook

1. copy the latest Raspbian image to your SD card
   (See the "latest Raspbian image" link above for more info)
2. mount the second partition of the newly created Raspbian image
  ```
  mount /dev/disk2s2 /mnt/sd/
  ```

   (Note in my case the device was /dev/disk2s2 you can find your device name by using `fdisk -l`)

3. mount the first partition to /mnt/sd/boot
   Use the same steps as above
4. edit requirements/dhcpcd.conf
   go to the bottom of the file, update the IP address, router, and DNS servers
5. edit requirements/wpa\_supplicant.conf
  * change the country code to your countries two digit code
  * change the SSID to your wireless SSID name
  * change the PSK to your wireless password
  * use: `wpa_passphrase YOUR_SSID YOUR_PASSWORD` on a computer with wpa\_passphrase installed to mask your PSK
  * will look similar to this when done `psk=ceff3c46ec98932f4cea44f19b776f620027f78559cff46afde5e66ce835d9b2`
6. copy requirements/dhcpcd.conf to (and overwrite) /mnt/sd/etc/dhcpd.conf
7. copy requirements/wpa\_supplicant.conf to /mnt/sd/boot
8. create an empty file called "ssh" in /mnt/sd/boot
  * `touch /mnt/sd/boot/ssh`
9. unmount /mnt/sd/boot
  * `umount /mnt/sd/boot`
10. unmount /mnt/sd
  * `umount /mnt/sd`
11. put the sd card into your raspberry pi and plug it in
12. ensure your raspberrry pi is connected to your network
  * ping the ip address you put int dhcpcd.conf
  * if it's not responding wait a minute
  * if it's still not responding check dhcpcd.conf and wpa\_supplicant.conf for errors

## Ansible Steps
1. edit inventories/all/hosts
  * setup your hosts as formatted there
2. edit roles/common/vars/main.yml
  * edit to include the username and ssh key you want
3. edit roles/common/default/main.yml
  * include any packages you want installed on your raspberry pis
4. run the ansible playbook
  * `ansible-playbook -b site.yml -t setup`
