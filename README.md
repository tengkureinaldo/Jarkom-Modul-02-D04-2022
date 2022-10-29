# Jarkom-Modul-2-D04-2022

## Anggota Kelompok
| **Nama** | **NRP** |
| ------ | ------ |
| Muhammad Ghani Taufiqurrahman Atmaja | 5025201110 |
| Tengku Fredly Reinaldo | 5025201198 |
| Shaula Aljauhara Riyadi | 5025201265 |

## Intro
Twilight (〈黄昏 (たそがれ) 〉, <Tasogare>) adalah seorang mata-mata yang berasal dari negara Westalis. Demi menjaga perdamaian antara Westalis dengan Ostania, Twilight dengan nama samaran Loid Forger (ロイド・フォージャー, Roido Fōjā) di bawah organisasi WISE menjalankan operasinya di negara Ostania dengan cara melakukan spionase, sabotase, penyadapan dan kemungkinan pembunuhan. Berikut adalah peta dari negara Ostania:
	
<p align="center">
  <img src="https://user-images.githubusercontent.com/56400536/198799381-5474e30d-500c-435d-87e8-c3e712452bcd.jpg"> 
</p>

WISE akan dijadikan sebagai DNS Master, Berlint akan dijadikan DNS Slave, dan Eden akan digunakan sebagai Web Server. Terdapat 2 Client yaitu SSS, dan Garden. 

**Sebelum** kita mulai mengerjakan soal, buatlah beberapa node ethernet switch dan ubuntu serta hubungan antar node seperti pada gambar di atas.  Setelah itu, setting network masing-masing node dengan fitur ```Edit network configuration``` lalu hapus semua settingnya dan isi dengan settingan di bawah:

* Ostania

```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 10.17.1.1
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 10.17.2.1
	netmask 255.255.255.0

auto eth3
iface eth3 inet static
	address 10.17.3.1
	netmask 255.255.255.0
```

* WISE

```
auto eth0
iface eth0 inet static
	address 10.17.1.2
	netmask 255.255.255.0
	gateway 10.17.1.1
```

* SSS

```
auto eth0
iface eth0 inet static
	address 10.17.2.2
	netmask 255.255.255.0
	gateway 10.17.2.1
```

* Garden

```
auto eth0
iface eth0 inet static
	address 10.17.2.3
	netmask 255.255.255.0
	gateway 10.17.2.1
```

* Berlint

```
auto eth0
iface eth0 inet static
	address 10.17.3.2
	netmask 255.255.255.0
	gateway 10.17.3.1
```

* Eden

```
auto eth0
iface eth0 inet static
	address 10.17.3.3
	netmask 255.255.255.0
	gateway 10.17.3.1
```

Setelah itu, tambahkan isi file ```root/.bashrc``` pada masing-masing node dengan command di bawah ini:

* Ostania

```
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.17.0.0/16
```

* WISE dan node lainnya

```
echo nameserver 192.168.122.1 > /etc/resolv.conf
apt-get update
apt-get install bind9 -y
apt-get install dnsutils
```

***Semua script yang akan ditampilkan di bawah sudah disimpan ke dalam file shell script di ```/root``` masing-masing node. Yang perlu anda lakukan hanyalah menjalankan file .sh tersebut, sesuai dengan nomor yang akan dikerjakan.
	
## Soal 1
Semua node terhubung pada router Ostania, sehingga dapat mengakses internet. 
### Jawab
Kita akan mengetes apakah semua node sudah terhubung dengan jaringan internet dengan cara melakukan ping ke google

> kita dapat langsung menjalankannya dengan melakukan command ```bash soal1.sh```

* Semua node

```
ping google.com -c 5
```

<p float="left" align="center">
  <img src="https://user-images.githubusercontent.com/56400536/198816020-a247e7a8-48df-49c9-86da-d4c62338be54.png" width=48% height=48%> 
  <img src="https://user-images.githubusercontent.com/56400536/198815981-c7b3e585-0033-4486-95f8-9d9f1a823083.png" width=48% height=48%>
  <img src="https://user-images.githubusercontent.com/56400536/198816083-57589d6f-0ac0-4ec7-a6a1-b031f0146fca.png" width=48% height=48%> 
  <img src="https://user-images.githubusercontent.com/56400536/198816086-1a6bb8cd-6d81-438d-8d3a-6ef732fd5840.png" width=48% height=48%>
  <img src="https://user-images.githubusercontent.com/56400536/198816094-dfd329be-2791-4075-97ae-c1f4c775fa1e.png" width=48% height=48%> 
  <img src="https://user-images.githubusercontent.com/56400536/198816100-4d5c6f4d-a970-429d-a2db-23d3ec618c0e.png" width=48% height=48%>
