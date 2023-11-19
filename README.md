# Jarkom-Modul-3-D27-2023 (DHCP, Reverse Proxy, Laravel)

**File GNS-PROJECT: [Download disini](https://drive.google.com/file/d/1y11ZouuA_y9QNKshWwM7aWTxfMTAftxb/view?usp=sharing)**

**File GRIMOIRE: [Buka disini](https://docs.google.com/document/d/1iqQ7df3Q7cw7-deWoB5pyWaQEnqQNwwH/edit?usp=sharing&ouid=103928347637411344802&rtpof=true&sd=true)**

Anggota Kelompok Jarkom D27:
* Duevano Fairuz Pandya (5025211052)
* 

----------------------------------------------------------------------------------------------------------------------------------
**PREFIX: 10.35.X.X**

LOKASI SHELL SCRIPT / KONFIGURASI TIAP NOMOR:

0. Topologi, Heiter</br>
1-5. Himmel, Aura
6. Lawine, Linie, Lugner
7. Eisen
8. Eisen
9. Eisen
10. Eisen
11. Eisen
12. Eisen 
13. Denken (Frieren, Flamme, Fern cukup install mariadb-client)
14. Frieren, Flamme, Fern</br>
15-17. Eisen
18. Eisen
19. Frieren, Flamme, Fern
20. Eisen
</br></br>

**KONFIG /root/.bashrc**</br>
Isi dari semua konfig /root/.bashrc di setiap node adalah penginstalan dependency yang dibutuhkan oleh setiap node (apt-get update, apt-get install, dan lainnya)

**KONFIG IP**</br>
Switch 1 & 2: Static IP
Switch 3 & 4: DHCP (tetapi tiap worker diset jadi dhcp fixed address)



----------------------------------------------------------------------------------------------------------------------------------
**Revisi:**
* tidak ada


----------------------------------------------------------------------------------------------------------------------------------
# No. 0
### Soal
Setelah mengalahkan Demon King, perjalanan berlanjut. Kali ini, kalian diminta untuk melakukan register domain berupa riegel.canyon.yyy.com untuk worker Laravel dan granz.channel.yyy.com untuk worker PHP **(0)** mengarah pada worker yang memiliki IP [prefix IP].x.1.

**Node  |       Kategori        |       Image Docker    |       Konfigurasi IP**</br>
* Aura	Router (DHCP Relay)	danielcristh0/debian-buster:1.1	Dynamic
* Himmel	DHCP Server	danielcristh0/debian-buster:1.1	Static
* Heiter	DNS Server	danielcristh0/debian-buster:1.1	Static
* Denken	Database Server	danielcristh0/debian-buster:1.1	Static
* Eisen	Load Balancer	danielcristh0/debian-buster:1.1	Static
* Frieren	Laravel Worker	danielcristh0/debian-buster:1.1	Static
* Flamme	Laravel Worker	danielcristh0/debian-buster:1.1	Static
* Fern	Laravel Worker	danielcristh0/debian-buster:1.1	Static
* Lawine	PHP Worker	danielcristh0/debian-buster:1.1	Static
* Linie	PHP Worker	danielcristh0/debian-buster:1.1	Static
* Lugner	PHP Worker	danielcristh0/debian-buster:1.1	Static
* Revolte	Client	danielcristh0/debian-buster:1.1	Dynamic
* Richter	Client	danielcristh0/debian-buster:1.1	Dynamic
* Sein	Client	danielcristh0/debian-buster:1.1	Dynamic
* Stark	Client	danielcristh0/debian-buster:1.1	Dynamic



### Penyelesaian
![Alt text](image.png)
![Alt text](image-1.png)

**Script di node Heiter:**
```sh
#!/bin/bash

echo 'zone "riegel.canyon.d27.com" {
    type master;
    file "/etc/bind/jarkom/riegel.canyon.d27.com";
};

zone "granz.channel.d27.com" {
    type master;
    file "/etc/bind/jarkom/granz.channel.d27.com";
};

zone "3.35.10.in-addr.arpa" {
    type master;
    file "/etc/bind/jarkom/3.35.10.in-addr.arpa";
};

zone "4.35.10.in-addr.arpa" {
    type master;
    file "/etc/bind/jarkom/4.35.10.in-addr.arpa";
};' > /etc/bind/named.conf.local

mkdir /etc/bind/jarkom
cp /etc/bind/db.local /etc/bind/jarkom/riegel.canyon.d27.com
cp /etc/bind/db.local /etc/bind/jarkom/granz.channel.d27.com
cp /etc/bind/db.local /etc/bind/jarkom/4.35.10.in-addr.arpa
cp /etc/bind/db.local /etc/bind/jarkom/3.35.10.in-addr.arpa


echo ';
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     riegel.canyon.d27.com. root.riegel.canyon.d27.com. (
                        2023110101    ; Serial
                        604800        ; Refresh
                        86400         ; Retry
                        2419200       ; Expire
                        604800 )      ; Negative Cache TTL
;
@               IN      NS      riegel.canyon.d27.com.
@               IN      A       10.35.2.2       ; IP LB
www             IN      CNAME   riegel.canyon.d27.com.
' > /etc/bind/jarkom/riegel.canyon.d27.com


echo ';
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     granz.channel.d27.com. root.granz.channel.d27.com. (
                        2023110101    ; Serial
                        604800        ; Refresh
                        86400         ; Retry
                        2419200       ; Expire
                        604800 )      ; Negative Cache TTL
;
@               IN      NS      granz.channel.d27.com.
@               IN      A       10.35.2.2       ; IP LB
www             IN      CNAME   granz.channel.d27.com.
' > /etc/bind/jarkom/granz.channel.d27.com

echo ';
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     riegel.canyon.d27.com. root.riegel.canyon.d27.com. (
                        2023110101    ; Serial
                        604800        ; Refresh
                        86400         ; Retry
                        2419200       ; Expire
                        604800 )      ; Negative Cache TTL
;
4.35.10.in-addr.arpa.           IN      NS      riegel.canyon.d27.com.
1                               IN      PTR     riegel.canyon.d27.com.
' > /etc/bind/jarkom/4.35.10.in-addr.arpa

echo ';
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     granz.channel.d27.com. root.granz.channel.d27.com. (
                        2023110101    ; Serial
                        604800        ; Refresh
                        86400         ; Retry
                        2419200       ; Expire
                        604800 )      ; Negative Cache TTL
;
3.35.10.in-addr.arpa.           IN      NS      granz.channel.d27.com.
1                               IN      PTR     granz.channel.d27.com.
' > /etc/bind/jarkom/3.35.10.in-addr.arpa

echo 'options {
        directory "/var/cache/bind";

        forwarders {
                192.168.122.1;
        };

        // dnssec-validation auto;
        allow-query{any;};
        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
}; ' >/etc/bind/named.conf.options

service bind9 restart
```

* Melakukan konfigurasi dns seperti biasa
* Mengubah isi `/etc/bind/named.conf.local`
* Menambahkan direktori jarkom
* Membuat file `granz.channel.d27.com` dan `riege.canyon.d27.com` di dalam direktori jarkom
* Mengarahkan IP ke Loadbalancer (Eisen)

### Output
![Alt text](image-2.png)
* Hasil uji ping ke domain yang telah dibuat


Kendala: sempat bingung mengarahkan IP ke worker atau LB, namun akhirnya memtuskan untuk mengarahkan ke LB karena agar Loadbalancing bisa berjalan

----------------------------------------------------------------------------------------------------------------------------------
# No. 1-5
### Soal
* Lakukan konfigurasi sesuai dengan peta yang sudah diberikan.
Kemudian, karena masih banyak spell yang harus dikumpulkan, bantulah para petualang untuk memenuhi kriteria berikut.:</br>
1. Semua CLIENT harus menggunakan konfigurasi dari DHCP Server.**(1)**
2. Client yang melalui Switch3 mendapatkan range IP dari [prefix IP].3.16 - [prefix IP].3.32 dan [prefix IP].3.64 - [prefix IP].3.80 **(2)**
3. Client yang melalui Switch4 mendapatkan range IP dari [prefix IP].4.12 - [prefix IP].4.20 dan [prefix IP].4.160 - [prefix IP].4.168 **(3)**
4. Client mendapatkan DNS dari Heiter dan dapat terhubung dengan internet melalui DNS tersebut **(4)**
5. Lama waktu DHCP server meminjamkan alamat IP kepada Client yang melalui Switch3 selama 3 menit sedangkan pada client yang melalui Switch4 selama 12 menit. Dengan waktu maksimal dialokasikan untuk peminjaman alamat IP selama 96 menit **(5)**


### Penyelesaian
![Alt text](image-3.png)
* Melakukan konfigurasi di /etc/network/interfaces seperti gambar di atas

**Isi script node Himmel(DHCP Server):**
```sh
#!/bin/bash

echo 'subnet 10.35.1.0 netmask 255.255.255.0 {
}
subnet 10.35.2.0 netmask 255.255.255.0 {
}
subnet 10.35.3.0 netmask 255.255.255.0 {
    range 10.35.3.16 10.35.3.32;
    range 10.35.3.64 10.35.3.80;
    option routers 10.35.3.0;
    option broadcast-address 10.35.3.255;
    option domain-name-servers 10.35.1.2;
    min-lease-time 180;
    default-lease-time 180;
    max-lease-time 5760;
}
subnet 10.35.4.0 netmask 255.255.255.0 {
    range 10.35.4.12 10.35.4.20;
    range 10.35.4.160 10.35.4.168;
    option routers 10.35.4.0;
    option broadcast-address 10.35.4.255;
    option domain-name-servers 10.35.1.2;
    min-lease-time 720;
    default-lease-time 720;
    max-lease-time 5760;
}

host Lawine {
    hardware ethernet ae:f3:2f:92:6b:c1;
    fixed-address 10.35.3.1;
    default-lease-time 604800;
    max-lease-time 604800;
}
host Linie {
    hardware ethernet e6:34:be:1b:63:00;
    fixed-address 10.35.3.2;
    default-lease-time 604800;
    max-lease-time 604800;
}

host Lugner {
    hardware ethernet 82:e9:f1:ff:ab:3d;
    fixed-address 10.35.3.3;
    default-lease-time 604800;
    max-lease-time 604800;
}

host Frieren {
    hardware ethernet 86:aa:21:a7:c0:8c;
    fixed-address 10.35.4.1;
    default-lease-time 604800;
    max-lease-time 604800;
}

host Flamme {
    hardware ethernet d2:39:02:63:71:0f;
    fixed-address 10.35.4.2;
    default-lease-time 604800;
    max-lease-time 604800;
}

host Fern {
    hardware ethernet 32:a7:64:43:e5:32;
    fixed-address 10.35.4.3;
    default-lease-time 604800;
    max-lease-time 604800;
}
' >>  /etc/dhcp/dhcpd.conf

service isc-dhcp-server restart
```
* Setup masing-masing subnet dimana subnet (switch) 1 & 2 dibuat kosong karena memakai ip static, dan subnet 3 & 4 yang siap menerima DHCP
* Range IP yang bisa didapat oleh client sudah diatur sebagai mana seperti soal subnet 3 `range 10.35.3.16 10.35.3.32;` & `range 10.35.3.64 10.35.3.80;`
* dan subnet 4 adalah `range 10.35.4.12 10.35.4.20;` & `range 10.35.4.160 10.35.4.168;`
* Client otomatis mendapatkan nameserver dari dns server
* Lease time juga sudah diatur sebagaimana instruksi soal

**Isi script node Aura (DHCP Relay)**
```sh
#!/bin/bash

echo 'SERVERS="10.35.1.1" # IP Dhcp server(Himmel)
INTERFACES="eth1 eth2 eth3 eth4"
OPTIONS=
' > /etc/default/isc-dhcp-relay

echo 'net.ipv4.ip_forward=1
' > /etc/sysctl.conf

service isc-dhcp-relay restart
```
* Mengatur jalur mana saja (eth) yang akan dilewati broadcast dhcp server
* Mengarahkan ke dhcp server `SERVERS="10.35.1.1" # IP Dhcp server(Himmel)`


### Output
![Alt text](image-4.png)
![Alt text](image-5.png)

Kendala:


----------------------------------------------------------------------------------------------------------------------------------
