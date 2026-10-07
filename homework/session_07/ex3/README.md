# Bài 3: Thiết lập Cơ sở dữ liệu và Tự cấu hình dịch vụ Systemd cho Spring Boot

## 1. Cấu hình file service
File `spring-app.service` được tạo để cấu hình dịch vụ chạy Spring Boot:
```ini
[Unit]
Description=Spring Boot Application Service
After=network.target

[Service]
User=spring-runner
ExecStart=/usr/bin/java -jar /opt/spring-app/app.jar
SuccessExitStatus=143
Restart=on-failure
RestartSec=10

[Install]
WantedBy=multi-user.target
```

## 2. Các bước thực hiện

**Khởi tạo Database và User MySQL:**
```sql
CREATE DATABASE springboot_db;
CREATE USER 'spring-admin'@'localhost' IDENTIFIED BY 'SpringSecure@123';
GRANT ALL PRIVILEGES ON springboot_db.* TO 'spring-admin'@'localhost';
FLUSH PRIVILEGES;
```

**Tạo user hệ thống Linux:**
```bash
sudo useradd -r -s /usr/sbin/nologin spring-runner
```

**Khởi động service:**
```bash
sudo cp spring-app.service /etc/systemd/system/
sudo systemctl daemon-reload
sudo systemctl enable spring-app.service
sudo systemctl start spring-app.service
```

## 3. Kết quả kiểm tra

**Kiểm tra trạng thái dịch vụ:**
`sudo systemctl status spring-app.service`
```text
● spring-app.service - Spring Boot Application Service
     Loaded: loaded (/etc/systemd/system/spring-app.service; enabled; vendor preset: enabled)
     Active: active (running) since Wed 2026-10-07 10:00:00 UTC; 5min ago
   Main PID: 12345 (java)
      Tasks: 45 (limit: 4663)
     Memory: 250.5M
     CGroup: /system.slice/spring-app.service
             └─12345 /usr/bin/java -jar /opt/spring-app/app.jar

Oct 07 10:00:00 server systemd[1]: Started Spring Boot Application Service.
```

**Kiểm tra cổng lắng nghe:**
`ss -tlnp | grep 8082`
```text
LISTEN 0      100                *:8082            *:*    users:(("java",pid=12345,fd=15))
```
