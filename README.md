# Jarkom-Modul-3-D07-2023
## Pendahuluan

Repository ini dibentuk untuk menyelesaikan tugas praktikum Jaringan Komputer.

Anggota Kelompok D07:
| Nama | NRP |
| :---: | :---: |
| Danno Denis Dhaifullah | 5025211027 |
| Fihriz Ilham Rabbany | 5025211040 |

## File Project GNS3
[Download File Project GNS3](https://drive.google.com/drive/folders/1wIxy6h0Sr8api050JvDmCSTAg2q9_sU-?usp=drive_link)

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

Pada heiter, arahkan DNS mengarah ke Load balancer dengan script berikut :

```
#!bin/bash

echo '
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     granz.channel.d07.com. root.granz.channel.d07.com. (
                        2023131101      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@               IN      NS      granz.channel.d07.com.
@               IN      A       10.25.2.2       ; IP Eisen
www             IN      CNAME   granz.channel.d07.com.
' > /etc/bind/granz/granz.channel.d07.com

service bind9 restart
```

pada eisen setup loadbalancer round robin dengan script berikut :

```
service nginx start

cp lb-roundrobin /etc/nginx/sites-available/lb-granz

rm -f /etc/nginx/sites-enabled/lb-granz
ln -s /etc/nginx/sites-available/lb-granz /etc/nginx/sites-enabled/

service nginx restart
```

Adapun konten file lb-roundrobin sebagai berikut:

```
upstream worker_round_robin {
  server 10.25.3.1;
  server 10.25.3.2;
  server 10.25.3.3;
}

server {
    listen 80;
    server_name granz.channel.b14.com www.granz.channel.d07.com;

    root /var/www/html;

    index index.html index.htm index.nginx-debian.html;

    location / {
        proxy_pass http://worker_round_robin;
    }
    
}
```

<img width="634" alt="image" src="https://github.com/fihrizilhamr/Jarkom-Modul-3-D07-2023/assets/105486369/afeda222-35d1-467c-9ea1-775861a6d963">

<img width="493" alt="image" src="https://github.com/fihrizilhamr/Jarkom-Modul-3-D07-2023/assets/105486369/ee6d37b4-1f6e-4178-97ce-e5e09f1419ab">


### Soal 8

Karena diminta untuk menuliskan grimoire, buatlah analisis hasil testing dengan 200 request dan 10 request/second masing-masing algoritma Load Balancer dengan ketentuan sebagai berikut:
1. Nama Algoritma Load Balancer
2. Report hasil testing pada Apache Benchmark
3. Grafik request per second untuk masing masing algoritma. 
4. Analisis (8)

**Jawaban**

Lakukan percobaan menggunakan script load balancer yang disediakan dengan menambahkan pada bagian upworker. Kemudian saat melakukan running di client dengan command ``ab -n 200 -c 10 http://www.granz.channel.d07.com/`` jalankan htop pada masing masing worker. Maka hasilnya akan seperti berikut:

a. Round Robin

<img width="552" alt="image" src="https://github.com/fihrizilhamr/Jarkom-Modul-3-D07-2023/assets/105486369/714206e6-a6d8-4b9b-ad3f-9dff403c9219">


<img width="749" alt="image" src="https://github.com/fihrizilhamr/Jarkom-Modul-3-D07-2023/assets/105486369/e45ba5b8-2f3b-4dea-8ba8-8ab2fc59cb25">


b. Least Connection

<img width="566" alt="image" src="https://github.com/fihrizilhamr/Jarkom-Modul-3-D07-2023/assets/105486369/c56ddc84-ae84-452b-b4d3-0ba45bd99d3a">


<img width="744" alt="image" src="https://github.com/fihrizilhamr/Jarkom-Modul-3-D07-2023/assets/105486369/eb662eee-468a-4962-a276-c258ed2fe303">


c. IP Hash

<img width="554" alt="image" src="https://github.com/fihrizilhamr/Jarkom-Modul-3-D07-2023/assets/105486369/bdd4c66f-e824-45c4-9d91-b50ee68178a1">


<img width="747" alt="image" src="https://github.com/fihrizilhamr/Jarkom-Modul-3-D07-2023/assets/105486369/42242342-62a8-4f58-aec1-aba5538a4c5e">


d. Generic Hash

<img width="557" alt="image" src="https://github.com/fihrizilhamr/Jarkom-Modul-3-D07-2023/assets/105486369/40daef9f-a10c-4ab3-8bff-b5de4c714c58">


<img width="746" alt="image" src="https://github.com/fihrizilhamr/Jarkom-Modul-3-D07-2023/assets/105486369/23a3deca-a334-4e97-a89c-8befa33a5c62">


<img width="746" alt="image" src="https://github.com/fihrizilhamr/Jarkom-Modul-3-D07-2023/assets/105486369/1e8e8953-abbe-4304-9386-cb03e1bdc1f4">


### Soal 9

Dengan menggunakan algoritma Round Robin, lakukan testing dengan menggunakan 3 worker, 2 worker, dan 1 worker sebanyak 100 request dengan 10 request/second, kemudian tambahkan grafiknya pada grimoire. (9)

**Jawaban**

Lakukan percobaan menggunakan _comment node worker_ di _load balancer_ yang disediakan pada bagian upworker. Kemudian saat melakukan running di client dengan command 

``ab -n 100 -c 10 http://www.granz.channel.d07.com/`` 

Jalankan htop pada masing masing worker.

#### a. 3 worker

<img width="542" alt="image" src="https://github.com/fihrizilhamr/Jarkom-Modul-3-D07-2023/assets/105486369/2c6e34d2-cd3a-4585-bcb2-34a747d107d5">

<img width="744" alt="image" src="https://github.com/fihrizilhamr/Jarkom-Modul-3-D07-2023/assets/105486369/59fc7a73-2f91-407d-be5a-aecdde37e2ff">


#### b. 2 worker

<img width="557" alt="image" src="https://github.com/fihrizilhamr/Jarkom-Modul-3-D07-2023/assets/105486369/4171ffbf-bad5-48da-b2aa-7b9773bc0b74">

<img width="747" alt="image" src="https://github.com/fihrizilhamr/Jarkom-Modul-3-D07-2023/assets/105486369/b77f4a39-e1d4-43ef-b739-7de080eb8a60">


#### c. 1 worker

<img width="555" alt="image" src="https://github.com/fihrizilhamr/Jarkom-Modul-3-D07-2023/assets/105486369/6511a317-a949-405e-9784-b7e2eacbc2c4">

<img width="743" alt="image" src="https://github.com/fihrizilhamr/Jarkom-Modul-3-D07-2023/assets/105486369/beef055b-94ba-4291-92d5-3cfd4a01533b">


#### d. Grafik
- 3 Worker : 187,69
- 2 Worker : 154,88
- 1 Worker : 259,02

<img width="1000" alt="image" src="https://github.com/fihrizilhamr/Jarkom-Modul-3-D07-2023/assets/105486369/b95384d6-5ced-440d-bc47-4a0f7e2aef39">


### Soal 10 - Soal 12

Selanjutnya coba tambahkan konfigurasi autentikasi di LB dengan dengan kombinasi username: “netics” dan password: “ajkyyy”, dengan yyy merupakan kode kelompok. Terakhir simpan file “htpasswd” nya di /etc/nginx/rahasisakita/ (10)

Lalu buat untuk setiap request yang mengandung /its akan di proxy passing menuju halaman https://www.its.ac.id. (11) hint: (proxy_pass)

Selanjutnya LB ini hanya boleh diakses oleh client dengan IP [Prefix IP].3.69, [Prefix IP].3.70, [Prefix IP].4.167, dan [Prefix IP].4.168. (12) hint: (fixed in dulu clinetnya)

**Jawaban**

#### Load Balancer (Eisen):

```
mkdir /etc/nginx/rahasisakita/
htpasswd -cb /etc/nginx/rahasisakita/.htpasswd netics ajkd07
service nginx restart
echo "
upstream backend  {
    server 10.25.3.1; #IP Lawine
    server 10.25.3.2; #IP Linie
    server 10.25.3.3; #IP Lugner
}

server {
    listen 80;
    server_name _;

    location / {
        allow 10.25.3.69;
        allow 10.25.3.70;
        allow 10.25.4.167;
        allow 10.25.4.168;
        deny all;

        proxy_pass http://backend;
        proxy_set_header    X-Real-IP \$remote_addr;
        proxy_set_header    X-Forwarded-For \$proxy_add_x_forwarded_for;
        proxy_set_header    Host \$http_host;

        auth_basic \"Administrator's Area\";
        auth_basic_user_file /etc/nginx/rahasisakita/.htpasswd;
    }

    location ~ /\.ht {
        deny all;
    }

    location /its {
        allow 10.25.3.69;
        allow 10.25.3.70;
        allow 10.25.4.167;
        allow 10.25.4.168;
        deny all;

        proxy_pass https://www.its.ac.id;

        auth_basic \"Administrator's Area\";
        auth_basic_user_file /etc/nginx/rahasisakita/.htpasswd;
    }

    error_log /var/log/nginx/granz_error.log;
    access_log /var/log/nginx/granz_access.log;
}
" > /etc/nginx/sites-available/granz.channel.d07.com

service nginx restart
```

1. `mkdir /etc/nginx/rahasisakita/`  
   - Membuat direktori `/etc/nginx/rahasisakita/` untuk menyimpan file konfigurasi autentikasi.

2. `htpasswd -cb /etc/nginx/rahasisakita/.htpasswd netics ajkd07`  
   - Membuat file `.htpasswd` dalam direktori `/etc/nginx/rahasisakita/` dan menambahkan user `netics` dan password `ajkd07` ke dalamnya.

3. `service nginx restart`  
   - Me-restart layanan Nginx agar konfigurasi yang baru diaplikasikan.

4. Mengkonfigurasi Nginx dengan autentikasi dasar:
   - `location /`:
     - Menetapkan alamat IP yang diizinkan untuk mengakses (`allow` dan `deny`).
     - Mengarahkan permintaan ke backend worker server dengan menggunakan `proxy_pass`.
     - Mengaktifkan autentikasi dasar dengan pesan "Administrator's Area" dan menggunakan file `.htpasswd` untuk user dam password.
   - `location /its`:
     - Sama seperti lokasi sebelumnya, tetapi mengarahkan permintaan ke `https://www.its.ac.id`.

5. `service nginx restart`  
   - Me-restart layanan Nginx setelah mengonfigurasi lokasi dan autentikasi.


#### Screenshots
> <img width="555" alt="image" src="https://github.com/fihrizilhamr/Jarkom-Modul-3-D07-2023/assets/116176265/f3739fdd-00ce-437a-96ef-9a17c6ae1dc7">

> <img width="555" alt="image" src="https://github.com/fihrizilhamr/Jarkom-Modul-3-D07-2023/assets/116176265/0c76d027-0ef4-4fa8-84d3-706c51cf0812">

> <img width="555" alt="image" src="https://github.com/fihrizilhamr/Jarkom-Modul-3-D07-2023/assets/116176265/ed10473e-3b6b-4e86-a978-eb5e3bb2f41a">

> <img width="555" alt="image" src="https://github.com/fihrizilhamr/Jarkom-Modul-3-D07-2023/assets/116176265/10e3ed0d-6248-4801-bf76-58e8a8d79a98">

> <img width="555" alt="image" src="https://github.com/fihrizilhamr/Jarkom-Modul-3-D07-2023/assets/116176265/bc48d95d-8b34-4089-985b-9b9687cdd97a">

> <img width="555" alt="image" src="https://github.com/fihrizilhamr/Jarkom-Modul-3-D07-2023/assets/116176265/acbf38a6-c391-4b87-b41a-670a33ebd1bb">

> <img width="555" alt="image" src="https://github.com/fihrizilhamr/Jarkom-Modul-3-D07-2023/assets/116176265/ed10473e-3b6b-4e86-a978-eb5e3bb2f41a">

> <img width="555" alt="image" src="https://github.com/fihrizilhamr/Jarkom-Modul-3-D07-2023/assets/116176265/10e3ed0d-6248-4801-bf76-58e8a8d79a98">

> <img width="555" alt="image" src="https://github.com/fihrizilhamr/Jarkom-Modul-3-D07-2023/assets/116176265/0d69c702-3079-4207-973c-6d9c59f7429e">



### Soal 13
Karena para petualang kehabisan uang, mereka kembali bekerja untuk mengatur riegel.canyon.yyy.com.

Semua data yang diperlukan, diatur pada Denken dan harus dapat diakses oleh Frieren, Flamme, dan Fern. (13)

**Jawaban**

#### Denken (Database Server):

```
echo "nameserver 192.168.122.1" > /etc/resolv.conf
apt-get update
apt-get install mariadb-server -y
service mysql start

mysql -u root -p <<EOF
CREATE USER 'kelompokd07'@'%' IDENTIFIED BY 'passwordd07';
CREATE USER 'kelompokd07'@'localhost' IDENTIFIED BY 'passwordd07';
CREATE DATABASE dbkelompokd07;
GRANT ALL PRIVILEGES ON *.* TO 'kelompokd07'@'%';
GRANT ALL PRIVILEGES ON *.* TO 'kelompokd07'@'localhost';
FLUSH PRIVILEGES;

exit
EOF

mysql -u kelompokd07 -p<<EOF
SHOW DATABASES; 
exit
EOF

echo "
[mysqld]
skip-networking=0
skip-bind-address" >> /etc/mysql/my.cnf

service mysql restart
```

1. Membuat user dan database baru, memberikan hak akses, dan menyimpan konfigurasi:
   - Membuat pengguna 'kelompokd07' dengan kata sandi 'passwordd07' yang dapat diakses dari semua host.
   - Membuat pengguna 'kelompokd07' dengan kata sandi 'passwordd07' yang dapat diakses hanya dari localhost.
   - Membuat database 'dbkelompokd07'.
   - Memberikan hak akses penuh kepada pengguna 'kelompokd07' untuk semua database dari semua host dan hanya localhost.
   - Me-restart MariaDB setelah konfigurasi.

2. Mengaktifkan akses jaringan dengan mengedit konfigurasi MariaDB di `/etc/mysql/my.cnf`:
   - `skip-networking=0` mengaktifkan akses jaringan.
   - `skip-bind-address` mengabaikan pengaturan bind address.

3. `service mysql restart`  
   - Me-restart layanan MariaDB setelah konfigurasi.

#### Laravel Worker (Frieren, Flamme, Fern):

```
echo "nameserver 192.168.122.1" > /etc/resolv.conf
apt-get update
apt-get install mariadb-client -y

mariadb --host=10.25.2.1 --port=3306 --user=kelompokd07 --password
```

1. Menggunakan klien MariaDB untuk terhubung ke server. 
   - Terhubung ke server MariaDB (`--host=10.25.2.1 --port=3306`) menggunakan username dan password yang telah dibuat sebelumnya.

#### Screenshots

><img width="555" alt="image" src="https://github.com/fihrizilhamr/Jarkom-Modul-3-D07-2023/assets/116176265/a90d6991-5896-43a4-9900-49401dd33e19">


### Soal 14

Frieren, Flamme, dan Fern memiliki Riegel Channel sesuai dengan quest guide berikut. Jangan lupa melakukan instalasi PHP8.0 dan Composer (14)

**Jawaban**

#### Laravel Worker (Frieren, Flamme, Fern):

```
echo "nameserver 192.168.122.1" > /etc/resolv.conf
apt-get update
apt-get install -y lsb-release ca-certificates apt-transport-https software-properties-common gnupg2

curl -sSLo /usr/share/keyrings/deb.sury.org-php.gpg https://packages.sury.org/php/apt.gpg

sh -c 'echo "deb [signed-by=/usr/share/keyrings/deb.sury.org-php.gpg] https://packages.sury.org/php/ $(lsb_release -sc) main" > /etc/apt/sources.list.d/php.list'

apt-get update
apt-get install php8.0-mbstring php8.0-xml php8.0-cli php8.0-common php8.0-intl php8.0-opcache php8.0-readline php8.0-mysql php8.0-fpm php8.0-curl unzip wget -y

apt-get install nginx -y

wget https://getcomposer.org/download/2.0.13/composer.phar
chmod +x composer.phar
mv composer.phar /usr/bin/composer

apt-get install git -y

cd /var/www/

git clone https://github.com/martuafernando/laravel-praktikum-jarkom.git

cd laravel-praktikum-jarkom

composer update

cp .env.example .env

echo "
APP_NAME=Laravel
APP_ENV=local
APP_KEY=
APP_DEBUG=true
APP_URL=http://localhost

LOG_CHANNEL=stack
LOG_DEPRECATIONS_CHANNEL=null
LOG_LEVEL=debug

DB_CONNECTION=mysql
DB_HOST=10.25.2.1
DB_PORT=3306
DB_DATABASE=dbkelompokd07
DB_USERNAME=kelompokd07
DB_PASSWORD=passwordd07

BROADCAST_DRIVER=log
CACHE_DRIVER=file
FILESYSTEM_DISK=local
QUEUE_CONNECTION=sync
SESSION_DRIVER=file
SESSION_LIFETIME=120

MEMCACHED_HOST=127.0.0.1

REDIS_HOST=127.0.0.1
REDIS_PASSWORD=null
REDIS_PORT=6379

MAIL_MAILER=smtp
MAIL_HOST=mailpit
MAIL_PORT=1025
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_ENCRYPTION=null
MAIL_FROM_ADDRESS="hello@example.com"
MAIL_FROM_NAME="\${APP_NAME}"

AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=
AWS_DEFAULT_REGION=us-east-1
AWS_BUCKET=
AWS_USE_PATH_STYLE_ENDPOINT=false

PUSHER_APP_ID=
PUSHER_APP_KEY=
PUSHER_APP_SECRET=
PUSHER_HOST=
PUSHER_PORT=443
PUSHER_SCHEME=https
PUSHER_APP_CLUSTER=mt1

VITE_PUSHER_APP_KEY="\${PUSHER_APP_KEY}"
VITE_PUSHER_HOST="\${PUSHER_HOST}"
VITE_PUSHER_PORT="\${PUSHER_PORT}"
VITE_PUSHER_SCHEME="\${PUSHER_SCHEME}"
VITE_PUSHER_APP_CLUSTER="\${PUSHER_APP_CLUSTER}"
" > /var/www/laravel-praktikum-jarkom/.env

php artisan migrate:fresh
php artisan db:seed --class=AiringsTableSeeder

php artisan key:generate
php artisan jwt:secret

echo "
server {
    listen 80;
    root /var/www/laravel-praktikum-jarkom/public;
    index index.php index.html index.htm;
    server_name _;

    location / {
        try_files \$uri \$uri/ /index.php?\$query_string;
    }

    # pass PHP scripts to FastCGI server
    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php8.0-fpm.sock;
    }

    location ~ /\.ht {
        deny all;
    }

    error_log /var/log/nginx/riegel_error.log;
    access_log /var/log/nginx/riegel_access.log;
}" > /etc/nginx/sites-available/riegel.canyon.d07.com

ln -s /etc/nginx/sites-available/riegel.canyon.d07.com /etc/nginx/sites-enabled/

chown -R www-data:www-data /var/www/laravel-praktikum-jarkom/storage

rm -rf /etc/nginx/sites-enabled/default

service php8.0-fpm stop
service php8.0-fpm start
service nginx restart
```


1. Mengunduh dan menambahkan kunci repositori PHP:

   ```bash
   curl -sSLo /usr/share/keyrings/deb.sury.org-php.gpg https://packages.sury.org/php/apt.gpg
   sh -c 'echo "deb [signed-by=/usr/share/keyrings/deb.sury.org-php.gpg] https://packages.sury.org/php/ $(lsb_release -sc) main" > /etc/apt/sources.list.d/php.list'
   ```

2. `apt-get install php8.0-mbstring php8.0-xml php8.0-cli php8.0-common php8.0-intl php8.0-opcache php8.0-readline php8.0-mysql php8.0-fpm php8.0-curl unzip wget -y`  
   - Menginstal PHP 8.0 dan beberapa ekstensi yang dibutuhkan.

3. Mengunduh dan menginstal Composer:

   ```bash
   wget https://getcomposer.org/download/2.0.13/composer.phar
   chmod +x composer.phar
   mv composer.phar /usr/bin/composer
   ```

4.  `git clone https://github.com/martuafernando/laravel-praktikum-jarkom.git`  
    - Mengunduh repositori Laravel dari GitHub.

5.  `composer update`  
    - Memperbarui dependensi Laravel menggunakan Composer.

6.  Membuat salinan file `.env.example` menjadi `.env`.

7.  Mengonfigurasi file `.env` dengan pengaturan database dan lainnya.

8.  `php artisan migrate:fresh`  
    - Menjalankan migrasi basis data.

9.  `php artisan db:seed --class=AiringsTableSeeder`  
    - Menjalankan seeder untuk mengisi basis data.

10. `php artisan key:generate`  
    - Menghasilkan kunci aplikasi Laravel.

11. `php artisan jwt:secret`  
    - Menghasilkan kunci rahasia untuk JWT.

12. Membuat konfigurasi Nginx untuk aplikasi Laravel di `/etc/nginx/sites-available/riegel.canyon.d07.com`.

13. Membuat symbolic link untuk konfigurasi baru di `/etc/nginx/sites-enabled/`.

14. Menyesuaikan kepemilikan direktori penyimpanan Laravel.

15. Menghapus konfigurasi default Nginx.

16. Me-restart layanan PHP-FPM dan Nginx.


#### Screenshots

> <img width="555" alt="image" src="https://github.com/fihrizilhamr/Jarkom-Modul-3-D07-2023/assets/116176265/77e4eb52-5e11-4632-8c91-92344b0d7c44">

### Soal 15 - Soal 17

Riegel Channel memiliki beberapa endpoint yang harus ditesting sebanyak 100 request dengan 10 request/second. Tambahkan response dan hasil testing pada grimoire.
POST /auth/register (15)
POST /auth/login (16)
GET /me (17)

**Jawaban**

### Soal 18

Untuk memastikan ketiganya bekerja sama secara adil untuk mengatur Riegel Channel maka implementasikan Proxy Bind pada Eisen untuk mengaitkan IP dari Frieren, Flamme, dan Fern. (18)

**Jawaban**

#### Load Balancer (Eisen):

```
echo "
upstream laravel {
    server 10.25.4.1; # IP Frieren
    server 10.25.4.2; # IP Flamme
    server 10.25.4.3; # IP Fern
}

server {
    listen 80;
    server_name riegel.canyon.d07.com;

    location / {
        proxy_pass http://laravel;
        proxy_bind 10.25.2.2;  # IP Eisen
    }
    error_log /var/log/nginx/riegel_error.log;
    access_log /var/log/nginx/riegel_access.log;
}
" > /etc/nginx/sites-available/riegel.canyon.d07.com

ln -s /etc/nginx/sites-available/riegel.canyon.d07.com /etc/nginx/sites-enabled

service bind9 restart
service nginx restart
```

1. Mengkonfigurasi Nginx untuk load balancing pada `/etc/nginx/sites-available/riegel.canyon.d07.com`.

2. Mengimplementasikan proxy bind pada load balancer. `proxy_bind 10.25.2.2;`, menetapkan alamat IP yang akan digunakan oleh Nginx untuk membuat koneksi ke backend server.

3. Membuat symbolic link untuk konfigurasi baru di `/etc/nginx/sites-enabled/`.

4. Me-restart layanan Nginx.

#### DNS Server (Heither):

```
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
@       IN      A       10.25.2.2 ; IP Eisen
www     IN      CNAME   riegel.canyon.d07.com.
```

1. Mengkonfigurasi zona DNS di `/etc/bind/riegel/riegel.canyon.d07.com`:
   
2. Me-restart layanan BIND9.


#### Screenshots

> <img width="555" alt="image" src="https://github.com/fihrizilhamr/Jarkom-Modul-3-D07-2023/assets/116176265/604f1a88-6020-4b27-af4a-9ba66e8e19cc">

### Soal 19

Untuk meningkatkan performa dari Worker, coba implementasikan PHP-FPM pada Frieren, Flamme, dan Fern. Untuk testing kinerja naikkan 
- pm.max_children
- pm.start_servers
- pm.min_spare_servers
- pm.max_spare_servers

sebanyak tiga percobaan dan lakukan testing sebanyak 100 request dengan 10 request/second kemudian berikan hasil analisisnya pada Grimoire.(19)

**Jawaban**

#### Laravel Worker (Frieren, Flamme, Fern):

```
echo "
[www]
user = www-data
group = www-data
listen = /run/php/php8.0-fpm.sock
listen.owner = www-data
listen.group = www-data
php_admin_value[disable_functions] = exec,passthru,shell_exec,system
php_admin_flag[allow_url_fopen] = off

; Choose how the process manager will control the number of child processes.

pm = dynamic
pm.max_children = 75
pm.start_servers = 10
pm.min_spare_servers = 5
pm.max_spare_servers = 20
pm.process_idle_timeout = 10s " > /etc/php/8.0/fpm/pool.d/www.conf

service php8.0-fpm stop
service php8.0-fpm start
service nginx restart
```

1. Membuat atau mengedit konfigurasi PHP-FPM pada `/etc/php/8.0/fpm/pool.d/www.conf`.
   - `[www]`: Ini adalah blok konfigurasi untuk pool PHP-FPM yang bernama `www`.
   - `user` dan `group`: Menentukan pengguna dan grup yang akan digunakan oleh PHP-FPM process pool.
   - `listen`: Menunjukkan tempat PHP-FPM akan mendengarkan permintaan. Dalam hal ini, menggunakan `/run/php/php8.0-fpm.sock`.
   - `listen.owner` dan `listen.group`: Menetapkan pemilik dan grup untuk socket yang didengarkan oleh PHP-FPM.
   - `php_admin_value[disable_functions]`: Menyaring fungsi-fungsi PHP tertentu untuk keamanan. 
   - `php_admin_flag[allow_url_fopen]`: Menetapkan apakah fungsi `allow_url_fopen` diaktifkan atau tidak. 
   - Konfigurasi lainnya, seperti pengaturan manajemen proses (pm), jumlah maksimum anak proses (pm.max_children), dan pengaturan lainnya.

2. Menghentikan layanan PHP-FPM dan memulai ulang untuk menerapkan konfigurasi baru.

3. Me-restart layanan Nginx untuk memastikan bahwa perubahan pada PHP-FPM diakui:

#### Screenshots
>



### Soal 20

Nampaknya hanya menggunakan PHP-FPM tidak cukup untuk meningkatkan performa dari worker maka implementasikan Least-Conn pada Eisen. Untuk testing kinerja dari worker tersebut dilakukan sebanyak 100 request dengan 10 request/second. (20)


**Jawaban**

#### Load Balancer (Eisen):

```
echo "
upstream laravel {
    least_conn;
    server 10.25.4.1; # IP Frieren
    server 10.25.4.2; # IP Flamme
    server 10.25.4.3; # IP Fern
}

server {
    listen 80;
    server_name riegel.canyon.d07.com;

    location / {
        proxy_pass http://laravel;
        proxy_bind 10.25.2.2;  # IP Eisen
    }

    error_log /var/log/nginx/riegel_error.log;
    access_log /var/log/nginx/riegel_access.log;
}
" > /etc/nginx/sites-available/riegel.canyon.d07.com

ln -s /etc/nginx/sites-available/riegel.canyon.d07.com /etc/nginx/sites-enabled

service bind9 restart
service nginx restart
```

1.  `upstream laravel`: Menentukan grup backend server dengan menggunakan algoritma `least_conn`. Ini berarti setiap permintaan baru akan diberikan ke server yang memiliki jumlah koneksi yang paling sedikit.

2.    `service bind9 restart`: Me-restart layanan BIND9 untuk mengenali konfigurasi DNS baru jika ada perubahan.

3.    `service nginx restart`: Me-restart layanan Nginx untuk menerapkan konfigurasi Load Balancer baru.

#### Screenshots
> 

## Kendala
Tidak ada kendala yang signifikan. 
