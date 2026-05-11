# BT3_MaNguonMo
Bài tập 03 của sinh viên: K225480106057 - Phạm Mạnh Quỳnh - Môn Phát triển ứng dụng với mã nguồn mở-TEE0421

Lớp: 58KTPM

Bài tập 03:

# SỬ DỤNG WORDPRESS ĐỂ TẠO WEB SITE
## deadline : 23h59 ngày 12 tháng 5 năm 2026.

1. SỬ DỤNG DOCKER TRÊN UBUNTU ĐỂ TẠO docker ccompose chứa:
- Mariadb: sử dụng image: mariadb:latest để làm hệ quản trị csdl cho wordpress
- Phpmyadmin: sư dụng image: phpmyadmin:latest để đăng nhập vào mariadb rồi tạo csdl trống (chỉ để xem, ko cần tạo bảng từ đây, wordpress sẽ làm hết)
- WordPress: Sử dụng image: wordpress:latest, truyền các tham số môi trường cho wordpress là các thông tin truy cập csdl mariadb, tạo bởi Phpmyadmin
2. Yêu cầu: sau khi có 3 service này trong file docker-compose.yml :
- Cấu hình để hệ thống chạy
- Sử dụng cloudflare tunnel để public web này lên 1 sub-domain
- Tạo 1 bài viết trong wordpress giới thiệu về bản thân sinh viên: thông tin cá nhân, sở thích, ... bài viết có thể chứa hình ảnh, âm thanh, video, ...
- Tạo 1 bài viết trong wordpress giới thiệu về ngành học mà em yêu thích trong trường TNUT. bài viết phải chứa hình ảnh, video, ...
- Nhận xét việc sử dụng mã nguồn mở wordpress để tạo website (tốn công sức thế nào, dễ/khó dùng ra sao, tốn kém tài nguyên(ssh/ram) của máy chủ ra sao,....)

----------------------------------------------------------------------------------------------------
# Bài Làm:
## PHẦN 1: GIỚI THIỆU
1. Mục tiêu bài tập: xây dựng một hệ thống web gồm 3 thành phần:
- MariaDB → hệ quản trị cơ sở dữ liệu
- phpMyAdmin → giao diện quản lý database
- WordPress → hệ thống website blog

Tất cả chạy bằng Docker Compose trên Ubuntu

Sau đó dùng Cloudflare Tunnel để public ra internet

## PHẦN 2: CÀI ĐẶT MÔ HÌNH DOCKER COMPOSE

Chúng ta sẽ bỏ qua bước cấu hình máy ảo (VM) và ssh do không thuộc bài tập.

### Bước 1: Cài Docker và Tạo thư mục project

sử dụng lệnh ```sudo apt install docker.io docker-compose -y``` để cài đặt docker và docker-compose:

<img width="911" height="273" alt="Screenshot 2026-05-11 205637" src="https://github.com/user-attachments/assets/18c58ad4-2fe0-4b4e-8581-d7a789096868" />

<img width="1912" height="542" alt="Screenshot 2026-05-11 205703" src="https://github.com/user-attachments/assets/b7035a1a-6d68-41fe-b498-7db375be8c64" />

kiểm tra xem cài đặt thành công chưa:

<img width="705" height="185" alt="Screenshot 2026-05-11 205951" src="https://github.com/user-attachments/assets/3db36a4a-136b-4b07-a39b-3003b350c9f4" />

sử dụng lệnh ```mkdir wordpress``` để tạo foler wordpress chứa các file cấu hình hệ thống.

### Bước 2: Tạo file docker-compose.yml

dùng lệnh ```sudo nano docker-compose.yml``` để tạo file docker-compose:
<img width="1811" height="971" alt="Screenshot 2026-05-11 210223" src="https://github.com/user-attachments/assets/40b3e078-80ea-4829-9530-3f18e4d5647e" />

```
version: '3.8'

services:

  mariadb:
    image: mariadb:latest
    container_name: mariadb
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: rootpassword
      MYSQL_DATABASE: wordpress_db
      MYSQL_USER: wp_user
      MYSQL_PASSWORD: wp_password
    volumes:
      - mariadb_data:/var/lib/mysql
    networks:
      - wp_network

  phpmyadmin:
    image: phpmyadmin:latest
    container_name: phpmyadmin
    restart: always
    ports:
      - "8081:80"
    environment:
      PMA_HOST: mariadb
      MYSQL_ROOT_PASSWORD: rootpassword
    depends_on:
      - mariadb
    networks:
      - wp_network

  wordpress:
    image: wordpress:latest
    container_name: wordpress
    restart: always
    ports:
      - "8080:80"
    environment:
      WORDPRESS_DB_HOST: mariadb:3306
      WORDPRESS_DB_NAME: wordpress_db
      WORDPRESS_DB_USER: wp_user
      WORDPRESS_DB_PASSWORD: wp_password
    depends_on:
      - mariadb
    volumes:
      - wordpress_data:/var/www/html
    networks:
      - wp_network

volumes:
  mariadb_data:
  wordpress_data:

networks:
  wp_network:
```

chạy hệ thống: ```docker-compose up -d```:
<img width="1495" height="1001" alt="Screenshot 2026-05-11 211449" src="https://github.com/user-attachments/assets/217a273e-fde2-48da-aa35-d4e101608b0b" />

Kiểm tra: ```docker ps```
<img width="1852" height="197" alt="Screenshot 2026-05-11 211548" src="https://github.com/user-attachments/assets/ff0e3392-1a36-4a22-a815-270059d26d05" />

Truy cập các trang hệ thống:

Wordpress: 
<img width="1897" height="1060" alt="Screenshot 2026-05-11 211728" src="https://github.com/user-attachments/assets/1ed053f2-7a87-4d0d-9508-75d0a5d72ff7" />

phpMyAdmin: 
<img width="1918" height="1078" alt="Screenshot 2026-05-11 211734" src="https://github.com/user-attachments/assets/ed2e1ab8-a7dc-42a1-bcd7-e7e0c6db92a6" />

đăng nhập vào phpMyAdmin bằng tài khoản _root_, password _rootpassword_

<img width="943" height="663" alt="Screenshot 2026-05-11 212214" src="https://github.com/user-attachments/assets/84fb819b-522e-4819-bc51-1914f37c9436" />
