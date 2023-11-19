# Jarkom-Modul-3-D07-2023
## Pendahuluan

Repository ini dibentuk untuk menyelesaikan tugas praktikum Jaringan Komputer.

Anggota Kelompok D07:
| Nama | NRP |
| :---: | :---: |
| Danno Denis Dhaifullah | 5025211027 |
| Fihriz Ilham Rabbany | 5025211040 |

## Pembahasan

### Soal 0 - Soal 5

Setelah mengalahkan Demon King, perjalanan berlanjut. Kali ini, kalian diminta untuk melakukan register domain berupa riegel.canyon.yyy.com untuk worker Laravel dan granz.channel.yyy.com untuk worker PHP (0) mengarah pada worker yang memiliki IP [prefix IP].x.1. 

Lakukan konfigurasi sesuai dengan peta yang sudah diberikan. (1)

Kemudian, karena masih banyak spell yang harus dikumpulkan, bantulah para petualang untuk memenuhi kriteria berikut.:

1. Semua CLIENT harus menggunakan konfigurasi dari DHCP Server.
2. Client yang melalui Switch3 mendapatkan range IP dari [prefix IP].3.16 - [prefix IP].3.32 dan [prefix IP].3.64 - [prefix IP].3.80 (2)
3. Client yang melalui Switch4 mendapatkan range IP dari [prefix IP].4.12 - [prefix IP].4.20 dan [prefix IP].4.160 - [prefix IP].4.168 (3)
4. Client mendapatkan DNS dari Heiter dan dapat terhubung dengan internet melalui DNS tersebut (4)
5. Lama waktu DHCP server meminjamkan alamat IP kepada Client yang melalui Switch3 selama 3 menit sedangkan pada client yang melalui Switch4 selama 12 menit. Dengan waktu maksimal dialokasikan untuk peminjaman alamat IP selama 96 menit (5)


**Jawaban**

Aura Router (DHCP Relay)
```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 10.25.1.0
	netmask 255.255.255.0

auto eth2
	iface eth2 inet static
	address 10.25.2.0
	netmask 255.255.255.0

auto eth3
	iface eth3 inet static
	address 10.25.3.0
	netmask 255.255.255.0
auto eth4
	iface eth4 inet static
	address 10.25.4.0
	netmask 255.255.255.0
```

Himmel DHCP Server
```
auto eth0
iface eth0 inet static
	address 10.25.1.1
	netmask 255.255.255.0
	gateway 10.25.1.0

```

Heiter DNS Server
```
auto eth0
iface eth0 inet static
	address 10.25.1.2
	netmask 255.255.255.0
	gateway 10.25.1.0
```


Denken Database Server
```
auto eth0
iface eth0 inet static
	address 10.25.2.1
	netmask 255.255.255.0
	gateway 10.25.2.0
```

Eisen Load Balancer
```
auto eth0
iface eth0 inet static
	address 10.25.2.2
	netmask 255.255.255.0
	gateway 10.25.2.0
```

Frieren Laravel Worker
```
auto eth0
iface eth0 inet static
	address 10.25.4.1
	netmask 255.255.255.0
	gateway 10.25.4.0
```

Flamme Laravel Worker
```
auto eth0
iface eth0 inet static
	address 10.25.4.2
	netmask 255.255.255.0
	gateway 10.25.4.0
```

Fern Laravel Worker
```
auto eth0
iface eth0 inet static
	address 10.25.4.3
	netmask 255.255.255.0
	gateway 10.25.4.0
```

Lawine PHP Worker
```
auto eth0
iface eth0 inet static
	address 10.25.3.1
	netmask 255.255.255.0
	gateway 10.25.3.0
```

Linie PHP Worker
```
auto eth0
iface eth0 inet static
	address 10.25.3.2
	netmask 255.255.255.0
	gateway 10.25.3.0
```

Lugner PHP Worker
```
auto eth0
iface eth0 inet static
	address 10.25.3.3
	netmask 255.255.255.0
	gateway 10.25.3.0
```

