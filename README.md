# Jarkom-Modul-1-2025-K19
| Nama | NRP |
|--------|--------|
| Yuan Banny Albyan  | 5027241027  |
| Nafis  | 5027241  |

# SOAL1
- membuat topologi agar ERU {eth1,eth2} tersambung ke {Switch1, Switch2}
- Switch1 {eth0, eth1, eth2} tersambung ke {ERU, Melkor, Manwe}
- Switch2 {eth0, eth1, eth2} tersambung ke {ERU, Varda, Ulmo}

---

# SOAL2
- ERU{eth0} tersambung ke {NAT1}
> configuration ERU
```c
auto eth0
iface eth0 inet dhcp
```
---
# SOAL3
> configuration ERU
```c
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 10.73.1.1
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 10.73.2.1
	netmask 255.255.255.0
```

> configuration Melkor
```c
auto eth0
iface eth0 inet static
	address10.73.1.2
	netmask 255.255.255.0
	gateway 10.73.1.1
```

> configuration Manwe
```c
auto eth0
iface eth0 inet static
	address10.73.1.3
	netmask 255.255.255.0
	gateway 10.73.1.1
```

> configuration Varda
```c
auto eth0
iface eth0 inet static
	address10.73.2.2
	netmask 255.255.255.0
	gateway 10.73.2.1
```

> configuration Ulno
```c
auto eth0
iface eth0 inet static
	address10.73.2.3
	netmask 255.255.255.0
	gateway 10.73.2.1
```
---
# SOAL4
*tambahkan dns-nameservers 8.8.8.8 di masing masing client*
- Melkor dan Manwe terhubung ke switch1 yang terhubung ke ip 10.73.1.1 nya ERU, sedangkan 
- Varda dan Ulmo terhubung ke switch2 yang terhubung ke ip 10.73.2.1 nya ERU
- masing masing client dibuat static IP nya agar lebih mudah saat mengerjakan soal nya nanti
- IP Address = alamat IP nya yang mau dibikin static untuk tiap client
- Netmask = menentukan ukuran network (berapa banyak host yang bisa ada dalam subnet)
- Gateway = Pintu keluar dari subnet ke jaringan lain
- DNS = Domain Name System = Menerjemahkan nama domain (google.com) menjadi IP address (142.250.185.46)
- DNS Server 8.8.8.8 = Google Public DNS
---
# SOAL 5
*edit .bashrc masing masing node seperti ini*
di ndoe eru
```c
apt-get update
apt-get install iptables -y
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.73.0.0/16
```

di ndoe melkor
```c
ip addr add 10.73.1.2/24 dev eth0
ip route add default via 10.73.1.1
echo nameserver 192.168.122.1 > /etc/resolv.conf
```

di ndoe manwe
```c
ip addr add 10.73.1.2/24 dev eth0
ip route add default via 10.73.1.1
echo nameserver 192.168.122.1 > /etc/resolv.conf
```

di ndoe varda
```c
ip addr add 10.73.2.2/24 dev eth0
ip route add default via 10.73.2.1
echo nameserver 192.168.122.1 > /etc/resolv.conf
```

di ndoe ulmo
```c
ip addr add 10.73.2.3/24 dev eth0
ip route add default via 10.73.2.1
echo nameserver 192.168.122.1 > /etc/resolv.conf
```

---
# SOAL 6
> Manwe
```c
apt-get update
apt-get install -y python3-pip unzip
pip3 install gdown --break-system-packages
gdown "https://drive.google.com/uc?id=1bE3kF1Nclw0VyKq4bL2VtOOt53IC7lG5" -O ./6.zip
unzip 6.zip -d ./6
```
![gambar6.png](gambar6.png)

harus pake --break-system-packages karna:
- Debian/Ubuntu modern (versi yang Anda pakai di GNS3) mengimplementasi PEP 668
- Policy ini memblokir pip install global untuk mencegah konflik dengan apt

![gambar61.png](gambar61.png)

---
#SOAL 7
> ERU
```c
apt update
apt install -y vsftpd
cp /etc/vsftpd.conf /etc/vsftpd.conf.original
```
### bikin user nya
- useradd buat bikin user
- m buat create / make home directory
- d /home/melkor ngasih path home dir 
- s bin/bash ngasih tau default shell nya
- melkor nama user nya
```c
useradd -m -d /home/ainur -s /bin/bash ainur
echo "ainur:ainur" | chpasswd
useradd -m -d /home/melkor -s /bin/bash melkor
echo "melkor:melkor" | chpasswd
```
- bikin direktori yang bisa diakses semua user dengan Owner bisa read, write, execute (7). Group & others bisa read, execute (5). dan diset yang punya ini si root, owner root group root
```c
mkdir -p /home/shared
chmod 755 /home/shared
chown root:root /home/shared
```
- membuat file teks sederhana kemudian akses file tersebut menggunakan kedua user. Owner bisa read + write (6). Group & others cuma read (4).
```c
echo "Ini adalah file test di direktori shared" > /home/shared/test.txt
chmod 644 /home/shared/test.txt
```
- bikin shared folder untuk ainur
```c
mkdir -p /home/ainur/shared
```
- masukin test.txt nya ke shared folder nya ainur
```c
cp /home/shared/test.txt /home/ainur/shared/
```
- bikin /home/ainur/shared ini dimiliki user ainur dengan 7 untuk owner (ainur) → bisa read, write, execute. 5 untuk group & others → cuma read + execute (nggak bisa write).
```c
chown -R ainur:ainur /home/ainur/shared
chmod 755 /home/ainur/shared
```
- ini bikin langsung di home nya ainur
```c
echo "File di home ainur" > /home/ainur/file_ainur.txt
chown ainur:ainur /home/ainur/file_ainur.txt
```

