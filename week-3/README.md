## **Konfigurasi Network Time Protocol (NTP)**

NTP Client adalah perangkat lunak yang berfungsi untuk menyinkronkan waktu sistem dengan server waktu menggunakan protokol NTP. Protokol ini memungkinkan komputer di jaringan untuk menyesuaikan jam dengan tingkat akurasi tinggi, biasanya hingga milidetik.

## NTPSec

NTP Secure (NTPsec) adalah versi yang telah diperkuat dari NTP, dirancang untuk menutup celah keamanan dan meningkatkan kestabilan. Berikut adalah langkah-langkah konfigurasinya.

### A. Instalasi NTP Client

1. Install dan konfigurasi NTP client agar host anda mempunyai Waktu yang sinkron dengan NTP server di Indonesia
    
    ![install-ntpsec.jpg](attachment:01c3691f-6c7d-4a89-85af-fb2eda90a9e5:install-ntpsec.jpg)
    
2. Cek waktu berdasarkan lokasi saya
    
    ![date.png](attachment:4813b8b8-94ef-4d5f-8757-0654ed7100f7:date.png)
    
3. nama NTP server yang harus dirujuk adalah ntp server Indonesia
    
    ![set-server-indo.jpg](attachment:d5af3e10-1af9-48e2-ae38-7875f157484c:set-server-indo.jpg)
    
    Selanjutnya restart seperti gambar dibawah ini:
    
    ![ntpq.jpg](attachment:1bbd435e-6cd6-478d-9b5e-2e43656f7fcb:ntpq.jpg)
    

### B. Instalasi dan konfigurasi Samba

1. Membuat public shared folder
    
    Install samba
    
    ![install-samba.jpg](attachment:299423f0-6317-44b3-8591-313f1ee0ebe5:install-samba.jpg)
    
    ```bash
    mkdir /home/share
    ```
    
    Membuat direktori `/home/share` sebagai folder berbagi.
    
    ```bash
    chmod 777 /home/share
    ```
    
    Memberikan izin penuh (read, write, execute) kepada semua pengguna.
    
    ```bash
    vi /etc/samba/smb.conf
    ```
    
    Membuka file konfigurasi Samba untuk diedit.
    
    Selanjutnya lakukan konfigurasi seperti gambar di bawah ini:
    
    ![smb1.jpg](attachment:cfb9ea78-3724-4a7d-8518-3d469a27a761:smb1.jpg)
    
    ```bash
    unix charset = UTF-8 
    ```
    
    untuk mengatur encoding karakter menjadi UTF-8 untuk mendukung karakter khusus.
    
    ```bash
    interfaces = 127.0.0.0/8 10.0.0.0/24
    ```
    
    Membatasi akses Samba hanya untuk localhost dan jaringan 10.0.0.0/24.
    
    ```bash
    map to guest = bad user
    ```
    
    Mengalihkan pengguna tidak dikenal ke akun guest secara otomatis.
    
    ![smb2.jpg](attachment:6766a9fb-af10-4424-8aee-6ea61f3d724e:smb2.jpg)
    
    ```bash
    [Share]
    ```
    
    Menentukan nama share yang akan digunakan.
    
    ```bash
    path = /home/share
    ```
    
    Menentukan direktori yang akan dibagikan melalui Samba.
    
    ```bash
    writable = yes
    ```
    
    Mengizinkan pengguna untuk menulis dan mengedit file dalam share ini.
    
    ```bash
    guest ok = yes
    ```
    
    Mengizinkan akses tamu tanpa autentikasi.
    
    ```bash
    guest only = yes
    ```
    
    Memaksa semua pengguna yang terhubung sebagai guest.
    
    ```bash
    force create mode = 777
    ```
    
    Mengatur izin semua file yang dibuat agar dapat diakses oleh semua pengguna.
    
    ```bash
    force directory mode = 777
    ```
    
    Mengatur izin semua folder yang dibuat agar dapat diakses oleh semua pengguna.
    
    ```bash
    systemctl restart smbd
    ```
    
    Me-restart layanan Samba agar perubahan konfigurasi diterapkan.
    
    ![cek-status-smbd.jpg](attachment:3b0723e4-9b5a-4779-a6b9-35362039c57c:cek-status-smbd.jpg)
    
    Setelah direstart, ketik `systemctl status smbd` untuk cek status dari samba aktif apa belum.
    
    Akses menggunakan Window Client:
    
    ![cek-di-laptop-with-lan.png](attachment:811f309e-ea6b-4013-8487-30b85f1ed6b7:cek-di-laptop-with-lan.png)
    
    Akses menggunakan Debian Client:
    
    ![cek-di-host.jpg](attachment:ceb293b0-fcfd-4d84-ac2c-122b4de9cf10:cek-di-host.jpg)
    