Revolte Client
```
auto eth0
iface eth0 inet dhcp
```

Richter Client
```
auto eth0
iface eth0 inet dhcp
```

Sein Client
```
auto eth0
iface eth0 inet dhcp
```

Stark Client
```
auto eth0
iface eth0 inet dhcp
```

#### Aura (DHCP Relay):

```
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.25.0.0/16

apt-get update
apt-get install isc-dhcp-relay -y

echo "
SERVERS=\"10.25.1.1\"
INTERFACES=\"eth1 eth2 eth3 eth4\"
OPTIONS=\"\"" > /etc/default/isc-dhcp-relay

echo "
net.ipv4.ip_forward=1" >> /etc/sysctl.conf

service isc-dhcp-relay start
```

1. `iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.25.0.0/16`  
   - Mengonfigurasi iptables untuk melakukan Network Address Translation (NAT) pada paket keluar.

2. Konfigurasi isc-dhcp-relay:
   - Menentukan server DHCP yang akan digunakan (`SERVERS="10.25.1.1"`).
   - Menentukan interface di mana DHCP Relay akan bekerja (`INTERFACES="eth1 eth2 eth3 eth4"`).

3. `/etc/sysctl.conf`
   - Menambahkan konfigurasi IP forwarding pada .

4. `service isc-dhcp-relay start`.
   - Memulai layanan isc-dhcp-relay

#### Himmel (DHCP Server):

```
echo "nameserver 192.168.122.1" > /etc/resolv.conf

apt-get update
apt-get install isc-dhcp-server -y

echo "
INTERFACESv4=\"eth0\"
INTERFACESv6=\"\"" > /etc/default/isc-dhcp-server

service isc-dhcp-server restart

echo "
subnet 10.25.1.0 netmask 255.255.255.0 {
}

subnet 10.25.2.0 netmask 255.255.255.0 {
}

subnet 10.25.3.0 netmask 255.255.255.0 {
	range 10.25.3.16 10.25.3.32;
	range 10.25.3.64 10.25.3.80;
	option routers 10.25.3.0;
	option broadcast-address 10.25.3.255;
	option domain-name-servers 10.25.1.2;
	default-lease-time 180;
	max-lease-time 5760;
}

subnet 10.25.4.0 netmask 255.255.255.0 {
	range 10.25.4.12 10.25.4.20;
	range 10.25.4.160 10.25.4.168;
	option routers 10.25.4.0;
	option broadcast-address 10.25.4.255;
	option domain-name-servers 10.25.1.2;
	default-lease-time 720;
	max-lease-time 5760;
}" > /etc/dhcp/dhcpd.conf

service isc-dhcp-server restart
```
1. Konfigurasi isc-dhcp-server, menentukan interface di mana server DHCP akan beroperasi (`INTERFACESv4="eth0"`).

2. `service isc-dhcp-server restart` Me-restart layanan isc-dhcp-server.
   
3. . Konfigurasi DHCP server pada `/etc/dhcp/dhcpd.conf`:
   - Mendefinisikan subnet dan menentukan konfigurasi untuk subnet 10.25.3.0 dan 10.25.4.0, termasuk rentang alamat IP, router, alamat siaran, server DNS, waktu sewa default, dan waktu sewa maksimal sesuai permintaan soal.

4. `service isc-dhcp-server restart`  
   - Me-restart layanan isc-dhcp-server setelah mengonfigurasi.

#### Heither (DNS Server):