###edit ftp config nya
```c
================================isi vsftpd================================
# Basic settings
listen=YES
anonymous_enable=NO
local_enable=YES
write_enable=YES
local_umask=022
dirmessage_enable=YES
use_localtime=YES
xferlog_enable=YES
connect_from_port_20=YES

# Security settings
chroot_local_user=YES
allow_writeable_chroot=YES

# User access control
userlist_enable=YES
userlist_file=/etc/vsftpd.userlist
userlist_deny=NO

# Passive mode
pasv_enable=YES
pasv_min_port=40000
pasv_max_port=40100

# Performance
local_max_rate=0

# Secure chroot
secure_chroot_dir=/var/run/vsftpd/empty
pam_service_name=vsftpd

# SSL (disabled)
rsa_cert_file=/etc/ssl/certs/ssl-cert-snakeoil.pem
rsa_private_key_file=/etc/ssl/private/ssl-cert-snakeoil.key
ssl_enable=NO
=============================================================================
```

*penjelasan*
```c
listen=YES = vsftpd jalan dalam mode standalone, dengerin port sendiri (biasanya 21).
anonymous_enable=NO = disable login anonymous (aman, nggak ada guest login).
local_enable=YES = user lokal (yang ada di /etc/passwd) boleh login FTP.
write_enable=YES = izinkan operasi tulis (upload, delete, rename).
local_umask=022 = file/folder yang dibuat user akan dapet permission default 755 (untuk dir) atau 644 (untuk file).

dirmessage_enable=YES = kalau ada file .message di folder, isinya ditampilin pas user masuk folder itu.
use_localtime=YES = log pakai waktu lokal (bukan UTC).
xferlog_enable=YES = aktifkan transfer log (rekam aktivitas FTP, upload/download).
connect_from_port_20=YES = pakai port 20 untuk data transfer (mode aktif FTP).

chroot_local_user=YES = user FTP dikunci di home directory masing-masing, nggak bisa “keluyuran” ke sistem file lain.
allow_writeable_chroot=YES = biarin user bisa nulis di home walaupun home directory ditetapkan jadi chroot. Tanpa ini, kalau home bisa ditulis user, vsftpd bakal nolak login.

userlist_enable=YES = aktifkan userlist.
userlist_file=/etc/vsftpd.userlist = path file daftar user FTP.
userlist_deny=NO = artinya hanya user yang ada di userlist yang boleh login. Kalau YES, artinya semua user kecuali yang di userlist yang boleh login.

pasv_enable=YES = enable passive FTP (lebih aman lewat firewall/NAT).
pasv_min_port=40000 = port bawah passive mode.
pasv_max_port=40100 = port atas passive mode.

local_max_rate=0 = nggak ada limit speed (0 = unlimited).
secure_chroot_dir=/var/run/vsftpd/empty = direktori dummy, aman, dipakai kalau ada proses yang butuh chroot “kosong”.

pam_service_name=vsftpd = pakai PAM (Pluggable Authentication Module) untuk autentikasi.
rsa_cert_file dan rsa_private_key_file = lokasi sertifikat SSL default (snakeoil).
ssl_enable=NO = SSL/TLS dimatikan (jadi koneksi masih plain FTP, bukan FTPS).
```
- masukin ainur ke ftp
```c
echo "ainur" > /etc/vsftpd.userlist
service vsftpd start / restart / status / stop
ftp localhost
```

> ainur
```c
ls
cd shared
ls
get .....
bye
```
![gambar7.png](gambar7.png)

> ERU
```c
echo "File upload test oleh ainur" > /tmp/upload_test.txt
```
> ainur
- cd pindah folder di server ftp
- lcd /tmp buat pindah folder di local (ERU)
```c
cd shared
lcd /tmp
put ......
ls
bye
```
![gambar71.png](gambar71.png)

