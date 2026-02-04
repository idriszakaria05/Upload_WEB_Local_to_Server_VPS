==========================================
AWLR SYSTEM - DEPLOYMENT GUIDE
==========================================

TUJUAN: Deploy AWLR System ke VPS YOUR_IP_SERVER

LANGKAH 1: PREPARASI DI WINDOWS
================================
1. Pastikan file key SSH ada di:
   C:\Users\[Username]\.ssh\awlr_vps.pem

2. Buka Git Bash di folder web:
   - Buka File Explorer ke C:\awlr-system
   - Klik kanan → "Git Bash Here"

3. Cek koneksi SSH:
   ssh -i ~/.ssh/awlr_vps.pem username_server@YOUR_IP_SERVER

LANGKAH 2: INSTALL SOFTWARE DI VPS
===================================
Setelah login ke VPS, jalankan perintah berikut:

1. Update sistem:
   sudo apt update && sudo apt upgrade -y

2. Install Apache:
   sudo apt install apache2 -y

3. Install PHP 8.1:
   sudo apt install software-properties-common -y
   sudo add-apt-repository ppa:ondrej/php -y
   sudo apt update
   sudo apt install php8.1 php8.1-fpm php8.1-cli php8.1-sqlite3 \
   php8.1-mysql php8.1-curl php8.1-gd php8.1-mbstring \
   php8.1-xml php8.1-zip libapache2-mod-php8.1 -y

LANGKAH 3: UPLOAD FILE DARI WINDOWS
====================================
Kembali ke Windows Git Bash, jalankan:

1. Upload semua file:
   scp -i ~/.ssh/awlr_vps.pem -r * username_server@YOUR_IP_SERVER:/home/nazaprima/awlr-system/

LANGKAH 4: SETUP DI VPS
========================
Kembali ke terminal VPS, jalankan:

1. Buat folder web:
   sudo mkdir -p /var/www/html/awlr-system

2. Copy file:
   sudo cp -r /home/nazaprima/awlr-system/* /var/www/html/awlr-system/

3. Atur permissions:
   sudo chown -R www-data:www-data /var/www/html/awlr-system
   sudo chmod -R 755 /var/www/html/awlr-system

4. Buat folder database:
   sudo mkdir -p /var/www/html/awlr-system/db
   sudo chmod 777 /var/www/html/awlr-system/db

LANGKAH 5: KONFIGURASI APACHE
==============================
1. Buat virtual host:
   sudo nano /etc/apache2/sites-available/awlr-system.conf

2. Isi dengan:
   <VirtualHost *:80>
       ServerAdmin admin@awlr.com
       DocumentRoot /var/www/html/awlr-system
       
       <Directory /var/www/html/awlr-system>
           Options Indexes FollowSymLinks
           AllowOverride All
           Require all granted
       </Directory>
       
       ErrorLog ${APACHE_LOG_DIR}/error.log
       CustomLog ${APACHE_LOG_DIR}/access.log combined
   </VirtualHost>

3. Save (Ctrl+X, Y, Enter)

LANGKAH 6: AKTIFKAN KONFIGURASI
================================
1. Enable mod_rewrite:
   sudo a2enmod rewrite

2. Disable default site:
   sudo a2dissite 000-default.conf

3. Enable site kita:
   sudo a2ensite awlr-system.conf

4. Restart Apache:
   sudo systemctl restart apache2

LANGKAH 7: TEST WEBSITE
========================
1. Dari Windows, buka browser:
   http://IP_Server/

2. Login dengan:
   Username: admin
   Password: admin123

LANGKAH 8: TROUBLESHOOTING
===========================
Jika ada error:

1. Cek error log:
   sudo tail -f /var/log/apache2/error.log

2. Cek Apache status:
   sudo systemctl status apache2

3. Test PHP:
   echo "<?php phpinfo(); ?>" | sudo tee /var/www/html/awlr-system/test.php
   Akses: http://IP_Server/test.php
   Hapus: sudo rm /var/www/html/awlr-system/test.php

4. Jika port 80 dipakai Nginx:
   sudo systemctl stop nginx
   sudo systemctl disable nginx

PERINTAH PENTING
================
1. Upload file baru:
   scp -i ~/.ssh/awlr_vps.pem namafile.php nazaprima@103.197.190.77:/var/www/html/awlr-system/

2. Restart Apache:
   sudo systemctl restart apache2

3. Backup database:
   scp -i ~/.ssh/awlr_vps.pem nazaprima@103.197.190.77:/var/www/html/awlr-system/db/awlr_system.db .

4. Monitor log:
   sudo tail -f /var/log/apache2/access.log

ALAMAT URL
==========
- Website: http://IP_Server/
- Login: http://IP_Server/login.php
- Admin: http://IP_Server/admin/
- Dashboard: http://IP_Server/user/dashboard.php

CATATAN
=======
- Pastikan file .pem permission benar: chmod 600 awlr_vps.pem
- Jika VPS restart, Apache auto start
- Database SQLite: /var/www/html/awlr-system/db/awlr_system.db
- File log: /var/log/apache2/

==========================================
DEPLOYMENT SELESAI - WEBSITE ONLINE!
==========================================