</p>

## Soal 2
Untuk mempermudah mendapatkan informasi mengenai misi dari Handler, bantulah Loid membuat website utama dengan akses wise.yyy.com dengan alias www.wise.yyy.com pada folder wise.
### Jawab
Pertama, dalam node WISE, kita harus mengonfigurasikan ```/etc/bind/named.conf.local``` dengan domain wise.D04.com. Kemudian buatlah direktori *wise* dengan ```mkdir /etc/bind/wise```. Setelah itu, salinlah ```/etc/bind/db.local``` ke dalam ```/etc/bind/wise/wise.D04.com```. Lalu isilah ```/etc/bind/wise/wise.D04.com``` sesuai dengan *script* yang ada di bawah ini. Kemudian *restart* bind9 dengan ```service bind9 restart```

> kita dapat langsung menjalankannya dengan melakukan command ```bash soal2.sh```

* WISE 

```
echo -e '
zone "wise.D04.com" {
        type master;
        file "/etc/bind/wise/wise.D04.com";
};
 ' > /etc/bind/named.conf.local

mkdir /etc/bind/wise

cp /etc/bind/db.local /etc/bind/wise/wise.D04.com

echo -e '
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     wise.D04.com. root.wise.D04.com. (
                     2022102601         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      wise.D04.com.
@       IN      A       10.17.1.2       ; IP WISE
www     IN      CNAME   wise.D04.com.
@       IN      AAAA    ::1
' > /etc/bind/wise/wise.D04.com

service bind9 restart
```
Kita dapat mengetes konfigurasi di atas dengan menjalankan ```bash soal2.sh``` di node SSS dan Garden (Client)
	
* SSS dan Garden

```
echo -e '
nameserver 10.17.1.2          ; IP WISE
' > /etc/resolv.conf

host -t CNAME www.wise.D04.com

ping www.wise.D04.com -c 5
```

<p float="left" align="center">
  <img src="https://user-images.githubusercontent.com/56400536/198816762-3f6d29bf-5722-4294-85f3-52f12b555a7c.png" width=48% height=48%> 
  <img src="https://user-images.githubusercontent.com/56400536/198816768-4d2d9918-4c69-4e24-b1d3-b1dd163414cb.png" width=48% height=48%>
</p>

## Soal 3
Setelah itu ia juga ingin membuat subdomain eden.wise.yyy.com dengan alias www.eden.wise.yyy.com yang diatur DNS-nya di WISE dan mengarah ke Eden.
### Jawab
Pertama, dalam node WISE, kita tambahkan ```eden IN A 10.17.3.3 ; IP Eden``` dan ```www.eden IN CNAME eden.wise.D04.com.``` ke dalam ```/etc/bind/wise/wise.D04.com```. Kemudian *restart* bind9 dengan ```service bind9 restart```

> kita dapat langsung menjalankannya dengan melakukan command ```bash soal3.sh```
	
* WISE
	
```
echo -e '
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     wise.D04.com. root.wise.D04.com. (
                     2022102601         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@               IN      NS      wise.D04.com.
@               IN      A       10.17.1.2             ; IP WISE
www             IN      CNAME   wise.D04.com.
eden            IN      A       10.17.3.3             ; IP Eden
www.eden        IN      CNAME   eden.wise.D04.com.
@               IN      AAAA    ::1
' > /etc/bind/wise/wise.D04.com

service bind9 restart
```
	
Kita dapat mengetes konfigurasi di atas dengan menjalankan ```bash soal3.sh``` di node SSS dan Garden (Client)
	
* SSS dan Garden
	
```
host -t CNAME www.eden.wise.D04.com

ping www.eden.wise.D04.com -c 5
```

<p float="left" align="center">
  <img src="https://user-images.githubusercontent.com/56400536/198821186-deb5ac40-f1c4-47f9-92cd-4ca459b80b0f.png" width=48% height=48%> 
  <img src="https://user-images.githubusercontent.com/56400536/198821192-09f23816-2e7b-43c3-aa0a-39a1b68cd5d8.png" width=48% height=48%>
</p>

