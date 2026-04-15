
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
# Chạy lệnh sau và làm theo hướng dẫn để cài SSL tự động
certbot --nginx -d wp.phucan.vietnix.tech
certbot --nginx -d laravel.phucan.vietnix.tech
```





