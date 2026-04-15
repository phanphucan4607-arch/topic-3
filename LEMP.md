
Đầu tiên, hãy SSH vào VPS với thông tin: ssh root@221.132.21.141.


**1. Cập nhật hệ thống**

```
apt update && apt upgrade -y
apt install -y curl wget vim zip unzip software-properties-common
```
**2. Cài đặt Nginx (Web Server)**


```
apt install nginx -y
systemctl start nginx
systemctl enable nginx
```

<img width="1841" height="754" alt="image" src="https://github.com/user-attachments/assets/fc2bda80-0673-4385-b825-bfabd4648d50" />
