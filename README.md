# BT03 - Thiết lập Cơ sở dữ liệu và Tự cấu hình dịch vụ Systemd cho Spring Boot

## Tạo database và user MySQL

```sql
CREATE DATABASE springboot_db;
CREATE USER 'spring-admin'@'localhost' IDENTIFIED BY 'SpringSecure@123';
GRANT ALL PRIVILEGES ON springboot_db.* TO 'spring-admin'@'localhost';
FLUSH PRIVILEGES;
```

## Tạo user chạy ứng dụng

```bash
sudo useradd --system --no-create-home --shell /usr/sbin/nologin spring-runner
```

## Cấu hình dịch vụ

```ini
# /etc/systemd/system/spring-app.service
[Unit]
Description=Spring Boot Application
After=network.target mysql.service

[Service]
User=spring-runner
WorkingDirectory=/opt/spring-app
ExecStart=/usr/bin/java -jar /opt/spring-app/app.jar
Restart=on-failure
RestartSec=10

[Install]
WantedBy=multi-user.target
```

## Kiểm tra

```bash
$ sudo systemctl status spring-app.service
● spring-app.service - Spring Boot Application
     Loaded: loaded (/etc/systemd/system/spring-app.service; enabled; preset: enabled)
     Active: active (running) since Thu 2026-10-08 10:30:00 UTC; 25s ago
   Main PID: 2481 (java)
      Tasks: 28
     Memory: 312.4M
     CGroup: /system.slice/spring-app.service
             └─2481 /usr/bin/java -jar /opt/spring-app/app.jar

$ ss -tlnp | grep 8082
LISTEN 0      100                *:8082             *:*    users:(("java",pid=2481,fd=9))
```