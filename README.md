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
# No. 6
### Soal
1.	Pada masing-masing worker PHP, lakukan konfigurasi virtual host untuk website berikut dengan menggunakan php 7.3. **(6)**


### Penyelesaian
**Isi script node Lawine, Linie, Lugner**
```sh
#!/bin/bash

wget -O /var/www/granz.channel.d27.zip "https://drive.google.com/uc?export=download&id=1k-qOmAmA7pwSwi5u04iTyFsreBp2CHxs"

sleep 5

unzip /var/www/granz.channel.d27.zip -d /var/www/

rm /var/www/granz.channel.d27.zip

echo ' server {

        listen 80;

        root /var/www/granz.channel.d27;

        index index.php index.html index.htm;
        server_name granz.channel.d27.com;

        location / {
                        try_files $uri $uri/ /index.php?$query_string;
        }

        # pass PHP scripts to FastCGI server
        location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.3-fpm.sock;
        }

 location ~ /\.ht {
                        deny all;
        }

        error_log /var/log/nginx/granz_error.log;
        access_log /var/log/nginx/granz_access.log;
 }
' > /etc/nginx/sites-available/granz

ln -s /etc/nginx/sites-available/granz /etc/nginx/sites-enabled

rm /etc/nginx/sites-available/default
rm /etc/nginx/sites-enabled/default

service nginx restart
service php7.3-fpm start
```
* Melakukan setup deployment website menggunakan nginx seperti biasa
* File resources di download lalu di ekstrak di dalam `/var/www`
* Mengubah server_name menjadi `granz.channel.d27.com`
* Melakukan link file konfig dengan command `ln -s /etc/nginx/sites-available/granz /etc/nginx/sites-enabled`

### Output
![Alt text](image-6.png)
![Alt text](image-7.png)


Kendala:


----------------------------------------------------------------------------------------------------------------------------------
# No. 7
### Soal
2.	Kepala suku dari Bredt Region memberikan resource server sebagai berikut:<br>
a.	Lawine, 4GB, 2vCPU, dan 80 GB SSD.<br>
b.	Linie, 2GB, 2vCPU, dan 50 GB SSD.<br>
c.	Lugner 1GB, 1vCPU, dan 25 GB SSD.<br>
aturlah agar Eisen dapat bekerja dengan maksimal, lalu lakukan testing dengan 1000 request dan 100 request/second. **(7)**


### Penyelesaian
**Isi script node Eisen:**
```sh
#!/bin/bash

echo '# Default menggunakan Round Robin
upstream backend  {
        server 10.35.3.1 weight=640; #IP Lawine
        server 10.35.3.2 weight=200; #IP Linie
        server 10.35.3.3 weight=25; #IP Lugner
}

server {
listen 80;
server_name granz.channel.d27.com;

        location / {
                proxy_pass http://backend;
                proxy_set_header    X-Real-IP $remote_addr;
                proxy_set_header    X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header    Host $http_host;
        }

error_log /var/log/nginx/granz_error.log;
access_log /var/log/nginx/granz_access.log;

}
' > /etc/nginx/sites-available/lb-jarkom

unlink /etc/nginx/sites-enabled/default
ln -s /etc/nginx/sites-available/lb-jarkom /etc/nginx/sites-enabled
service nginx restart
```
* Menambahkan setup loadbalancing seperti biasa
* Menggunakan algoritma **weighted roundrobin** dengan bobot yang diperoleh berdasarkan perkalian resource yang tertera di soal
* Mengubah server_name menjadi granz.channel.d27.com
* Melakukan symlink `ln -s /etc/nginx/sites-available/lb-jarkom /etc/nginx/sites-enabled`

### Output
![Alt text](image-8.png)
![Alt text](image-9.png)
![Alt text](image-10.png)

