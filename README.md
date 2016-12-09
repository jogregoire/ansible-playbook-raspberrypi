# Ansible scripts to configure a Raspberry Pi development environment

This playbook will create a reproducible, automated development environment as we all like them for your *headless* Raspberry Pi, including:
- python (pip upgraded, virtualenv, pylint)
- nodejs
- redis
- a few Linux tools (htop, nmap, dstat, tmux, vim)

It will also reconfigure some options in the Raspberry Pi configuration files, such as disabling HDMI for power savings. See https://github.com/geerlingguy/ansible-role-raspberry-pi/.

## Requirements

If you haven't done so yet, run ``sudo pip install ansible`` to install [Ansible](http://ansible.cc) on any machine running Linux/Unix.

A Raspberry Pi with Raspbian installed and plugged to your local network. You can download latest Raspbian here: https://www.raspberrypi.org/downloads/raspbian/

You also need SSH to be enabled, which is not by default. If you want to enable SSH, you have to put a file called ssh in the /boot/ directory on the SD card. The file's content doesn't matter. Everything is explained here: https://www.raspberrypi.org/blog/#a-security-update-for-raspbian-pixel

## Where is my Raspberry Pi?

If you are running your Raspberry headless and you don't know its IP address, you can get a list of active devices on your local network using _nmap_  (sn= disable port scan):

    $ nmap -sn 192.168.0.1/24
    Starting Nmap 7.01 ( https://nmap.org ) at 2016-12-04 21:13 UTC
    Nmap scan report for 192.168.0.27
    Host is up (0.0025s latency).
    Nmap scan report for 192.168.0.28
    Host is up (0.0025s latency).
    Nmap scan report for 192.168.0.51
    Host is up (0.0032s latency).
    Nmap scan report for 192.168.0.254
    Host is up (0.0047s latency).
    Nmap done: 256 IP addresses (7 hosts up) scanned in 21.26 seconds

Also, _arp-scan_ can be used to discover all hosts on the local network. It displays the MAC address and the vendor string corresponding to the MAC address, which give you one more hint about your Raspberry's IP (you need to be root to run _arp-scan_):

    $ sudo arp-scan --interface=enp0s8 --localnet
    Interface: enp0s8, datalink type: EN10MB (Ethernet)
    Starting arp-scan 1.8.1 with 256 hosts (http://www.nta-monitor.com/tools/arp-scan/)
    192.168.0.27    00:e0:--:--:--:--       REALTEK SEMICONDUCTOR CORP.
    192.168.0.28    a0:ce:--:--:--:--       (Unknown)
    192.168.0.47    e8:b1:--:--:--:--       (Unknown)
    192.168.0.51    00:11:--:--:--:--       Synology Incorporated
    192.168.0.254   00:24:--:--:--:--       FREEBOX SA

The above example displays _REALTEK_ which is the vendor of the USB Ethernet adapter plugged to my Raspberry.

## How to use

Edit the _inventory_ file and enter the IP address of your Raspberry. 

There is a dependency on a Ansible playbook to install redis. Install dependencies with the following command:

``sudo ansible-galaxy install -r requirements.yml``

Then set up your Raspberry using:

``ansible-playbook -i inventory --ask-pass playbook.yml``

The default password is _raspberry_.

It is recommended to use a key-based over a password authentication. To do so, give the path to your public key in ``var/main.yml``, for example:

``path_to_ssh_key: "~/.ssh/id_rsa.pub"``

You can then disable password authentication using the following Ansible role:

``ansible-playbook -i inventory geerlingguy.security``

## Author

Joel Gregoire