2. Membuat limited shared Folder
    
    ```bash
    groupadd wangsagroup01
    ```
    
    Membuat grup baru bernama wangsa**group01** untuk mengelola akses Samba.
    
    ```bash
    mkdir /home/share01
    ```
    
    Membuat direktori **/home/share01** sebagai folder berbagi.
    
    ```bash
    chgrp wangsagroup01 /home/share01
    ```
    
    Mengubah grup kepemilikan direktori menjadi wangsa**group01**.
    
    ```bash
    chmod 770 /home/share01
    ```
    
    Mengatur izin agar hanya pemilik dan anggota grup wangsa**group01** yang bisa mengaksesnya.
    
    ```bash
    vi /etc/samba/smb.conf
    ```
    
    Membuka file konfigurasi Samba untuk diedit.
    
    ![smb1.jpg](attachment:cfb9ea78-3724-4a7d-8518-3d469a27a761:smb1.jpg)
    
    ```bash
    unix charset = UTF-8 
    ```
    
    untuk mengatur encoding karakter menjadi UTF-8 untuk mendukung karakter khusus.
    
    ```bash
    interfaces = 127.0.0.0/8 10.0.0.0/24
    ```
    
    Membatasi akses Samba hanya untuk localhost dan jaringan 10.0.0.0/24.
    
    ![limited4.png](attachment:fa064d2e-0cee-4770-b94f-216070f064c4:limited4.png)
    
    ```bash
    [Share01]
    ```
    
    Menentukan nama share baru sebagai **Share01**.
    
    ```bash
    security = user
    ```
    
    Mengaktifkan autentikasi berbasis pengguna.
    
    ```bash
    path = /home/share01
    ```
    
    Menentukan direktori **/home/share01** sebagai folder berbagi
    
    ```bash
    writable = yes
    ```
    
    Mengizinkan pengguna untuk menulis dan mengedit file dalam share ini
    
    ```bash
    guest ok = no
    ```
    
    Menolak akses tamu, hanya pengguna terdaftar yang bisa masuk.
    
    ```bash
    valid users = @wangsagroup01
    ```
    
    Hanya anggota grup wangsa**group01** yang dapat mengakses share ini.
    
    ```bash
    force group = wangsagroup01
    ```
    
    Secara otomatis menetapkan grup file yang dibuat sebagai wangsa**group01**.
    
    ```bash
    force create mode = 770
    ```
    
    Mengatur izin file yang dibuat agar bisa diakses hanya oleh pemilik dan grup.
    
    ```bash
    force directory mode = 770
    ```
    
    Mengatur izin folder yang dibuat agar bisa diakses hanya oleh pemilik dan grup.
    
    ```bash
    inherit permissions = yes
    ```
    
    Membuat file dan folder baru mewarisi izin dari direktori induk.
    
    ```bash
    systemctl restart smbd
    ```
    
    Me-restart layanan Samba agar perubahan konfigurasi diterapkan.
    
    ```bash
    adduser dewangga
    ```
    
    Membuat akun pengguna **dewangga** di sistem.
    
    ![limited3.png](attachment:018f1d14-5fbd-4638-8c5b-234967cc7ba9:limited3.png)
    
    smbpasswd -a debian
    
    Menambahkan pengguna **dewangga** ke Samba dan mengatur kata sandinya.
    
    ![Screenshot 2025-03-09 123514.png](attachment:b884cf8f-fd08-4abf-9dca-73af76c37f44:Screenshot_2025-03-09_123514.png)
    
    ```bash
    usermod -aG wangsagroup01 debian
    ```
    
    Menambahkan pengguna **dewangga** ke grup **wangsagroup01** agar bisa mengakses **Share01**.
    
    Akses menggunakan Window Client dan login dengan user yang telah ditentukan:
    
    ![Screenshot 2025-03-09 123642.png](attachment:fb9b8e00-56bc-483e-a926-bf290751a02a:Screenshot_2025-03-09_123642.png)
    
    ![Screenshot 2025-03-10 195615.png](attachment:e3675f8a-cea7-4e6e-b90f-3e505878564e:Screenshot_2025-03-10_195615.png)
    
    Akses menggunakan Debian Client dan login dengan user yang telah ditentukan:
    
    ![Screenshot 2025-03-10 200857.png](attachment:8c74c7ce-fe8f-4b2b-838c-4db4e1ba8670:Screenshot_2025-03-10_200857.png)
    
    ![Screenshot 2025-03-10 200930.png](attachment:c8fd3cea-0af8-4dc9-9aa5-6ea6bb83877c:Screenshot_2025-03-10_200930.png)
    