---
# SOAL 8
> ulno
```c
apt update
apt install -y ftp
apt install -y python3 python3-pip
pip3 install gdown --break-system-packages
mkdir -p /tmp/download
cd /tmp/download
gdown 11ra_yTV_adsPIXeIPMSt0vrxCBZu0r33
apt-get install -y unzip
unzip cuaca.zip -d ./cuaca

cd /tmp/download/cuaca/
cp /tmp/download/cuaca/cuaca.txt /tmp/download/
cp /tmp/download/cuaca/mendung.jpg /tmp/download/
```
- masuk ke eru lewat ainur dan set local ulno nya ke download
```c
ftp 10.73.2.1 ainur:ainur
cd shared
lcd /tmp/download
```

> ERU
```c
ls -la /home/ainur/shared/
chown -R ainur:ainur /home/ainur/shared/
chmod 755 /home/ainur/shared/
```

> ulno
```c
ftp 10.73.2.1
cd shared
lcd /tmp/download
binary
put ...
```
![gambar8.png](gambar8.png)

---
# SOAL 9
> ERU
```c
cd /tmp
wget "https://drive.google.com/uc?export=download&id=11ua2KgBu3MnHEIjhBnzqqv2RMEiJsILY" -O kitab_penciptaan.zip
apt install unzip 
unzip -d kitab_penciptaan.zip ./kitab_penciptaan
mv kitab_penciptaan/kitab_penciptaan.txt ./ 

cp /tmp/kitab_penciptaan.txt /home/ainur/shared/
chmod 444 /home/ainur/shared/kitab_penciptaan.txt

mkdir -p /etc/vsftpd/user_conf
```
- mengubah akses user ainur menjadi read-only
```c
cat > /etc/vsftpd/user_conf/ainur << 'EOF'
write_enable=NO
anon_upload_enable=NO
anon_mkdir_write_enable=NO
EOF
```

### nano /etc/vsftpd.conf
```c
+++++++++++++++++++++++++++isi nya+++++++++++++++++++++++++++++++
# Basic settings
listen=YES
anonymous_enable=NO
local_enable=YES
write_enable=NO
local_umask=022
dirmessage_enable=YES
use_localtime=YES
xferlog_enable=YES
connect_from_port_20=YES

# Security settings
chroot_local_user=YES
allow_writeable_chroot=YES

# User access control
userlist_enable=YES
userlist_file=/etc/vsftpd.userlist
userlist_deny=NO
listen_address=0.0.0.0

# Passive mode
pasv_enable=YES
pasv_min_port=40000
pasv_max_port=40100

# Performance
local_max_rate=0

# Secure chroot
secure_chroot_dir=/var/run/vsftpd/empty
pam_service_name=vsftpd

# SSL (disabled)
rsa_cert_file=/etc/ssl/certs/ssl-cert-snakeoil.pem
rsa_private_key_file=/etc/ssl/private/ssl-cert-snakeoil.key
ssl_enable=NO

user_config_dir=/etc/vsftpd/user_conf
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
```
- jalanin
```c
service vsftpd restart
service vsftpd status
```

> manwe
```c
apt update
apt install -y ftp

mkdir -p /root/downloads
cd /root/downloads

ftp
open 10.73.1.1 21
```
> ls -la /root/downloads/

> cat /root/downloads/kitab_penciptaan.txt

![gambar9.png](gambar9.png)

---
# SOAL 10
### sniff pake wireshark di melkor-eru

> melkor
ping -c 10 10.73.1.1
![gambar10.png](gambar10.png)

ping -c 100 10.73.1.1
![gambar101.png](gambar101.png)

ping -c 100 -i 0.001 10.73.1.1
![gambar102.png](gambar102.png)

ping -c 50 -s 1000 10.73.1.1
![gambar103.png](gambar103.png)

ping -c 500 -f -s 2000 10.73.1.1
![gambar104.png](gambar104.png)

---
# SOAL 11
> melkor
```c
apt update
apt install telnetd openbsd-inetd net-tools -y
nano /etc/inetd.conf
telnet stream tcp nowait root /usr/sbin/telnetd telnetd
```
```c
/etc/init.d/openbsd-inetd restart
adduser testingeru
ppp
```
> eru
```c
apt update
apt install telnet -y
```
- pasang wireshark
```c
telnet 10.73.1.2
```
![gambar11.png](gambar11.png)

---
#SOAL 12
> melkor
```c
apt update
apt install netcat-openbsd -y
sudo nc -l -p 21 &
sudo nc -l -p 80 &
```

> eru
```c
apt update
apt install netcat-openbsd -y
nc -zv 10.73.1.2 21
nc -zv 10.73.1.2 80
nc -zv 10.73.1.2 666
```

---
# SOAL 13
> eru
```c
apt update
apt install openssh-server -y

/etc/init.d/ssh start
ps aux | grep sshd

ip a | grep "inet " | grep -v 127.0.0.1
adduser varda
cat /etc/passwd | grep varda
```
> varda
```c
apt update
apt install openssh-client -y

ping -c 4 10.73.1.1

ssh varda@10.73.1.1
```
![gambar13.png](gambar13.png)
