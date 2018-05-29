Using HypriotOS to run openSTF local
====================================

<span id="anchor"></span>Installation
-------------------------------------

### <span id="anchor-1"></span>Hypriot Docker Image for Raspberry Pi

Download and flash this image to your SD card.
Start your Pi with the flashed SD card and enjoy instant Docker awesomeness.
The latest releases can always be found on [GitHub](https://github.com/hypriot/image-builder-rpi/releases).

The default credentials for the image are user **pirate** with password **hypriot**.

|               |                                                                                                                                                                                                                                                          |                                   |            |
|---------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------|------------|
| Description   | Download Link                                                                                                                                                                                                                                            | Description                       | Published  |
| Version 1.9.0 | [hypriotos-rpi-v1.9.0.img.zip](https://github.com/hypriot/image-builder-rpi/releases/download/v1.9.0/hypriotos-rpi-v1.9.0.img.zip) [Checksum](https://github.com/hypriot/image-builder-rpi/releases/download/v1.9.0/hypriotos-rpi-v1.9.0.img.zip.sha256) | Docker 18.04.0-ce, kernel 4.14.34 | 28.04.2018 |

myLogin@myMachine ~

$ ssh pirate@black-pearl

pirate@black-pearl's password:

Linux black-pearl 4.14.34-hypriotos-v7+ \#1 SMP Sun Apr 22 14:57:31 UTC 2018 armv7l

HypriotOS (Debian GNU/Linux 9)

The programs included with the Debian GNU/Linux system are free software;

the exact distribution terms for each program are described in the

individual files in /usr/share/doc/\*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent

permitted by applicable law.

HypriotOS/armv7: pirate**@**black-pearl** in **~

**$**

 ifconfig

docker0: flags=4099&lt;UP,BROADCAST,MULTICAST&gt; mtu 1500

 inet 172.17.0.1 netmask 255.255.0.0 broadcast 172.17.255.255

 ether 02:42:b0:d0:bf:fa txqueuelen 0 (Ethernet)

 RX packets 0 bytes 0 (0.0 B)

 RX errors 0 dropped 0 overruns 0 frame 0

 TX packets 0 bytes 0 (0.0 B)

 TX errors 0 dropped 0 overruns 0 carrier 0 collisions 0

eth0: flags=4163&lt;UP,BROADCAST,RUNNING,MULTICAST&gt; mtu 1500

 inet ip.of.your.raspberry netmask 255.255.248.0 broadcast ip.of.your.broadcast

 inet6 fe80::1096:713d:a542:dfbb prefixlen 64 scopeid 0x20&lt;link&gt;

 ether b8:27:eb:4b:11:0d txqueuelen 1000 (Ethernet)

 RX packets 7423 bytes 1021610 (997.6 KiB)

 RX errors 0 dropped 22 overruns 0 frame 0

 TX packets 291 bytes 50988 (49.7 KiB)

 TX errors 0 dropped 0 overruns 0 carrier 0 collisions 0

lo: flags=73&lt;UP,LOOPBACK,RUNNING&gt; mtu 65536

 inet 127.0.0.1 netmask 255.0.0.0

 inet6 ::1 prefixlen 128 scopeid 0x10&lt;host&gt;

 loop txqueuelen 1000 (Local Loopback)

 RX packets 70 bytes 5718 (5.5 KiB)

 RX errors 0 dropped 0 overruns 0 frame 0

 TX packets 70 bytes 5718 (5.5 KiB)

 TX errors 0 dropped 0 overruns 0 carrier 0 collisions 0

wlan0: flags=4099&lt;UP,BROADCAST,MULTICAST&gt; mtu 1500

 ether b8:27:eb:1e:44:58 txqueuelen 1000 (Ethernet)

 RX packets 0 bytes 0 (0.0 B)

 RX errors 0 dropped 0 overruns 0 frame 0

 TX packets 0 bytes 0 (0.0 B)

 TX errors 0 dropped 0 overruns 0 carrier 0 collisions 0

 sudo mkdir /DATA

HypriotOS/armv7: pirate**@**black-pearl** in **~

**$ **cd /DATA

HypriotOS/armv7: pirate**@**black-pearl** in **/DATA

**$ **sudo chown -R pirate /DATA/

<span id="anchor-2"></span>Git
------------------------------

### <span id="anchor-3"></span>Proxy

** **sudo nano /etc/apt/apt.conf

**$ **cat /etc/apt/apt.conf

Acquire::http::Proxy "http://your.proxy.host:proxyPort";

### <span id="anchor-4"></span>curl-config

**$ **sudo -E apt-get install --reinstall libcurl4-openssl-dev

### <span id="anchor-5"></span>Certificates

<https://stackoverflow.com/questions/21181231/server-certificate-verification-failed-cafile-etc-ssl-certs-ca-certificates-c>

hostname=github.com

port=443

*trust\_cert\_file\_location=\`curl-config --ca\`*

**$ **sudo bash -c "echo -n | openssl s\_client -proxy your.proxy.host:proxyPort -showcerts -connect $hostname:$port \\

 2&gt;/dev/null | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' \\

 &gt;&gt; $trust\_cert\_file\_location"

As it is not working yet, we do *export GIT\_SSL\_NO\_VERIFY=1*

*The solution is written somewhere in *[*https://stackoverflow.com/questions/42616392/unable-to-correct-sudden-server-certificate-verification-failed*](https://stackoverflow.com/questions/42616392/unable-to-correct-sudden-server-certificate-verification-failed)* or *[*https://stackoverflow.com/questions/35821245/github-server-certificate-verification-failed/35824116\#35824116*](https://stackoverflow.com/questions/35821245/github-server-certificate-verification-failed/35824116#35824116)*.*

HypriotOS/armv7: pirate@black-pearl in /DATA

ALL\_PROXY=http://your.proxy.host:proxyPort git clone https://github.com/jonathas/openstf-arm7-docker

Cloning into 'openstf-arm7-docker'...

remote: Counting objects: 10, done.

remote: Total 10 (delta 0), reused 0 (delta 0), pack-reused 10

Unpacking objects: 100% (10/10), done.

### <span id="anchor-6"></span>Proxy for Docker

**$ **docker-compose up --build

Pulling adb (mitchtech/arm-adb:)...

ERROR: Get https://registry-1.docker.io/v2/: net/http: request canceled while waiting for connection (Client.Timeout exceeded while awaiting headers)

**$ **sudo mkdir -p /etc/systemd/system/docker.service.d/

HypriotOS/armv7: pirate**@**black-pearl** in **/DATA/openstf-arm7-docker

**$ **sudo vi /etc/systemd/system/docker.service.d/http-proxy.conf

HypriotOS/armv7: pirate**@**black-pearl** in **/DATA/openstf-arm7-docker

**$ **sudo vi /etc/systemd/system/docker.service.d/https-proxy.conf

HypriotOS/armv7: pirate**@**black-pearl** in **/DATA/openstf-arm7-docker

**$ **cat !$

cat /etc/systemd/system/docker.service.d/https-proxy.conf

\[Service\]

Environment="HTTPS\_PROXY=http://your.proxy.host:proxyPort"

**$ **sudo systemctl daemon-reload

HypriotOS/armv7: pirate**@**black-pearl** in **/DATA/openstf-arm7-docker

**$ **sudo systemctl restart docker

HypriotOS/armv7: pirate**@**black-pearl** in **/DATA/openstf-arm7-docker

**$ **systemctl show --property=Environment docker

Environment=HTTP\_PROXY=http://your.proxy.host:proxyPort HTTPS\_PROXY=http://your.proxy.host:proxyPort

$ docker-compose up --build

Pulling adb (mitchtech/arm-adb:)...

latest: Pulling from mitchtech/arm-adb

991507d4dcc6: Downloading \[=====&gt; \] 3.385MB/32.88MB

2ad44321f88a: Download complete

2f39bd897657: Download complete

a3ed95caeb02: Download complete

38949773330c: Downloading \[======&gt; \] 1.702MB/12.22MB

a6ac660d9104: Download com

### Launching openSTF

**$ **ifconfig

docker0: flags=4099&lt;UP,BROADCAST,MULTICAST&gt; mtu 1500

 inet 172.17.0.1 netmask 255.255.0.0 broadcast 172.17.255.255

 ether 02:42:b0:d0:bf:fa txqueuelen 0 (Ethernet)

 RX packets 0 bytes 0 (0.0 B)

 RX errors 0 dropped 0 overruns 0 frame 0

 TX packets 0 bytes 0 (0.0 B)

 TX errors 0 dropped 0 overruns 0 carrier 0 collisions 0

eth0: flags=4163&lt;UP,BROADCAST,RUNNING,MULTICAST&gt; mtu 1500

 inet ip.of.your.raspberry netmask 255.255.248.0 broadcast ip.of.your.broadcast

 inet6 fe80::1096:713d:a542:dfbb prefixlen 64 scopeid 0x20&lt;link&gt;

 ether b8:27:eb:4b:11:0d txqueuelen 1000 (Ethernet)

 RX packets 1897164 bytes 573876867 (547.2 MiB)

 RX errors 0 dropped 22 overruns 0 frame 0

 TX packets 1080202 bytes 64923611 (61.9 MiB)

 TX errors 0 dropped 0 overruns 0 carrier 0 collisions 0

\# Smartphone Test Farm using Docker on Raspberry Pi

This docker-compose config contains \[openstf\](https://openstf.io/), rethinkdb and \[adb\](https://developer.android.com/studio/command-line/adb.html).

In order to use it:

- Install Docker and \[docker-compose\](https://docs.docker.com/compose/install/)

- Open the docker-compose.yml file and edit the ip address there, entering your server's ip address instead

- Leave the docker-compose.yml file inside your home directory (/home/pi)

- Copy the docker-infra.service file from the systemd directory to /etc/systemd/system

- Reload the daemons: sudo systemctl daemon-reload

- Enable the service: sudo systemctl enable docker-infra

- Start everything: sudo systemctl start docker-infra

After everything is loaded, your server will be available on http://&lt; the ip address you configured &gt;:7100

So :

version: "2"

services:

 stf:

 image: openstf/stf-armv7l

 privileged: true

 container\_name: openstf

 depends\_on:

 - db

 - adb

 network\_mode: host

 command: "stf local --public-ip ip.of.your.raspberry"

 db:

 image: generalmeow/rethinkdb:1.0-arm

 container\_name: openstf\_db

 volumes:

 - "./rethinkdb\_data:/data"

 network\_mode: host

 adb:

 image: mitchtech/arm-adb

 privileged: true

 container\_name: adb

 network\_mode: host

networks:

 default:

 external:

 name: "host"

et

** **docker-compose up --build

and… Tadaam :

<img src="Pictures/10000000000001A4000001A358E952380368EBED.png" width="420" height="418" />