3. akses ke folder Share dari CLI client

![image.png](attachment:6aa75d7b-e03b-4f0d-ab56-51a84a72367e:image.png)

### C. Buat rangkuman tentanag package management.

**Advanced Package Tool (APT)**

**Manajemen paket di Linux** adalah proses menginstal, memperbarui, menghapus, dan mengelola perangkat lunak menggunakan sistem manajemen paket (Package Management System). Sistem ini memungkinkan pengguna untuk mengelola perangkat lunak dengan lebih efisien melalui repositori resmi yang berisi berbagai paket aplikasi. Dengan manajemen paket, pengguna dapat dengan mudah menjaga sistem tetap terupdate, mengelola dependensi, serta memastikan keamanan perangkat lunak yang digunakan.

- **Perintah 'User' untuk Mencari dan Menampilkan Informasi**
    
    Perintah-perintah ini dapat dijalankan oleh pengguna biasa, karena tidak akan memengaruhi sistem Anda.
    
    | **Perintah** | **Deskripsi** |
    | --- | --- |
    | `apt show foo` | Menampilkan informasi tentang paket **foo** |
    | `apt search foo` | Mencari paket yang sesuai dengan **foo** |
    | `apt-cache policy foo` | Menampilkan versi yang tersedia dari **foo** |
- **Perintah Mode ‘Administrator’ untuk Pemeliharaan Sistem**
    
    Perintah-perintah ini harus dijalankan dengan hak administrator "root" karena dapat memengaruhi sistem.
    
    Untuk masuk ke mode administrator dari terminal, ketik **`su -`** lalu masukkan kata sandi administrator.
    
    ### **Perintah Dasar**
    
    | **Perintah** | **Deskripsi** |
    | --- | --- |
    | `apt update` | Memperbarui metadata repositori |
    | `apt install foo` | Menginstal paket **foo** beserta dependensinya |
    | `apt upgrade` | Memperbarui paket yang terinstal dengan aman |
    
    ---
    
    ### **Perintah Lanjutan**
    
    | **Perintah** | **Deskripsi** |
    | --- | --- |
    | `apt full-upgrade` | Memperbarui paket yang terinstal dengan menambah/menghapus paket lain jika diperlukan |
    | `apt remove foo` | Menghapus paket **foo**, tetapi tidak menghapus file konfigurasi |
    | `apt autoremove` | Menghapus paket yang tidak diperlukan secara otomatis |
    | `apt purge foo` | Menghapus paket **foo** beserta file konfigurasinya |
    | `apt clean` | Membersihkan cache lokal dari paket yang diinstal |
    | `apt autoclean` | Membersihkan cache lokal dari paket yang sudah usang |
    | `apt-mark showmanual` | Menandai paket sebagai "diinstal secara manual" |