## Soal 4
Buat juga reverse domain untuk domain utama.
### Jawab
Pertama, dalam node WISE, kita tambahkan konfigurasi dengan menambahkan reverse dari 3 byte awal dari IP yang ingin dilakukan Reverse DNS (10.17.1) ke dalam ```/etc/bind/named.conf.local```. Kemudian, salin ```/etc/bind/db.local``` ke dalam ```/etc/bind/wise/1.17.10.in-addr.arpa```. Lalu sesuaikan isi ```/etc/bind/wise/1.17.10.in-addr.arpa``` dengan *script* yang ada di bawah. Kemudian *restart* bind9 dengan ```service bind9 restart```
	
> kita dapat langsung menjalankannya dengan melakukan command ```bash soal4.sh```

* WISE

```
echo -e '
zone "wise.D04.com" {
        type master;
        file "/etc/bind/wise/wise.D04.com";
};

zone "1.17.10.in-addr.arpa" {
    type master;
    file "/etc/bind/wise/1.17.10.in-addr.arpa";
};

 ' > /etc/bind/named.conf.local

cp /etc/bind/db.local /etc/bind/wise/1.17.10.in-addr.arpa

echo -e '
$TTL    604800
@       IN      SOA     wise.D04.com. root.wise.D04.com. (
                     2022102601         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
1.17.10.in-addr.arpa.    IN     NS      wise.D04.com.
2                        IN     PTR     wise.D04.com.
' > /etc/bind/wise/1.17.10.in-addr.arpa

service bind9 restart
```

Kita dapat mengetes konfigurasi di atas dengan menjalankan ```bash soal4.sh``` di node SSS dan Garden (Client)
	
* SSS dan Garden
	
```
host -t PTR 10.17.1.2
```

<p float="left" align="center">
  <img src="https://user-images.githubusercontent.com/56400536/198821891-a6efbae5-4449-40a4-9eb5-b6f39021ed5b.png" width=48% height=48%> 
  <img src="https://user-images.githubusercontent.com/56400536/198821899-cce6bdae-1836-46d1-9958-5acc59da5645.png" width=48% height=48%>
</p>
	
## Soal 5
Agar dapat tetap dihubungi jika server WISE bermasalah, buatlah juga Berlint sebagai DNS Slave untuk domain utama.
### Jawab
Pertama, dalam node WISE, kita dapat menambahkan konfigurasi seperti *script* yang ada di bawah ini ke dalam ```/etc/bind/named.conf.local```. Kemudian *restart* bind9 dengan ```service bind9 restart```
	
> kita dapat langsung menjalankannya dengan melakukan *command* ```bash soal5.sh```

* WISE

```
echo -e '
zone "wise.D04.com" {
        type master;
        notify yes;
        also-notify { 10.17.3.2; }; // Masukan IP Berlint tanpa tanda petik
        allow-transfer { 10.17.3.2; }; // Masukan IP Berlint tanpa tanda petik
        file "/etc/bind/wise/wise.D04.com";
};

zone "1.17.10.in-addr.arpa" {
    type master;
    file "/etc/bind/wise/1.17.10.in-addr.arpa";
};

 ' > /etc/bind/named.conf.local

service bind9 restart
```
	
Kemudian, dalam node Berlint, kita dapat menambahkan konfigurasi seperti *script* yang ada di bawah ini ke dalam ```/etc/bind/named.conf.local```. Kemudian *restart* bind9 dengan ```service bind9 restart```
	
> kita dapat langsung menjalankannya dengan melakukan *command* ```bash soal5.sh```
	
* Berlint

```
echo -e '
zone "wise.D04.com" {
    type slave;
    masters { 10.17.1.2; }; // Masukan IP WISE tanpa tanda petik
    file "/var/lib/bind/wise.D04.com";
};
 ' > /etc/bind/named.conf.local

service bind9 restart
```
	
Untuk mengetes apabila konfigurasi di atas sudah berhasil, kita akan menonaktifkan *service* bind9 pada node WISE terlebih dahulu dengan ```service bind9 stop```. Kemudian kita beralih ke node SSS dan Garden untuk menjalankan *command* ```bash soal5.sh```
	
* SSS dan Garden
	
```
echo -e '
nameserver 10.17.1.2          ; IP WISE
nameserver 10.17.3.2          ; IP Berlint
' > /etc/resolv.conf

ping wise.D04.com -c 5
```
	
<p float="left" align="center">
  <img src="https://user-images.githubusercontent.com/56400536/198822379-935c8899-4d4e-4577-aacf-e0bb7e8b5cae.png" width=48% height=48%> 
  <img src="https://user-images.githubusercontent.com/56400536/198822373-1dbb0f8c-89b7-46ac-a459-87815f380d00.png" width=48% height=48%>
