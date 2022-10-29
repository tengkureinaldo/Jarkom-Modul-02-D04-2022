# Jarkom-Modul-2-D04-2022

## Anggota Kelompok
| **Nama** | **NRP** |
| ------ | ------ |
| Muhammad Ghani Taufiqurrahman Atmaja | 5025201110 |
| Tengku Fredly Reinaldo | 5025201198 |
| Shaula Aljauhara Riyadi | 5025201265 |

## Intro
Twilight (〈黄昏 (たそがれ) 〉, <Tasogare>) adalah seorang mata-mata yang berasal
dari negara Westalis. Demi menjaga perdamaian antara Westalis dengan Ostania, Twilight
dengan nama samaran Loid Forger (ロイド・フォージャー, Roido Fōjā) di bawah organisasi
WISE menjalankan operasinya di negara Ostania dengan cara melakukan spionase,
sabotase, penyadapan dan kemungkinan pembunuhan. Berikut adalah peta dari negara
Ostania:

![topologi_modul2](https://user-images.githubusercontent.com/56400536/198799381-5474e30d-500c-435d-87e8-c3e712452bcd.jpg)

WISE akan dijadikan sebagai DNS Master, Berlint akan dijadikan DNS Slave, dan Eden
akan digunakan sebagai Web Server. Terdapat 2 Client yaitu SSS, dan Garden. 

**Sebelum** kita mulai mengerjakan soal, buatlah beberapa node ethernet switch dan ubuntu serta hubungan antar node seperti pada gambar di atas. 
Setelah itu, setting network masing-masing node dengan fitur ```Edit network configuration``` lalu hapus semua settingnya dan isi dengan settingan di bawah

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
## Soal 1
Semua node terhubung pada router Ostania, sehingga dapat mengakses internet 
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
Untuk mempermudah mendapatkan informasi mengenai misi dari Handler, bantulah
Loid membuat website utama dengan akses wise.yyy.com dengan alias
www.wise.yyy.com pada folder wise
### Jawab
Pertama, dalam node WISE, kita harus mengonfigurasikan ```/etc/bind/named.conf.local``` dengan domain wise.D04.com. Kemudian buatlah direktori *wise* dengan command
```mkdir /etc/bind/wise```. Setelah itu, salinlah ```/etc/bind/db.local``` ke dalam ```/etc/bind/wise/wise.D04.com```. Kemudian Isilah ```/etc/bind/wise/wise.D04.com```
sesuai dengan *script* yang ada di bawah ini

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
Kemudian kita dapat mengetes konfigurasi di atas dengan menjalankan ```bash soal2.sh``` di node SSS dan Garden (Client)

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
Setelah itu ia juga ingin membuat subdomain
eden.wise.yyy.com dengan alias www.eden.wise.yyy.com yang diatur DNS-nya di WISE
dan mengarah ke Eden
### Jawab


  

