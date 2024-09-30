---
date created: 2024-07-05 14:32
date updated: 2024-09-30 11:38
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
curl -sO https://raw.githubusercontent.com/coffee-90/Incident-Response-Tools/refs/heads/master/UbuntuIR.sh && sudo bash ./UbuntuIR.sh namasistemelektonik
```

Contoh :

```
curl -sO https://raw.githubusercontent.com/coffee-90/Incident-Response-Tools/refs/heads/master/UbuntuIR.sh && sudo bash ./UbuntuIR.sh lab22
```

Ouput-1 :

```
Collection-lab22.tar.gz
```

## Tahap 2 - Akuisisi Log

### 1. Pengumpulan File Log

**disesuaikan dengan jenis webserver yang digunakan**

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

### 1. Unduh & Menjalankan Thor-Lite

Thor-Lite berikut sudah ditambahkan rule hasil identifikasi Direktorat Operasi Siber BSSN

```
sudo su
cd /root
git clone https://github.com/adpermana/Thor-2.git
cd Thor2
chmod +x thor-lite-linux-64
```

Saatnya scanning

```
./thor-lite-linux-64 -a Filescan --intense -norescontrol -cross-platform -alldrives -p /home/ -p /var/www/
```

Output-4:  _(hasil bisa berbeda)_

- `ubuntu_thor_2024-07-05_2146.html`
- `ubuntu_thor_2024-07-05_2146.txt`

## 4 - Upload Bukti Digital

Sampai dengan saat ini kita memiliki 4 Ouput (6 files):

| Ouput | File                                                                    |
| ----- | ----------------------------------------------------------------------- |
| 1     | Collection-lab22.tar.gz                                                 |
| 2     | log.tar.gz                                                              |
| 3     | <li>log.md5<li>Collection-lab22.md5                                     |
| 4     | <li>ubuntu_thor_2024-07-05_2146.html<li>ubuntu_thor_2024-07-05_2146.txt |

Pindahkah 6 files tersebut ke dalam satu folder dan dikompresi.

```
cd
mkdir namasistemelektronik
mv log.tar.gz namasistemelektronik/
mv Collection-lab22.tar.gz namasistemelektronik/
mv log.md5 namasistemelektronik/
mv Collection-lab22.md5 namasistemelektronik/
mv thor/ubuntu_thor_2024-07-05_2146.* namasistemelektronik/
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

atau dapat menggunakan winscp atau filemanager lainnya.

**TAHAP PERSIAPAN SELESAI.**
