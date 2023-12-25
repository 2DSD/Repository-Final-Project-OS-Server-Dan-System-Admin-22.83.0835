# Repository-Final-Project-OS-Server-Dan-System-Admin-22.83.0835
Final Projek ini adalah panduan atau langkah-langkah dalam instalasi dan konfigurasi layanan server menggunakan OS Centos 7 . Saya memberikan langkah-langkah untuk mengatur berbagai layanan server. Layanan server yang saya instal yaitu SSH-Server, FTP-Server, DNS-Server, Mail-Server, Web-Server

## Daftar Isi
1. [Instalasi dan Konfigurasi SSH] 
2. [Instalasi dan Konfigurasi DNS Server]
3. [Instalasi dan Konfigurasi FTP dan Samba Server]
4. [Instalasi dan Konfigurasi Web Server]
5. [Instalasi dan Konfigurasi Mail Server]


## 1. Instalasi dan Konfigurasi SSH Server
**Langkah 1: Buka Direktori konfigurasi ssh dengan nano**
```
nano /etc/ssh/sshd_config
```
**Langkah 2: Edit Konfigurasi seperti dibawah ini**
```
Include /etc/ssh/sshd_config.d/*.conf

Port 22
#AddressFamily any
#ListenAddress 0.0.0.0
#ListenAddress ::

#HostKey /etc/ssh/ssh_host_rsa_key
#HostKey /etc/ssh/ssh_host_ecdsa_key
#HostKey /etc/ssh/ssh_host_ed25519_key

# Ciphers and keying
#RekeyLimit default none


#### 2 Konfigurasi DNS Server

**Langkah 1: Konfigurasi file named.conf seperti dibawah ini**

![3](https://github.com/2DSD/Repository-Final-Project-OS-Server-Dan-System-Admin-22.83.0835/assets/114807215/3583071a-253b-4660-ad4d-b2cc74b23667)

**Disini saya menambahkan IP forwarder dengan menggunakan IP Gateway dan DNS Public 8.8.8.8 fungsinya ketika Client menggunakan IP DNS Lokal, maka Client masih dapat mengakses ke Internet. Silahkan sesuaikan dengan IP DNS masing-masing**

![4](https://github.com/2DSD/Repository-Final-Project-OS-Server-Dan-System-Admin-22.83.0835/assets/114807215/80e5fa9c-25ab-4df2-a9b2-4d3af767ee51)

**Selanjutnya, saya akan menambahkan dua file Zone yaitu Forward Zone File dan Reverse Zone. Untuk nama domain disini saya menggunakan nama saya "georelbonai.com". Silahkan sesuaikan dengan nama domain masing-masing.**

![5](https://github.com/2DSD/Repository-Final-Project-OS-Server-Dan-System-Admin-22.83.0835/assets/114807215/3d4c0dc9-f783-4787-baf0-6976221bb30e)

**Langkah 2: Buat file baru untuk forward zone**
```
nano /var/named/db.Arsya.com
```
**Bebas untuk menamainya, disana saya menggunakan nama**

**Lalu tambahkan script seperti dibawah ini**

![7](https://github.com/2DSD/Repository-Final-Project-OS-Server-Dan-System-Admin-22.83.0835/assets/114807215/2d8dc91e-5799-428b-baa9-0f8cb853263b)

**Langkah 3: Buat file reverse zone**

**Selanjutnya, buat file reverse zone dengan nama db.ip_reverse pada direktori /var/named**
```
nano /var/named/db.173.168.192
```

**Tambahkan script seperti dibawah ini:**

![9](https://github.com/2DSD/Repository-Final-Project-OS-Server-Dan-System-Admin-22.83.0835/assets/114807215/fc6eb6af-7872-44d9-8325-2e92977bce82)

**Langkah 4: Simpan Konfigurasi dan Jalankan Service DNS**

**Masukkan Perintah ini:**
```
systemctl start named
```
```
systemctl enable named
```
**Cek juga status Service DNS Server, pastikan service berjalan dengan baik**

![11](https://github.com/2DSD/Repository-Final-Project-OS-Server-Dan-System-Admin-22.83.0835/assets/114807215/6196e5e1-1950-41ef-8939-a584c79a588f)

#### 2.3 Tambahkan Firewall
**Langkah 1: Tambahkan Firewall pada Service DNS**
```
firewall-cmd --add-service=dns --permanent
```
```
firewall-cmd --reload
```
#### 2.4 Cek Settingan resolv.conf
**Langkah 1: Cek juga settingan resolv.conf, pastikan nameserver sudah diisi dengan IP Address Server**
```
nano /etc/resolv.conf
```
![14](https://github.com/2DSD/Repository-Final-Project-OS-Server-Dan-System-Admin-22.83.0835/assets/114807215/4d845646-08c3-4eab-82f9-06f20fe8d42b)

#### 2.5 Pengetesan
**Langkah 1: Lakukan pengetesan**

**Untuk pengetesan silahkan jalankan perintah nslookup nama domain, jika berhasil maka nama domain akan di translasikan ke alamat IP Address Server**

![15](https://github.com/2DSD/Repository-Final-Project-OS-Server-Dan-System-Admin-22.83.0835/assets/114807215/c24dcf6f-b50d-4690-bde8-fc4e7f32f3ac)

**Lalu test ping ke nama Domain dari Laptop Host**

![16](https://github.com/2DSD/Repository-Final-Project-OS-Server-Dan-System-Admin-22.83.0835/assets/114807215/c028f3d0-d871-436e-be1d-4be0fc94e0b1)

## 3. Instalasi dan Konfigurasi FTP dan Samba Server

### 3.1 Instalasi FTP dan Samba
**Langkah 1: Instalasi Paket samba**
```
apt update
apt-get install samba
```
**Langkah 2: Instalasi Paket VSFTPD(FTP)y**
Sepertinya ada banyak pilihan seperti "PROFTPD" tapi disini saya menggunakan VSFTPD
```
apt update
apt-get install vsftpd
```

### 3.2 Konfigurasi Samba

**Langkah 1: Lakukan Backup Konfigurasi Default**
```
cp /etc/samba/smb.conf /etc/samba/smb.conf.backup
```
**Langkah 2: Hapus isi Konfigurasi didalam file samba.conf**
```
echo "" | tee /etc/samba/smb.conf
```
**Langkah 3: Buka file konfigurasi Utama Samba**
```
nano /etc/samba/smb.conf
```
**Langkah 4: isi file Konfigurasi**
```
[anonymous]
path = /home/rinto
public = yes
writable = yes
guest ok = yes
guest only = yes