</p>
	
## Soal 6
Karena banyak informasi dari Handler, buatlah subdomain yang khusus untuk operation yaitu operation.wise.yyy.com dengan alias www.operation.wise.yyy.com yang didelegasikan dari WISE ke Berlint dengan IP menuju ke Eden dalam folder operation.
### Jawab
Pertama, dalam node WISE, tambahkan ```ns1 IN A 10.17.3.2 ; IP Berlint``` dan ```operation N NS ns1```  ke dalam ```/etc/bind/wise/wise.D04.com```. Lalu buka ```/etc/bind/named.conf.options``` untuk meng-*comment* ```dnssec-validation auto;``` dan menambahkan ```allow-query{any;};``` pada file tersebut. Setelah itu, sesuaikan konfigurasi pada ```/etc/bind/named.conf.local``` agar sesuai dengan *script* di bawah. Kemudian *restart* bind9 dengan ```service bind9 restart```
	
> kita dapat langsung menjalankannya dengan melakukan *command* ```bash soal6.sh```
	
* WISE
	
```
echo -e '
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     wise.D04.com. root.wise.D04.com. (
                     2022102601         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@                       IN      NS      wise.D04.com.
@                       IN      A       10.17.1.2             ; IP WISE
www                     IN      CNAME   wise.D04.com.
eden                    IN      A       10.17.3.3             ; IP Eden
www.eden                IN      CNAME   eden.wise.D04.com.
ns1                     IN      A       10.17.3.2             ; IP Berlint
operation               IN      NS      ns1
@                       IN      AAAA    ::1
' > /etc/bind/wise/wise.D04.com

echo -e '
options {
        directory "/var/cache/bind";

        //forwarders {
        //     0.0.0.0;
        //};
	
        //dnssec-validation auto;
        allow-query{any;};

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
};
' > /etc/bind/named.conf.options
	
echo -e '
zone "wise.D04.com" {
        type master;
        notify yes;
        also-notify { 10.17.3.2; }; // Masukan IP Berlint tanpa tanda petik
        allow-transfer { 10.17.3.2; }; // Masukan IP Berlint tanpa tanda petik
        file "/etc/bind/wise/wise.D04.com";
};

zone "1.17.10.in-addr.arpa" {
    type master;
    file "/etc/bind/wise/1.17.10.in-addr.arpa";
};
' > /etc/bind/named.conf.local

service bind9 restart
```
	
Setelah itu, pada node Berlint, *comment* ```dnssec-validation auto;``` dan tambahkan ```allow-query{any;};``` pada ```/etc/bind/named.conf.options```. Lalu buka  ```/etc/bind/named.conf.local``` dan sesuaikan isinya seperti *script* di bawah. Kemudian buatlah direktori *operation* dengan ```mkdir /etc/bind/operation```. Setelah itu, salin ```/etc/bind/db.local``` ke dalam ```/etc/bind/operation/operation.wise.D04.com```. Lalu sesuaikan isi ```/etc/bind/operation/operation.wise.D04.com``` seperti pada *script* di bawah. Kemudian *restart* bind9 dengan ```service bind9 restart```
	
> kita dapat langsung menjalankannya dengan melakukan *command* ```bash soal6.sh```
	
* Berlint

```
echo -e '
options {
        directory "/var/cache/bind";

        // forwarders {
        //      0.0.0.0;
        // };

        //dnssec-validation auto;
        allow-query{any;};

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
};
' > /etc/bind/named.conf.options

echo -e '
zone "wise.D04.com" {
    type slave;
    masters { 10.17.1.2; }; // Masukan IP WISE tanpa tanda petik
    file "/var/lib/bind/wise.D04.com";
};

zone "operation.wise.D04.com" {
        type master;
        file "/etc/bind/operation/operation.wise.D04.com";
};
' > /etc/bind/named.conf.local
	
mkdir /etc/bind/operation

cp /etc/bind/db.local /etc/bind/operation/operation.wise.D04.com

echo -e '
$TTL    604800
@       IN      SOA     operation.wise.D04.com. root.operation.wise.D04.com. (
                     2022102601         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
	                2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@               IN      NS          operation.wise.D04.com.
@               IN      A           10.17.3.3                 ; IP Eden
www             IN      CNAME       operation.wise.D04.com.
' > /etc/bind/operation/operation.wise.D04.com

service bind9 restart
```
	