```
echo "nameserver 192.168.122.1" > /etc/resolv.conf

apt-get update
apt-get install bind9 -y

service bind9 restart

echo "
options {
    directory \"/var/cache/bind\";

    forwarders {
        8.8.8.8;
        8.8.8.4;
        192.168.122.1;
        0.0.0.0;
    };

    // dnssec-validation auto;
    allow-query { any; };
    auth-nxdomain no;
    listen-on-v6 { any; };
};" > /etc/bind/named.conf.options

service bind9 restart

echo "
zone \"riegel.canyon.d07.com\" {
        type master;
        file \"/etc/bind/riegel/riegel.canyon.d07.com\";
};

zone \"granz.channel.d07.com\" {
        type master;
        file \"/etc/bind/granz/granz.channel.d07.com\";
};" > /etc/bind/named.conf.local

mkdir /etc/bind/riegel
echo "
;
; BIND data file for local loopback interface
;
\$TTL    604800
@       IN      SOA     riegel.canyon.d07.com. root.riegel.canyon.d07.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      riegel.canyon.d07.com.
@       IN      A       10.25.4.1 ; IP Frieren
www     IN      CNAME   riegel.canyon.d07.com." > /etc/bind/riegel/riegel.canyon.d07.com

mkdir /etc/bind/granz
echo "
;
; BIND data file for local loopback interface
;
\$TTL    604800
@       IN      SOA     granz.channel.d07.com. root.granz.channel.d07.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      granz.channel.d07.com.
@       IN      A       10.25.3.1 ; IP Lawine
www     IN      CNAME   granz.channel.d07.com." > /etc/bind/granz/granz.channel.d07.com

service bind9 restart

```

1. Konfigurasi DNS server pada `/etc/bind/named.conf.options`:
   - Menentukan direktori untuk BIND cache.
   - Menetapkan server DNS untuk forwarding (8.8.8.8, 8.8.8.4, 192.168.122.1, 0.0.0.0), agar dapat mengakses internet.

2. `service bind9 restart`  
   - Me-restart layanan bind9 setelah mengonfigurasi.

3. `echo "..." > /etc/bind/named.conf.local`  
   - Menambahkan konfigurasi zone untuk dua domain: `riegel.canyon.d07.com` dan `granz.channel.d07.com`.

4. `mkdir /etc/bind/riegel`  
   - Membuat direktori untuk file konfigurasi zona Riegel.

5. `echo "..." > /etc/bind/riegel/riegel.canyon.d07.com`  
   - Membuat file konfigurasi zona Riegel.
   - Menentukan parameter SOA (Start of Authority), NS (Name Server), dan A (Alamat) record.
   - Menetapkan alamat IP (`10.25.4.1`) untuk domain.

6. `mkdir /etc/bind/granz`  
   - Membuat direktori untuk file konfigurasi zona Granz.

7. `echo "..." > /etc/bind/granz/granz.channel.d07.com`  
   - Membuat file konfigurasi zona Granz.
   - Menentukan parameter SOA, NS, dan A record.
   - Menetapkan alamat IP (`10.25.3.1`) untuk domain.
   - 
8. `service bind9 restart`  
   - Me-restart layanan BIND9 setelah menambahkan konfigurasi zona.

#### Screenshots
> <img width="555" alt="image" src="https://github.com/fihrizilhamr/Jarkom-Modul-3-D07-2023/assets/116176265/14d8ab61-32e7-4df4-bb30-16912a66264c">

> <img width="555" alt="image" src="https://github.com/fihrizilhamr/Jarkom-Modul-3-D07-2023/assets/116176265/cb407268-8dd7-4f85-96d8-ffceec0a53c4">


### Soal 6

