
Đầu tiên, hãy SSH vào VPS với thông tin được cung cấp: ssh root@221.132.21.141.


## **1. Cập nhật hệ thống**

```
apt update && apt upgrade -y
apt install -y curl wget vim zip unzip software-properties-common
```
## **2. Cài đặt Nginx (Web Server)**


```
apt install nginx -y
systemctl start nginx
systemctl enable nginx
```
Kiểm tra: Truy cập IP http://221.132.21.141, nếu thấy trang chào mừng của Nginx là thành công.

<img width="1841" height="754" alt="image" src="https://github.com/user-attachments/assets/fc2bda80-0673-4385-b825-bfabd4648d50" />

   

## **3. Cài đặt MySQL và Cấu hình Remote**

```
apt install mysql-server -y
```

 **cấu hình remote root**
  ***Sửa file cấu hình cho phép kết nối từ bên ngoài****
  mở file: _nano /etc/mysql/mysql.conf.d/mysqld.cnf_
  tìm dòng: bind-address = 127.0.0.1 và sửa thành 0.0.0.0.
  Lưu (Ctrl+O) và Thoát (Ctrl+X).
  restart cấu hình 

```
systemctl restart mysq
```

  <img width="924" height="252" alt="image" src="https://github.com/user-attachments/assets/4c571ed9-6951-4db7-bfe9-b6a0ba57377b" />

  **Cấp quyền cho user root**
```
mysql
# Trong môi trường mysql:
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '7b6MqeOapWFrEbZanjjA';
CREATE USER 'root'@'%' IDENTIFIED BY '7b6MqeOapWFrEbZanjjA';
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION;
FLUSH PRIVILEGES;
EXIT;

```
## **4. Cài đặt phiên bản PHP 8.1**

```
add-apt-repository ppa:ondrej/php -y
apt update
apt install php8.1-fpm php8.1-mysql php8.1-common php8.1-mysql php8.1-xml php8.1-xmlrpc php8.1-curl php8.1-gd php8.1-imagick php8.1-cli php8.1-dev php8.1-imap php8.1-mbstring php8.1-opcache php8.1-soap php8.1-zip php8.1-intl -y
```

<img width="924" height="252" alt="image" src="https://github.com/user-attachments/assets/02cddc60-4d48-48cc-ac75-a3609a3c5d8e" />

## **5. Cấu hình Virtual Host cho WordPress và Laravel**
Giả sử bạn có 2 domain: _wp.phucan.vietnix.tech_ và _laravel.phucan.vietnix.tech_


Cho WordPress:
Tạo file: 

```
nano /etc/nginx/sites-available/wp.phucan.vietnix.tech
```

thêm nội dung sau vào 

<img width="926" height="368" alt="image" src="https://github.com/user-attachments/assets/7f43e2b6-404d-4303-87a3-4d8e129e1feb" />

Cho Laravel:

Tạo file: 

```
nano /etc/nginx/sites-available/laravel.phucan.vietnix.tech
```

tiếp tục thêm nội dung sau vào 

<img width="926" height="368" alt="image" src="https://github.com/user-attachments/assets/de7c9a2a-fd2b-45b1-9ca3-369caa8b65e4" />

Kích hoạt:
```
ln -s /etc/nginx/sites-available/wp.phucan.vietnix.tech /etc/nginx/sites-enabled/
ln -s /etc/nginx/sites-available/laravel.phucan.vietnix.tech /etc/nginx/sites-enabled/
```

Kiểm tra:

```
nginx -t
```
<img width="723" height="108" alt="image" src="https://github.com/user-attachments/assets/b58e28f2-4294-4cc8-8e8e-01a6f538978f" />

Nginx đã hoạt động. Bây giờ hệ thống đã hiểu đúng các cấu hình cho WordPress và Laravel.

## **6. Cài đặt SSL bằng Certbot**

```
apt install certbot python3-certbot-nginx -y
```

# Chạy lệnh sau và làm theo hướng dẫn để cài SSL tự động và làm theo hình ảnh 

```
certbot --nginx -d wp.phucan.vietnix.tech
```

Chỗ này mình phải nhập email dùng để nhận thông báo khi chứng chỉ sắp hết hạn.
<img width="919" height="184" alt="image" src="https://github.com/user-attachments/assets/2ccfc72c-fbb9-4ae4-be92-3fec08078632" />

<img width="921" height="381" alt="image" src="https://github.com/user-attachments/assets/0b0000b6-f4fe-4e7e-920b-e2f68d35a48a" />

Làm tương:

```
certbot --nginx -d laravel.phucan.vietnix.tech
```

## **7. Cài đặt FTP (vsftpd) và Tạo tài khoản**

```
apt install vsftpd -y
# Tạo user mới (ví dụ: ftpuser)
adduser ftpuser
# Nhập mật khẩu và thông tin
usermod -d /var/www ftpuser
chown -R ftpuser:ftpuser /var/www
```

Để cho phép upload, sửa file /etc/vsftpd.conf:
    Bỏ dấu # trước write_enable=YES.

<img width="919" height="184" alt="image" src="https://github.com/user-attachments/assets/b75ece5a-2b9b-4327-9ea9-40fbd9a521e4" />

Khởi động lại: systemctl restart vsftpd.

## **8. Cài đặt mã nguồn mặc định**
WordPress:

```
mkdir -p /var/www/wp
cd /var/www/wp
wget https://wordpress.org/latest.tar.gz
tar -xvf latest.tar.gz --strip-components=1
rm latest.tar.gz
```
Laravel:

```
curl -sS https://getcomposer.org/installer | php
cd /var/www
rm -rf laravel
composer create-project laravel/laravel laravel
chown -R www-data:www-data /var/www/laravel
chmod -R 775 /var/www/laravel/storage /var/www/laravel/bootstrap/cache
```

<img width="918" height="626" alt="image" src="https://github.com/user-attachments/assets/e2c75d48-4bb4-4720-8802-b05a32473f5c" />


## **9. Kiểm tra kết nối**
 Kiểm tra Web & SSL (HTTPS)
 truy cập 
 https://wp.phucan.vietnix.tech
 https://laravel.phucan.vietnix.tech
 
<img width="1842" height="999" alt="image" src="https://github.com/user-attachments/assets/89878bc8-5f56-42b2-9ce8-693330e5cd9e" />


<img width="1842" height="999" alt="image" src="https://github.com/user-attachments/assets/b3eec1b6-26fb-4261-a6f8-6769bfa85e40" />