**Langkah 5: Membuat Direktori dan Konfigurasi User**
```
mkdir /home/public
mkdir /home/private
groupadd rinto
useradd -G rinto fenrir
smbpasswd -a rinto
New SMB password:
Retype new SMB password:
Added user rinto.
```
**Langkah 6: Set permission untuk kedua Direktori**
```
chmod 777 /home/public
chmod 770 /home/private
chown :rinto /home/private
```
**Langkah 7: Restart Service SMB**
```
systemctl restart smbd
```
### 3.3 Konfigurasi VSFTPD(FTP)
**Langkah 1: Buka File Konfigurasi Utama Vsftpd**
```
nano /etc/vsftpd.conf
```
**Langkah 2 : Lakukan Perubahan Konfigurasi berikut :**
```
listen=YES
listen_ipv6=NO
anonymous_enable=NO
#HILANGKAN TANDA PAGAR PADA LINE KONFIGURASI DIBAWAH INI#
write_enable=YES
local_umask=022
ascii_upload_enable=YES
ascii_download_enable=YES
chroot_local_user=YES
chroot_list_enable=YES
chroot_list_file=/etc/vsftpd.chroot_list
ls_recurse_enable=YES
```
**Langkah 3: Menambahkan User baru dan Memasukanya ke list chroot**
Menambahkan user baru
```
useradd rinto
passwd rinto
New password:
Retype new password:
passwd:
password updated successfully
```
menambahkan user baru ke list 
```
nano /etc/vsftpd.chroot_list
#Tambahkan user baru di direktori ini
rinto
```
**Langkah 4: Restart Layanan Vsftpd**
```
systemctl restart vsftpd
```
### 3.4 Pengujian Konfigurasi