Berjalannya waktu, petualang diminta untuk melakukan deployment.
Pada masing-masing worker PHP, lakukan konfigurasi virtual host untuk website [berikut](https://drive.google.com/file/d/1ViSkRq7SmwZgdK64eRbr5Fm1EGCTPrU1/view?usp=sharing) dengan menggunakan php 7.3. (6)

**Jawaban**

#### PHP Workers (Lawine, Linie, Lugner):

```
echo "nameserver 192.168.122.1" > /etc/resolv.conf
apt-get update
apt-get install nginx php7.3 php-fpm ca-certificates openssl unzip wget -y 

wget --no-check-certificate "https://drive.google.com/uc?export=download&id=1ViSkRq7SmwZgdK64eRbr5Fm1EGCTPrU1" -O download.zip
unzip download.zip
mv modul-3 /var/www/granz.channel.d07.com

echo "
server {
    listen 80;
    root /var/www/granz.channel.d07.com;
    index index.php index.html index.htm;
    server_name granz.channel.d07.com;

    location / {
        try_files \$uri \$uri/ /index.php?\$query_string;
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
" > /etc/nginx/sites-available/granz.channel.d07.com

rm -rf /etc/nginx/sites-enabled/default

ln -s /etc/nginx/sites-available/granz.channel.d07.com /etc/nginx/sites-enabled

service php7.3-fpm start
service nginx restart
```

1. Mengunduh dan mengekstrak file dari Google Drive.

2. Memindahkan modul-3 ke direktori `/var/www/granz.channel.d07.com`.

3. Mengkonfigurasi Nginx untuk situs `granz.channel.d07.com`:
   - Menentukan root direktori dan file index.
   - Mengonfigurasi location untuk menangani permintaan ke PHP scripts.
   - Menyimpan konfigurasi di `/etc/nginx/sites-available/granz.channel.d07.com`.

4. Menghapus konfigurasi default Nginx.

5. Membuat symbolic link untuk konfigurasi baru.

6. Memulai layanan PHP-FPM dan me-restart layanan Nginx.

#### Load Balancer (Eisen):


```
echo "nameserver 192.168.122.1" > /etc/resolv.conf

apt-get update
apt-get install -y bind9 nginx apache2-utils

echo "
upstream backend  {
    server 10.25.3.1; # IP Lawine
    server 10.25.3.2; # IP Linie
    server 10.25.3.3; # IP Lugner
}

server {
    listen 80;
    server_name _;

    location / {
        proxy_pass http://backend;
        proxy_set_header X-Real-IP \$remote_addr;
        proxy_set_header X-Forwarded-For \$proxy_add_x_forwarded_for;
        proxy_set_header Host \$http_host;
    }

    error_log /var/log/nginx/granz_error.log;
    access_log /var/log/nginx/granz_access.log;
}" > /etc/nginx/sites-available/granz.channel.d07.com

rm -rf /etc/nginx/sites-enabled/default

ln -s /etc/nginx/sites-available/granz.channel.d07.com /etc/nginx/sites-enabled

service bind9 restart
service nginx restart
```

1. Mengonfigurasi Nginx untuk berperan sebagai proxy load balancer:
   - Menentukan backend server dengan alamat IP Lawine, Linie, dan Lugner.
   - Mengonfigurasi location untuk meneruskan permintaan ke backend server.
   - Menyimpan konfigurasi di `/etc/nginx/sites-available/granz.channel.d07.com`.

2. Menonaktifkan konfigurasi default Nginx.

3. Membuat symbolic link untuk konfigurasi baru.

4. Me-restart layanan BIND9 dan Nginx.

#### Client (Revolte, Richter, Sein, Stark):

```
apt-get update
apt-get install lynx apache2-utils -y
apt-get install apache2-utils -y
```

#### DNS Server (Heither):

```
echo "
;
; BIND data file for local loopback interface
;
\$TTL    604800
@       IN      SOA     granz.channel.d07.com. root.granz.channel.d07.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      granz.channel.d07.com.
@       IN      A       10.25.2.2 ; IP Eisen
www     IN      CNAME   granz.channel.d07.com." > /etc/bind/granz/granz.channel.d07.com

service bind9 restart
```
1. Mengkonfigurasi zona Granz pada `/etc/bind/granz/granz.channel.d07.com`:
   - Menetapkan alamat IP (`10.25.2.2`) untuk domain.
2. Me-restart layanan BIND9.


#### Screenshots
><img width="555" alt="image" src="https://github.com/fihrizilhamr/Jarkom-Modul-3-D07-2023/assets/116176265/b6f7437e-4ece-4043-a11e-f6d283d105fd">

><img width="555" alt="image" src="https://github.com/fihrizilhamr/Jarkom-Modul-3-D07-2023/assets/116176265/cac8a5d3-6f6b-4d4b-93c2-364aaf01c88b">

><img width="555" alt="image" src="https://github.com/fihrizilhamr/Jarkom-Modul-3-D07-2023/assets/116176265/d6661af0-dd2a-4075-8120-5f7f1a59b7e4">


### Soal 7

Kepala suku dari [Bredt Region](https://frieren.fandom.com/wiki/Bredt_Region) memberikan resource server sebagai berikut:
1. Lawine, 4GB, 2vCPU, dan 80 GB SSD.
2. Linie, 2GB, 2vCPU, dan 50 GB SSD.
3. Lugner 1GB, 1vCPU, dan 25 GB SSD.

aturlah agar Eisen dapat bekerja dengan maksimal, lalu lakukan testing dengan 1000 request dan 100 request/second. (7)


**Jawaban**

### Soal 8

Karena diminta untuk menuliskan grimoire, buatlah analisis hasil testing dengan 200 request dan 10 request/second masing-masing algoritma Load Balancer dengan ketentuan sebagai berikut:
1. Nama Algoritma Load Balancer
2. Report hasil testing pada Apache Benchmark
3. Grafik request per second untuk masing masing algoritma. 
4. Analisis (8)

**Jawaban**

### Soal 9

Dengan menggunakan algoritma Round Robin, lakukan testing dengan menggunakan 3 worker, 2 worker, dan 1 worker sebanyak 100 request dengan 10 request/second, kemudian tambahkan grafiknya pada grimoire. (9)

**Jawaban**

### Soal 10

Selanjutnya coba tambahkan konfigurasi autentikasi di LB dengan dengan kombinasi username: “netics” dan password: “ajkyyy”, dengan yyy merupakan kode kelompok. Terakhir simpan file “htpasswd” nya di /etc/nginx/rahasisakita/ (10)


**Jawaban**

### Soal 11

Lalu buat untuk setiap request yang mengandung /its akan di proxy passing menuju halaman https://www.its.ac.id. (11) hint: (proxy_pass)

**Jawaban**

### Soal 12

Selanjutnya LB ini hanya boleh diakses oleh client dengan IP [Prefix IP].3.69, [Prefix IP].3.70, [Prefix IP].4.167, dan [Prefix IP].4.168. (12) hint: (fixed in dulu clinetnya)

**Jawaban**



### Soal 13
Karena para petualang kehabisan uang, mereka kembali bekerja untuk mengatur riegel.canyon.yyy.com.

Semua data yang diperlukan, diatur pada Denken dan harus dapat diakses oleh Frieren, Flamme, dan Fern. (13)

**Jawaban**

### Soal 14

Frieren, Flamme, dan Fern memiliki Riegel Channel sesuai dengan quest guide berikut. Jangan lupa melakukan instalasi PHP8.0 dan Composer (14)

**Jawaban**

### Soal 15 - Soal 17

Riegel Channel memiliki beberapa endpoint yang harus ditesting sebanyak 100 request dengan 10 request/second. Tambahkan response dan hasil testing pada grimoire.
POST /auth/register (15)
POST /auth/login (16)
GET /me (17)

**Jawaban**

### Soal 18

Untuk memastikan ketiganya bekerja sama secara adil untuk mengatur Riegel Channel maka implementasikan Proxy Bind pada Eisen untuk mengaitkan IP dari Frieren, Flamme, dan Fern. (18)

**Jawaban**

### Soal 19

Untuk meningkatkan performa dari Worker, coba implementasikan PHP-FPM pada Frieren, Flamme, dan Fern. Untuk testing kinerja naikkan 
- pm.max_children
- pm.start_servers
- pm.min_spare_servers
- pm.max_spare_servers
sebanyak tiga percobaan dan lakukan testing sebanyak 100 request dengan 10 request/second kemudian berikan hasil analisisnya pada Grimoire.(19)

**Jawaban**

### Soal 20

Nampaknya hanya menggunakan PHP-FPM tidak cukup untuk meningkatkan performa dari worker maka implementasikan Least-Conn pada Eisen. Untuk testing kinerja dari worker tersebut dilakukan sebanyak 100 request dengan 10 request/second. (20)


**Jawaban**