**Software (Simplified Package Manager)**

Software (Simplified Package Manager) adalah alat manajemen paket yang dirancang untuk memudahkan pengguna dalam menginstal, memperbarui, dan menghapus aplikasi di sistem operasi berbasis Linux, seperti Debian dan Ubuntu. Berbeda dengan manajer paket berbasis terminal seperti APT, Software menyediakan antarmuka grafis yang lebih intuitif, memungkinkan pengguna untuk mencari dan mengelola aplikasi tanpa perlu mengetik perintah di terminal. Manajer paket ini sering digunakan oleh pengguna yang menginginkan kemudahan dalam mengelola perangkat lunak tanpa harus memahami detail teknis dari sistem manajemen paket yang lebih kompleks.

![image.png](attachment:e6327b60-3396-4a29-bd9c-119a66e623fc:image.png)

**Discover: KDE Package Manager**

Discover: KDE Package Manager adalah alat manajemen paket berbasis GUI yang dikembangkan untuk lingkungan desktop KDE Plasma. Discover memungkinkan pengguna untuk mencari, menginstal, memperbarui, dan menghapus aplikasi serta ekstensi dengan antarmuka yang ramah pengguna. Selain mendukung berbagai format paket seperti DEB, RPM, Flatpak, dan Snap, Discover juga dapat mengelola pembaruan sistem secara langsung. Dengan desain yang sederhana dan integrasi yang baik dengan ekosistem KDE, Discover menjadi pilihan ideal bagi pengguna yang menginginkan kemudahan dalam mengelola perangkat lunak tanpa harus menggunakan perintah terminal.

![image.png](attachment:4de523bd-96c3-4d28-ab1b-4e83c7f9e1e4:image.png)

**Synaptic: Comprehensive Package Manager** 

Synaptic: Comprehensive Package Manager ****adalah alat manajemen paket berbasis GUI yang dirancang untuk sistem berbasis Debian, seperti Ubuntu. Synaptic memberikan kontrol penuh atas instalasi, pembaruan, dan penghapusan paket dengan antarmuka yang lebih mendalam dibandingkan dengan manajer paket sederhana. Pengguna dapat melihat daftar lengkap paket yang tersedia, menyaring berdasarkan kategori, serta melihat dependensi dan file terkait sebelum melakukan perubahan. Dengan fitur pencarian yang kuat dan dukungan untuk berbagai konfigurasi sistem, Synaptic menjadi pilihan ideal bagi pengguna yang menginginkan fleksibilitas dan kendali penuh atas perangkat lunak mereka tanpa harus menggunakan terminal.

![image.png](attachment:71b8a301-6f6c-4f7e-b82f-162eeb88472c:image.png)

**GDebi** 

![image.png](attachment:5a7ec694-5c4c-4162-8cb1-56d2e80fb1f2:image.png)

GDebi adalah alat manajemen paket yang digunakan untuk menginstal paket **.deb** di sistem berbasis Debian dan Ubuntu. Dibandingkan dengan **dpkg**, GDebi memiliki keunggulan dalam menangani dependensi secara otomatis, sehingga memastikan semua pustaka dan paket tambahan yang dibutuhkan juga terinstal. GDebi tersedia dalam versi antarmuka grafis (GUI) maupun mode terminal (CLI), memungkinkan pengguna untuk menginstal paket dengan mudah dan efisien tanpa harus mencari dan menginstal dependensi secara manual.

**DPKG**

Dpkg adalah manajer paket dasar untuk sistem berbasis Debian yang digunakan untuk menginstal, menghapus, dan mengelola paket **.deb** secara langsung. Berbeda dengan **APT**, **dpkg** tidak menangani dependensi secara otomatis, sehingga pengguna harus menginstal paket tambahan yang diperlukan secara manual. **Dpkg** biasanya digunakan dalam mode terminal dan menjadi komponen inti dalam sistem manajemen paket Debian, memungkinkan pengguna untuk mengelola perangkat lunak tanpa perlu koneksi ke repositori daring.