. FTP Server
   ini adalah Tampilan dari FTP client(Menggunakan Filezila)

![Filezila Client FTP](![25](https://github.com/2DSD/Repository-Final-Project-OS-Server-Dan-System-Admin-22.83.0835/assets/114807215/f4052a8f-7517-44fa-b93e-b5c633ddac51)
)

## 4. Instalasi dan Konfigurasi WEB Server
#### 4.1 Instalasi dan Konfigurasi HTTPd
**Langkah 1: Install HTTPd**
```
yum -y install httpd
```
**Langkah 2: Konfigurasi HTTPD Web Server**

**Untuk file konfigurasi utama httpd dengan nama httpd.conf yang berada pada direktori /etc/httpd/conf/ Silahkan buka file tersebut dengan menggunakan text editor kesukaan anda.**
```
nano /etc/httpd/conf/httpd.conf
```
**Lalu edit file tersebut seperti dibawah ini:**

![3](https://github.com/2DSD/Repository-Final-Project-OS-Server-Dan-System-Admin-22.83.0835/assets/114807215/4991f713-8099-4245-bd37-c7eb918f0036)

![4](https://github.com/2DSD/Repository-Final-Project-OS-Server-Dan-System-Admin-22.83.0835/assets/114807215/6d5c1f6a-6786-408c-9acd-c686a2ff72e5)


**Langkah 3: Simpan Konfigurasi dan aktifkan service HTTPd**
```
systemctl start httpd
```
```
systemctl enable httpd
```

**Langkah 4: Tambahkan Firewall**
```
firewall-cmd --add-service=http --permanent
```
```
firewall-cmd --reload
```

#### 4.2 Pengetesan
**Langkah 1: Test pada PC Client**

![7](https://github.com/2DSD/Repository-Final-Project-OS-Server-Dan-System-Admin-22.83.0835/assets/114807215/c6acc2e2-63ef-4cfc-81de-bc7497796fd0)


**Dikarenakan saya telah membuat DNS maka, saya dapat memanggil WEB Server dengan Domain saya**

#### 4.3 Membuat Website Sederhana
**Selanjutnya kita akan coba membuat sebuah website sederahan, untuk penempatan file website yang kita buat ada pada direktori /var/www/html/ disinilah tempat untuk meletakan file website yang kita buat. Sebagai contoh, kita akan membuat satu folder dengan nama website1 lalu di dalam folder tersebut kita buat satu buah file dengan nama index.html lalu kita akan masukkan script HTML sederhana. Silahkan ikuti langkah-langkahnya seperti dibawah ini:**

**Langkah 1: Menghapus file Welcome Page**
```
rm -f /etc/httpd/conf.d/welcome.conf
```
**Langkah 2: Membuat file Welcome page baru**
```
cd /var/www/html/
```
```
mkdir website1
```
```
cd website1/
```
```
nano index.html
```
**Masukkan Script HTML sederhana dibawah ini:**
```
<!doctype html>
<html>
<head>
    <title>Muhamad Arsya Pamungkas</title>

    <meta charset="utf-8" />
    <meta http-equiv="Content-type" content="text/html; charset=utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <style type="text/css">
    body {
        background-color: #00FF00;
        margin: 0;
        padding: 0;
        font-family: -apple-system, system-ui, BlinkMacSystemFont, "Segoe UI", "Open Sans", "Helvetica Neue", Helvetica, Arial, sans-serif;
        
    }
    div {
        width: 600px;
        margin: 5em auto;
        padding: 2em;
        background-color: #fdfdff;
        border-radius: 0.5em;
        box-shadow: 2px 3px 7px 2px rgba(0,0,0,0.02);
    }
    a:link, a:visited {
        color: #38488f;
        text-decoration: none;
    }
    @media (max-width: 700px) {
        div {
            margin: 0 auto;
            width: auto;
        }
    }
    </style>    
</head>

<body>
<div>
    <h1 style="text-align: center;">www.georelbonai.com</h1>
    <p>Hallo, jika anda berhasil melihat halaman page ini berarti konfigurasi WEB Server telah berhasil. Terimakasih.</p>

</div>
</body>
```

**Langkah 3: Simpan Konfigurasi dan Jalankan Service HTTPd**
```
systemctl restart httpd
```

**Langkah 4: Pengetesan**
**Untuk pengetesan silahkan akses menggunakan browser dari PC Client lalu masukan pada url http://ip_address_server/nama_folder_web jika berhasil maka akan mengarah ke website yang kita buat.**

![ip dns](https://github.com/2DSD/Repository-Final-Project-OS-Server-Dan-System-Admin-22.83.0835/assets/114807215/4b2a4fe4-1e00-423c-835a-9cd9109f281e)

#### 4.4 Membuat VirtualHost
**Langkah 1: Membuat VirtualHost**

**Selanjutnya kita akan membuat Virtualhost, Jika sebelumnya kita mengakses website kita dengan menggunakan IP Address, maka dengan menggunakan Virtualhost kita dapat mengakses website kita dengan menggunakan nama domain. Ok silahkan masuk pada direktori /etc/httpd/conf.d/ lalu buat satu buah file dengan extention .conf**

```
cd /etc/httpd/conf.d/
```
```
nano contoh.conf
```
**Masukkan Script seperti dibawah ini:**

![14](https://github.com/2DSD/Repository-Final-Project-OS-Server-Dan-System-Admin-22.83.0835/assets/114807215/7d826e12-289b-4f7e-8e54-9475430fbcdc)


**Langkah 2: Restart Service HTTPd**
```
systemctl restart httpd
```
**Untuk pengetesan silahkan akses menggunakan browser dari PC Client lalu masukan pada url http://namadomain.com jika berhasil maka nama domain akan mengarah ke website yang kita buat.**

![bukti punya dns](https://github.com/2DSD/Repository-Final-Project-OS-Server-Dan-System-Admin-22.83.0835/assets/114807215/a9df042a-9aa0-4405-8054-fac74cadb5c0)

## 5. Instalasi dan Konfigurasi Mail Server
#### 5.1 Instalasi dan Konfigurasi Postfix
**Langkah 1: Install Postfix**
```
yum -y install postfix
```

**Langkah 2: Konfigurasi Main.cf**
```
nano /etc/postfix/main.cf
```
**konfigurasi file main.cf seperti dibawah ini:**
![3](https://github.com/2DSD/Repository-Final-Project-OS-Server-Dan-System-Admin-22.83.0835/assets/114807215/6174b049-a048-428c-8aa3-979d730eb33d)
![4](https://github.com/2DSD/Repository-Final-Project-OS-Server-Dan-System-Admin-22.83.0835/assets/114807215/5a87c252-6a36-4691-b67b-54e8426f3dd4)
![5](https://github.com/2DSD/Repository-Final-Project-OS-Server-Dan-System-Admin-22.83.0835/assets/114807215/7f416b0f-f06d-4a33-a87b-b70d484f0ecd)
![6](https://github.com/2DSD/Repository-Final-Project-OS-Server-Dan-System-Admin-22.83.0835/assets/114807215/0bfd585e-339d-4b66-81e9-d7fc14df442a)
![7](https://github.com/2DSD/Repository-Final-Project-OS-Server-Dan-System-Admin-22.83.0835/assets/114807215/1e85542a-41db-49f5-85ed-537d66e77bbd)

**Langkah 3: Restart dan Jalankan Service Postfix**
```
systemctl restart postfix
```
```
systemctl enable postfix
```
#### 5.2 Tambahkan Firewall pada Postfix
**Langkah 1: Tambahkan Firewall pada Postfix**
```
firewall-cmd --add-service=smtp --permanent
```
```
firewall-cmd --reload
```

#### 5.3 Instalasi dan Konfigurasi Dovecot
**Langkah 1: Install Dovecot**
```
yum -y install dovecot
```
**Langkah 2: Konfigurasi file dovecot.conf**
```
nano /etc/dovecot/dovecot.conf
```
**Ubahlah .conf seperti dibawah ini:**

![13](https://github.com/2DSD/Repository-Final-Project-OS-Server-Dan-System-Admin-22.83.0835/assets/114807215/c8624e69-69a8-4391-a50d-4815e9a96ee8)


**Langkah 3: Konfigurasi file 10-auth.conf**
```
nano /etc/dovecot/conf.d/10-auth.conf
```
**Ubahlah .conf seperti dibawha ini:**

![15](https://github.com/2DSD/Repository-Final-Project-OS-Server-Dan-System-Admin-22.83.0835/assets/114807215/13ee2b90-bd76-4401-9cd9-6a8c2b33a9d0)
![16](https://github.com/2DSD/Repository-Final-Project-OS-Server-Dan-System-Admin-22.83.0835/assets/114807215/1f777548-7ff6-488d-842e-792bf45aaee4)


**Langkah 4: Konfigurasi file 10-mail.conf**
```
nano /etc/dovecot/conf.d/10-mail.conf
```
**Ubahlah .conf seperti dibawah ini:**

![18](https://github.com/2DSD/Repository-Final-Project-OS-Server-Dan-System-Admin-22.83.0835/assets/114807215/bbb9ec2f-b22b-4bc3-892c-70387ce43a8f)

**Langkah 5: Konfigurasi file 10-master.conf**
```
nano /etc/dovecot/conf.d/10-master.conf
```
**Ubahlah .conf seperti dibawah in:**

![20](https://github.com/2DSD/Repository-Final-Project-OS-Server-Dan-System-Admin-22.83.0835/assets/114807215/260e8626-9f73-49bf-8800-fd73cc25484f)

**Langkah 6: Konfigurasi file 10-ssl.conf**
```
nano /etc/dovecot/conf.d/10-ssl.conf
```
**Ubahlah .conf seperti dibawah ini**

![22](https://github.com/2DSD/Repository-Final-Project-OS-Server-Dan-System-Admin-22.83.0835/assets/114807215/7f1a34ce-c437-4137-ae67-25c3625da529)

**Langkah 7: Aktifkan dan Jalankan service Dovecot**
```
systemctl start dovecot
```
```
systemctl enable dovecot
```
#### 5.4 Tambahkan Firewall pada Dovecot
**Langkah 1: Tambahkan Firewall pada Dovecot**
```
firewall-cmd --add-port={110/tcp,143/tcp} --permanent
```
```
firewall-cmd --reload
```
#### 5.5 Pengetesan
**Langkah 1: Buat user baru**
```
useradd nama_user_baru
```
```
passwd password_baru
```
**Langkah 2: Install Mailx**
```
yum -y install mailx
```
**Langkah 3: Setting Environment Variabel untuk menggunakan Maildirectory**
```
echo 'export MAIL=$HOME/Maildir' >> /etc/profile
```
**Langkah 4: Masukkan Perintah disini untuk mengirim Pesan**
```
telnet localhost smtp #Enter
mail from: <test> #Enter
rcpt to: <falan> #Enter
data #Enter
Hello World #Enter
. #Enter
quit #Enter
```
**Contoh**

![mail 1](https://github.com/2DSD/Repository-Final-Project-OS-Server-Dan-System-Admin-22.83.0835/assets/114807215/60c9878b-d23f-447e-87e3-a0019a5b901a)
![mail 2](https://github.com/2DSD/Repository-Final-Project-OS-Server-Dan-System-Admin-22.83.0835/assets/114807215/3f48dc90-cfa7-4b51-8cf3-895bf2c914ea)
![mail 3](https://github.com/2DSD/Repository-Final-Project-OS-Server-Dan-System-Admin-22.83.0835/assets/114807215/2ff5997c-0d81-45e1-b8d8-6f91ac8a8d60)