Kita dapat mengetes konfigurasi di atas dengan menjalankan ```bash soal6.sh``` di node SSS dan Garden (Client)
	
* SSS dan Garden
	
```
host -t CNAME www.operation.wise.D04.com

ping operation.wise.D04.com -c 5
```
	
<p float="left" align="center">
  <img src="https://user-images.githubusercontent.com/56400536/198823391-dc6d8e23-01fa-42d0-9b31-86d66e71319e.png" width=48% height=48%> 
  <img src="https://user-images.githubusercontent.com/56400536/198823394-52d8e4b1-c17b-4cf7-9b36-8af18e6799af.png" width=48% height=48%>
</p>
	
## Soal 7
Untuk informasi yang lebih spesifik mengenai Operation Strix, buatlah subdomain melalui Berlint dengan akses strix.operation.wise.yyy.com dengan alias www.strix.operation.wise.yyy.com yang mengarah ke Eden.
### Jawab
Pertama, pada node Berlint, tambahkan ```strix IN A 10.17.3.3``` dan ```www.strix IN CNAME strix.operation.wise.D04.com.``` ke dalam ```/etc/bind/operation/operation.wise.D04.com```. Kemudian *restart* bind9 dengan ```service bind9 restart```
	
> kita dapat langsung menjalankannya dengan melakukan *command* ```bash soal7.sh```
	
* Berlint
	
```
echo -e '
$TTL    604800
@       IN      SOA     operation.wise.D04.com. root.operation.wise.D04.com. (
                     2022102601         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@               IN      NS              operation.wise.D04.com.
@               IN      A               10.17.3.3
www             IN      CNAME           operation.wise.D04.com.
strix           IN      A               10.17.3.3
www.strix       IN      CNAME           strix.operation.wise.D04.com.
' > /etc/bind/operation/operation.wise.D04.com

service bind9 restart
```

Kita dapat mengetes konfigurasi di atas dengan menjalankan ```bash soal7.sh``` di node SSS dan Garden (Client)
	
* SSS dan Garden
	
```
ping strix.operation.wise.D04.com -c 5

ping www.strix.operation.wise.D04.com -c 5
```
	
<p float="left" align="center">
  <img src="https://user-images.githubusercontent.com/56400536/198823760-bea2b5fc-655c-412c-81c9-dd4347e8fe32.png" width=48% height=48%> 
  
## Soal 8
Setelah melakukan konfigurasi server, maka dilakukan konfigurasi Webserver. Pertama dengan webserver www.wise.yyy.com. Pertama, Loid membutuhkan webserver dengan DocumentRoot pada /var/www/wise.yyy.com 

### Jawab

## Soal 9
Setelah itu, Loid juga membutuhkan agar url www.wise.yyy.com/index.php/home dapat menjadi menjadi www.wise.yyy.com/home

### Jawab

## Soal 10
Setelah itu, pada subdomain www.eden.wise.yyy.com, Loid membutuhkan penyimpanan aset yang memiliki DocumentRoot pada /var/www/eden.wise.yyy.com

### Jawab

## Soal 11
Akan tetapi, pada folder /public, Loid ingin hanya dapat melakukan directory listing saja 

### Jawab

## Soal 12
Tidak hanya itu, Loid juga ingin menyiapkan error file 404.html pada folder /error untuk mengganti error kode pada apache

### Jawab
  
## Soal 13
Loid juga meminta Franky untuk dibuatkan konfigurasi virtual host. Virtual host ini bertujuan untuk dapat mengakses file asset www.eden.wise.yyy.com/public/js menjadi www.eden.wise.yyy.com/js

### Jawab

## Soal 14
Loid meminta agar www.strix.operation.wise.yyy.com hanya bisa diakses dengan port 15000 dan port 15500

### Jawab

## Soal 15
dengan autentikasi username Twilight dan password opStrix dan file di /var/www/strix.operation.wise.yyy

### Jawab

## Soal 16
dan setiap kali mengakses IP Eden akan dialihkan secara otomatis ke www.wise.yyy.com 

### Jawab

## Soal 17
Karena website www.eden.wise.yyy.com semakin banyak pengunjung dan banyak modifikasi sehingga banyak gambar-gambar yang random, maka Loid ingin mengubah request gambar yang memiliki substring “eden” akan diarahkan menuju eden.png. Bantulah Agent Twilight dan Organisasi WISE menjaga perdamaian! 

### Jawab

