---
date created: 2024-07-05 14:32
date updated: 2024-07-07 19:12
---

# Tahapan Pengumpulan  Bukti Digital

Uji coba pada :

**Server**
- Domain : lab22.id
- Nama Sistem Elektronik : lab22
- OS : Ubuntu
- Username : adminsvr
- Web Service : Apache
- Compromised : Webshell, Cryptomining

**Host**
- OS : Windows 10
- VM : VMWare 17

## Tahap 1 - Eviden Collector

Menggunakan script eviden collector untuk otomatisasi pengumpulan bukti digital.

Command :

```
curl -sO https://raw.githubusercontent.com/adpermana/Incident-Response-Tools/analisa/UbuntuIR.sh && sudo bash ./UbuntuIR.sh nama_instansi
```

Contoh :

```
curl -sO https://raw.githubusercontent.com/adpermana/Incident-Response-Tools/analisa/UbuntuIR.sh && sudo bash ./UbuntuIR.sh lab22
```

Ouput-1 :

```
Collection-lab22.tar.gz
```

## Tahap 2 - Akuisisi Log

### 1. Pengumpulan File Log

```
mkdir log
cp /var/log/auth.* log/
cp /var/log/apache2/* log/
cp /var/log/nginx/* log/
```

resource : <https://medium.com/@cuncis/linux-archiving-commands-a-beginners-guide-to-efficient-file-management-8448f7403e95>

### 2. Compress Folder log

```
tar -czvf log.tar.gz log
```

Output-2:

```
log.tar.gz
```

### 3. Diggest Files

```
md5sum log.tar.gz > log.md5
md5sum Collection-lab22.tar.gz > Collection-lab22.md5
```

Output-3:

```
log.md5
Collection-lab22.md5
```

## Tahap 3 - Scanning Malicious Files

Menggunakan `Thor-Lite`, merupakan sebuah aplikasi pendeteksian portable untuk mendeteksi aktivitas mencurigakan pada sistem yang disusupi. Thor Scanner dapat mendeteki secara mendalam sampai local event log, registry, dan file system. Thor Scanner dapat menjadi system pendeteksi bagi aktivitas berbahaya yang terlewat oleh antivirus umum. Hasil dari pendeteksian menggunakan Thor Scanner dapat dieksport dalam bentuk HTML,TXT, JSON, CSV.

### 1. Registrasi

Kita perlu registrasi untuk dapat menggunakan `Thor-Lite`.

1. Buka website <https://www.nextron-systems.com/thor-lite/>
2. Download > Download Thor Lite
3. Masukkan `First Name` & `Last Name` (disarankan tidak memasukkan nama asli)
4. Masukkan `Email Address` (disarankan menggunakan email temporary, misalkan <https://temp-mail.org/en/>)
5. Checklist `Accept our Privacy Policy`
6. Checklist reCAPTCHA
7. Klick Submit

### 2. Download Thor-Lite

1. Buka inbox email dari `Nextron System` dengan subjek _Confirm your email address for THOR Lite - Nextron Systems_
2. Klik `Comfirm`
3. Buka inbox email dari `Nextron System` dengan subjek   _THOR Lite Download - Nextron Systems_
4. Klik `Download Your License` untuk mengunduh lisensi
5. Klik `Download Thor Lite` untuk mengunduh aplikasi, dalam hal ini kita mencoba _Download for Linux_
6. Checklist EULA > download.

Sampai disini kita memiliki 2 file yaitu : `thor-lite-nnnnnnnn.lic` & `thor10.7lite-linux-pack.zip` pada folder **Download**

### 3. Memindahkan Thor-Lite ke Linux

resource: <https://www.hostinger.co.id/tutorial/scp-linux>

Pada Windows,

1. Pada folder **Download**, klik tekan `SHIFT`+`klik kanan`
2. Pilih _Open PoweShell window here_
3. untuk memindahkan 2 file Thor-Lite & lisensinya dari windows ke server linux, ketikkan perintah berikut : `scp namafile user@ip:/home/user`, contoh:

```
scp .\thor10.7lite-linux-pack.zip adminsvr@lab22.id:/home/adminsvr
scp .\thor-lite-nnnnnnnn.lic adminsvr@lab22.id:/home/adminsvr
```

saat ini 2 file tersebut sudah berada di file linux server.

### 4. Menjalankan Thor-Lite

Extract `thor10.7lite-linux-pack.zip`

```
mkdir thor
unzip thor10.7lite-linux-pack.zip -d thor/
```

pindahkan `thor-lite-nnnnnnnn.lic` ke filder `thor`

```
mv thor-lite-nnnnnnnn.lic thor/
```

Saatnya scanning

```
cd thor
sudo ./thor-lite-util update
sudo ./thor-lite-util upgrade
sudo ./thor-lite-linux-64 -a Filescan --intense -norescontrol -cross-platform -alldrives -p /home/
```

Output-4: `ubuntu_thor_2024-07-05_2146.html`

## Upload Bukti Digital

Sampai dengan saat ini kita memiliki 4 Ouput (5 files):

| Ouput | File                                |
| ----- | ----------------------------------- |
| 1     | Collection-lab22.tar.gz             |
| 2     | log.tar.gz                          |
| 3     | <li>log.md5<li>Collection-lab22.md5 |
| 4     | ubuntu_thor_2024-07-05_2146.html    |

Pindahkah 5 files tersebut ke dalam satu folder dan dikompresi.

```
cd
mkdir namasistemelektronik
mv log.tar.gz namasistemelektronik/
mv Collection-lab22.tar.gz namasistemelektronik/
mv log.md5 namasistemelektronik/
mv Collection-lab22.md5 namasistemelektronik/
mv thor/ubuntu_thor_2024-07-05_2146.html namasistemelektronik/
tar -czvf namasistemelektronik.tar.gz namasistemelektronik/
md5sum namasistemelektronik.tar.gz > namasistemelektronik.md5
```

Salin `namasistemelektronik.tar.gz` dan `namasistemelektronik.md5`  ke Windows, buka _Command Prompt (CMD)_ di Windows, ketikkan:

```
scp username@ip:/home/usersaname/namasistemelektronik.tar.gz .
scp username@ip:/home/usersaname/namasistemelektronik.md5 .
```

Contoh

```
scp adminsvr@lab22.id:/home/adminsvr/lab22.tar.gz .
scp adminsvr@lab22.id:/home/adminsvr/lab22.md5 .
```

Upload `namasistemelektronik.tar.gz` ke cloud yang telah diinformasikan oleh PIC.

**TAHAP PERSIAPAN SELESAI.**