### ab testing
* Menjalankan command
```sh
ab -n 1000 -c 100 http://granz.channel.d27.com/
```
* Hasil dari benchmark dapat dilihat di [GRIMOIRE](https://docs.google.com/document/d/1iqQ7df3Q7cw7-deWoB5pyWaQEnqQNwwH/edit?usp=sharing&ouid=103928347637411344802&rtpof=true&sd=true)

Kendala:


----------------------------------------------------------------------------------------------------------------------------------
# No. 8
### Soal
3.	Karena diminta untuk menuliskan grimoire, buatlah analisis hasil testing dengan 200 request dan 10 request/second masing-masing algoritma Load Balancer dengan ketentuan sebagai berikut:<br>
a.	Nama Algoritma Load Balancer<br>
b.	Report hasil testing pada Apache Benchmark<br>
c.	Grafik request per second untuk masing masing algoritma.<br> 
d.	Analisis **(8)**


### Penyelesaian
* Melakukan sedikit perubahan di `lb-jarkom` milik node Eisen
* Melakukan pengetesan ab testing di setiap algoritma<br>

* **Algoritma A (roundrobin)**
```sh
upstream backend  {
        server 10.35.3.1; #IP Lawine
        server 10.35.3.2; #IP Linie
        server 10.35.3.3; #IP Lugner
}
```
* **Algoritma B (weighted roundrobin)**
```sh
upstream backend  {
        server 10.35.3.1 weight=100; #IP Lawine
        server 10.35.3.2 weight=50; #IP Linie
        server 10.35.3.3 weight=25; #IP Lugner
}
```
* **Algoritma C (least connection)**
```sh
upstream backend  {
        least_conn;
        server 10.35.3.1; #IP Lawine
        server 10.35.3.2; #IP Linie
        server 10.35.3.3; #IP Lugner
}
```
* **Algoritma D (iphash)**
```sh
upstream backend  {
        ip_hash;
        server 10.35.3.1; #IP Lawine
        server 10.35.3.2; #IP Linie
        server 10.35.3.3; #IP Lugner
}
```
* **Algoritma E (generic hash)**
```sh
upstream backend  {
        hash $request_uri consistent;
        server 10.35.3.1; #IP Lawine
        server 10.35.3.2; #IP Linie
        server 10.35.3.3; #IP Lugner
}
```

###ab testing
* Menjalankan command `ab -n 200 -c 10 http://granz.channel.d27.com/` di setiap algoritma

### Output
Hasil dan analisis dari ab testing dapat dilihat di [GRIMOIRE](https://docs.google.com/document/d/1iqQ7df3Q7cw7-deWoB5pyWaQEnqQNwwH/edit?usp=sharing&ouid=103928347637411344802&rtpof=true&sd=true)

Kendala:


----------------------------------------------------------------------------------------------------------------------------------
# No. 9
### Soal
4.	Dengan menggunakan algoritma Round Robin, lakukan testing dengan menggunakan 3 worker, 2 worker, dan 1 worker sebanyak 100 request dengan 10 request/second, kemudian tambahkan grafiknya pada grimoire. **(9)**


### Penyelesaian
Cukup menjalankan script Bernama `no8algoA.sh` di node Eisen lalu ubah isi upstream sebagai berikut setiap pengetesannya.<br>
**3 worker**<br>
![Alt text](image-11.png)<br>
**2 worker**<br>
![Alt text](image-12.png)<br>
**1 worker**<br>
![Alt text](image-13.png)<br>

### ab testing
Command: 
command ab testing: <br>
```sh
ab -n 100 -c 10 http://granz.channel.d27.com/
```

### Output
Hasil dari pengetesan ab testing dapat dilihat di [GRIMOIRE](https://docs.google.com/document/d/1iqQ7df3Q7cw7-deWoB5pyWaQEnqQNwwH/edit?usp=sharing&ouid=103928347637411344802&rtpof=true&sd=true)


Kendala:


----------------------------------------------------------------------------------------------------------------------------------
# No. 10
### Soal
5.	Selanjutnya coba tambahkan konfigurasi autentikasi di LB dengan dengan kombinasi username: “netics” dan password: “ajkd27”, dengan yyy merupakan kode kelompok. Terakhir simpan file “htpasswd” nya di /etc/nginx/rahasisakita/ **(10)**


### Penyelesaian
**Isi script node Eisen:**
```sh
#!/bin/bash

mkdir /etc/nginx/rahasiakita
htpasswd -c -i -b /etc/nginx/rahasiakita/.htpasswd netics ajkd27

echo '# Default menggunakan Round Robin
upstream backend  {
        server 10.35.3.1; #IP Lawine
        server 10.35.3.2; #IP Linie
        server 10.35.3.3; #IP Lugner
}

server {
listen 80;
server_name granz.channel.d27.com;

        location / {
                proxy_pass http://backend;
                proxy_set_header    X-Real-IP $remote_addr;
                proxy_set_header    X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header    Host $http_host;

                auth_basic "Administrator Area";
                auth_basic_user_file /etc/nginx/rahasiakita/.htpasswd;
        }

        location ~ /\.ht {
            deny all;
        }

error_log /var/log/nginx/granz_error.log;
access_log /var/log/nginx/granz_access.log;

}
' > /etc/nginx/sites-available/lb-jarkom

service nginx restart
```
* Membuat folder rahasiakita di `/etc/nginx/`
* Menambahkan credentials menggunakan apache2-utils yakni `htpasswd` menggunakan command `htpasswd -c -i -b /etc/nginx/rahasiakita/.htpasswd netics ajkd27`
* Menambahkan atribut `auth_basic "Administrator Area";` & `auth_basic_user_file /etc/nginx/rahasiakita/.htpasswd;`


### Output
![Alt text](image-14.png)<br>
![Alt text](image-15.png)<br>
![Alt text](image-16.png)<br>
![Alt text](image-17.png)<br>
![Alt text](image-18.png)<br>

Kendala:


----------------------------------------------------------------------------------------------------------------------------------
# No. 11
### Soal
6.	Lalu buat untuk setiap request yang mengandung /its akan di proxy passing menuju halaman https://www.its.ac.id. **(11) hint: (proxy_pass)**


### Penyelesaian
**Isi script node Eisen:**
```sh
#!/bin/bash

echo '# Default menggunakan Round Robin
upstream backend  {
        server 10.35.3.1; #IP Lawine
        server 10.35.3.2; #IP Linie
        server 10.35.3.3; #IP Lugner
}

server {
listen 80;
server_name granz.channel.d27.com;

        location / {
                proxy_pass http://backend;
                proxy_set_header    X-Real-IP $remote_addr;
                proxy_set_header    X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header    Host $http_host;

                auth_basic "Administrator Area";
                auth_basic_user_file /etc/nginx/rahasiakita/.htpasswd;
        }

        location /its {
                proxy_pass https://www.its.ac.id;
        }

        location ~ /\.ht {
            deny all;
        }

error_log /var/log/nginx/granz_error.log;
access_log /var/log/nginx/granz_access.log;

}
' > /etc/nginx/sites-available/lb-jarkom

service nginx restart
```
* Cukup menambahkan proxypass ke url its sebagai berikut
```
        location /its {
                proxy_pass https://www.its.ac.id;
        }
```

### Output
![Alt text](image-19.png)<br>
![Alt text](image-20.png)
![Alt text](image-21.png)

Kendala:


----------------------------------------------------------------------------------------------------------------------------------
# No. 12
### Soal
7.	Selanjutnya LB ini hanya boleh diakses oleh client dengan IP [Prefix IP].3.69, [Prefix IP].3.70, [Prefix IP].4.167, dan [Prefix IP].4.168. **(12) hint: (fixed in dulu clientnya)**


### Penyelesaian
**Isi script node Eisen:**
```sh
#!/bin/bash

echo '# Default menggunakan Round Robin
upstream backend  {
        server 10.35.3.1; #IP Lawine
        server 10.35.3.2; #IP Linie
        server 10.35.3.3; #IP Lugner
}

server {
listen 80;
server_name granz.channel.d27.com;

        location / {
                allow 10.35.3.69;
                allow 10.35.3.70;
                allow 10.35.4.167;
                allow 10.35.4.168;
                deny all;
                proxy_pass http://backend;
                proxy_set_header    X-Real-IP $remote_addr;
                proxy_set_header    X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header    Host $http_host;

                auth_basic "Administrator Area";
                auth_basic_user_file /etc/nginx/rahasiakita/.htpasswd;
        }

        location /its {
                proxy_pass https://www.its.ac.id;
        }

        location ~ /\.ht {
            deny all;
        }

error_log /var/log/nginx/granz_error.log;
access_log /var/log/nginx/granz_access.log;

}
' > /etc/nginx/sites-available/lb-jarkom

service nginx restart
```
* Menambahkan IP yang disebutkan oleh soal dengan cara `allow [alamat ip];`
* Menolak semua request selain dari IP yang di allow dengan cara `deny all;`

### Output
![Alt text](image-22.png)
* Lalu restart node stark (disini saya hanya mengubah network interfaces stark untuk mengetes)
![Alt text](image-23.png)
![Alt text](image-24.png)


Kendala:


----------------------------------------------------------------------------------------------------------------------------------
# No. 13
### Soal



### Penyelesaian


### Output


Kendala:


----------------------------------------------------------------------------------------------------------------------------------
# No. 14
### Soal



### Penyelesaian


### Output


Kendala:


----------------------------------------------------------------------------------------------------------------------------------
# No. 15
### Soal



### Penyelesaian


### Output


Kendala:


----------------------------------------------------------------------------------------------------------------------------------