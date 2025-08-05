# Guide_for_windows

# 🛠 **1. Cài đặt Chocolatey (Package Manager cho Windows)**

1. **Mở PowerShell với quyền Administrator**

```powershell
# Mở PowerShell as Administrator
```

2. **Cài đặt Chocolatey**

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
```

3. **Kiểm tra cài đặt**

```powershell
choco --version
```

4. **Cập nhật Chocolatey**

```powershell
choco upgrade all -y
```

# 🐘 **2. Cài đặt nhiều phiên bản PHP**

### **🔹 Cài đặt PHP thủ công **

1. **Tải PHP từ trang chủ**
   - Truy cập: https://windows.php.net/download/
   - Tải các phiên bản: 8.1, 8.2, 8.3, 8.4

2. **Giải nén vào thư mục riêng**
   ```
   C:\php\php81\
   C:\php\php82\
   C:\php\php83\
   C:\php\php84\
   ```

3. **Cấu hình PATH cho từng phiên bản**
   - Mở System Properties > Advanced > Environment Variables
   - Thêm đường dẫn vào PATH:
   ```
   C:\php\php81;C:\php\php82;C:\php\php83;C:\php\php84
   ```

### **🔹 Kiểm tra phiên bản PHP**

```powershell
php -v
```

# ⚡ **3. Cấu hình PHP để chạy song song**

**Tạo file `php.ini` cho từng phiên bản:**

```ini
# C:\php\php83\php.ini
[PHP]
extension_dir = "C:\php\php83\ext"
extension=curl
extension=gd
extension=mbstring
extension=openssl
extension=pdo_mysql
extension=mysqli
extension=fileinfo

date.timezone = Asia/Ho_Chi_Minh
```

**Chạy phiên bản PHP**

```ini
# cmd
C:\php\php83>php-cgi.exe -b 127.0.0.1:9083
```

**Bảng cổng cho các phiên bản PHP:**

| **PHP** | **Cổng FPM** |
|---------|---------------|
| PHP 8.1 | `9081` |
| PHP 8.2 | `9082` |
| PHP 8.3 | `9083` |
| PHP 8.4 | `9084` |

# 🌐 **4. Cài đặt & Cấu hình Nginx**

### **🔹 Cài đặt Nginx**

```powershell
choco install nginx
```

### **🔹 Cấu hình Nginx**

**File `C:\tools\nginx-1.29.0\conf\nginx.conf`:**

```nginx
worker_processes  1;

events {
    worker_connections  1024;
}

http {
    upstream php81 {
        server 127.0.0.1:9081;
    }
    upstream php82 {
        server 127.0.0.1:9082;
    }
    upstream php83 {
        server 127.0.0.1:9083;
    }
    upstream php84 {
        server 127.0.0.1:9084;
    }



    include       mime.types;
    default_type  application/octet-stream;
    sendfile        on;
    keepalive_timeout  65;

    server {
        listen       80;
        server_name  localhost;

        root   C:/xampp/htdocs;
        index  index.php index.html index.htm;

        location /folder1/ {
            root C:/xampp/htdocs;
            index index.php;
            try_files $uri $uri/ /folder1/index.php?$query_string;

            location ~ ^/folder1/.*\.php$ {
                include       fastcgi_params;
                fastcgi_pass  php81;
                fastcgi_index index.php;
                fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
            }
        }

        location /folder2/ {
            root C:/xampp/htdocs;
            index index.php;
            try_files $uri $uri/ /folder2/index.php?$query_string;

            location ~ ^/folder2/.*\.php$ {
                include       fastcgi_params;
                fastcgi_pass  php82;
                fastcgi_index index.php;
                fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
            }
        }

        location /folder3/ {
            root C:/xampp/htdocs;
            index index.php;
            try_files $uri $uri/ /folder3/index.php?$query_string;

            location ~ ^/folder3/.*\.php$ {
                include       fastcgi_params;
                fastcgi_pass  php83;
                fastcgi_index index.php;
                fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
            }
        }

        location /folder4/ {
            root C:/xampp/htdocs;
            index index.php;
            try_files $uri $uri/ /folder4/index.php?$query_string;

            location ~ ^/folder4/.*\.php$ {
                include       fastcgi_params;
                fastcgi_pass  php84;
                fastcgi_index index.php;
                fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
            }
        }
    }
}

```
### **Chạy nginx(với quyền admin)**

```ini
# powershell
PS C:\tools\nginx-1.29.0> nginx
```

# 💾 **5. Cài đặt & Cấu hình MySQL**

### **🔹 Cài đặt MySQL**

```powershell
choco install mysql
```

### **🔹 Đăng nhập vào MySQL**

```powershell
mysql -u root -p
```
**Chạy lệnh sau để đổi mật khẩu**
```powershell
ALTER USER 'root'@'localhost' IDENTIFIED BY 'matkhau_moi';
FLUSH PRIVILEGES;
```

# 🐞 **6. Cài đặt Xdebug cho từng phiên bản PHP**

### **🔹 Tải Xdebug**
1.**Mở powershell, chạy lệnh bên dưới và copy output**
```powershell
C:\php\php83\php.exe -i > phpinfo83.txt
notepad phpinfo83.txt
```
2. **Truy cập: https://xdebug.org/wizard**
3. **Nhập thông tin PHP(vừa copy), nhấn analyse my phpinfo() output**
3. **Tải file .dll phù hợp, làm theo hướng dẫn của web**

### **🔹 Cấu hình Xdebug**

**Thêm vào file `php.ini` của từng phiên bản:**

```ini
# C:\php\php83\php.ini
[xdebug]
zend_extension="C:\php\php83\ext\php_xdebug.dll"
xdebug.mode=debug
xdebug.start_with_request=yes
xdebug.client_host=127.0.0.1
xdebug.client_port=9183
```

**Bảng cổng Xdebug:**

| **PHP** | **Xdebug Port** |
|---------|-----------------|
| PHP 8.1 | `9181` |
| PHP 8.2 | `9182` |
| PHP 8.3 | `9183` |
| PHP 8.4 | `9184` |

# 🌀 **7. Virtual Host project Laravel**

### **🔹 Chỉnh sửa file hosts**

Mở file `C:\Windows\System32\drivers\etc\hosts` với quyền Administrator:
Thêm vào cuối file
```
127.0.0.1 laravel83.test
```
**Test**
Truy cập thử http://laravel83.test/folder1
# 🍺 **8. Cài đặt Composer**

### **🔹 Cài đặt Composer**

```powershell
choco install composer
```

### **🔹 Cài đặt thủ công**

1. **Tải Composer từ trang chủ**
   - Truy cập: https://getcomposer.org/download/
   - Tải Composer-Setup.exe

2. **Chạy file cài đặt**
   - Chọn PHP executable phù hợp

### **🔹 Kiểm tra cài đặt**

```powershell
composer --version
```

# 🔆 **9. Cài đặt NodeJS**

### **🔹 Cài đặt NodeJS**

```powershell
choco install nodejs
```

### **🔹 Cài đặt thủ công**

1. **Tải NodeJS từ trang chủ**
   - Truy cập: https://nodejs.org/
   - Tải phiên bản LTS

2. **Cài đặt NodeJS**
   - Chạy file .msi
   - Chọn "Add to PATH"

### **🔹 Cài đặt Yarn**

```powershell
npm install -g yarn
```

### **🔹 Kiểm tra cài đặt**

```powershell
node --version
npm --version
yarn --version
```

# 🌟 **10. Cài đặt dự án mới**

### **🔹 Clone dự án**

```powershell
git clone git@github.com:Xusux/abc.git
cd abc
```

### **🔹 Cài đặt dự án**

```powershell
# Install composer dependencies
composer install

# Copy env file
copy .env.example .env

# Generate key
php artisan key:generate

# Edit .env file
notepad .env
```

**Cấu hình database trong `.env`:**
```env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=your_database_name
DB_USERNAME=root
DB_PASSWORD=your_password

APP_URL=http://abc.test
```

### **🔹 Cài đặt Node modules**

```powershell
yarn install
```

### **🔹 Chạy dự án**

```powershell
yarn dev
```

### **🔹 Tạo file cấu hình Nginx**

**Tạo file `C:\nginx\conf\sites-enabled\abc.conf`:**

```nginx
server {
    listen 80;
    server_name abc.test;
    root C:/Users/YourUsername/Projects/abc/public;
    index index.php index.html;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        include fastcgi_params;
        fastcgi_pass php83;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }
}
```

### **🔹 Restart Nginx**

```powershell
cd C:\nginx
nginx -s reload
```

# 💢 **11. Lỗi thường gặp & cách khắc phục**

## 🔹 **Lỗi khi start Nginx**

### **1. Kiểm tra trạng thái Nginx**

```powershell
tasklist /fi "imagename eq nginx.exe"
```

### **2. Kiểm tra cổng 80**

```powershell
netstat -an | findstr :80
```

### **3. Kiểm tra log lỗi**

```powershell
type C:\nginx\logs\error.log
```

### **4. Khởi động lại Nginx**

```powershell
cd C:\nginx
nginx -s stop
start nginx
```

## 🔹 **Lỗi khi start PHP-FPM**

### **1. Kiểm tra PHP-FPM**

```powershell
tasklist /fi "imagename eq php-cgi.exe"
```

### **2. Khởi động PHP-FPM**

```powershell
cd C:\php\php83
php-cgi.exe -b 127.0.0.1:9083
```

### **3. Tạo batch file để khởi động PHP-FPM**

**Tạo file `start-php-fpm.bat`:**

```batch
@echo off
cd /d C:\php\php74
start php-cgi.exe -b 127.0.0.1:9074

cd /d C:\php\php80
start php-cgi.exe -b 127.0.0.1:9080

cd /d C:\php\php81
start php-cgi.exe -b 127.0.0.1:9081

cd /d C:\php\php82
start php-cgi.exe -b 127.0.0.1:9082

cd /d C:\php\php83
start php-cgi.exe -b 127.0.0.1:9083

cd /d C:\php\php84
start php-cgi.exe -b 127.0.0.1:9084
```

## 🔹 **Lỗi không đăng nhập được MySQL**

### **1. Reset mật khẩu MySQL**

```sql
-- Kết nối MySQL
mysql -u root -p

-- Đặt lại mật khẩu
ALTER USER 'root'@'localhost' IDENTIFIED BY 'new_password';
FLUSH PRIVILEGES;
EXIT;
```

### **2. Kiểm tra service MySQL**

```powershell
net start mysql
```

## 🔹 **Lỗi upload file Laravel**

### **1. Chỉnh sửa php.ini**

Tìm và sửa trong file `php.ini`:

```ini
upload_max_filesize = 10M
post_max_size = 12M
max_execution_time = 300
memory_limit = 256M
```

### **2. Restart PHP-FPM**

```powershell
taskkill /f /im php-cgi.exe
# Sau đó chạy lại start-php-fpm.bat
```

## 🔹 **Lỗi permission**

### **1. Cấp quyền cho thư mục**

```powershell
icacls "C:\nginx\logs" /grant Everyone:F
icacls "C:\php\php74\tmp" /grant Everyone:F
```

### **2. Chạy với quyền Administrator**

Luôn chạy PowerShell với quyền Administrator khi cài đặt và cấu hình.

## 🔹 **Lỗi PATH không nhận diện**

### **1. Kiểm tra PATH**

```powershell
echo $env:PATH
```

### **2. Thêm vào PATH**

Mở System Properties > Advanced > Environment Variables và thêm:

```
C:\php\php74;C:\php\php80;C:\php\php81;C:\php\php82;C:\php\php83;C:\php\php84;C:\nginx;C:\composer
```

## 🔹 **Lỗi Xdebug không hoạt động**

### **1. Kiểm tra Xdebug**

```powershell
php -m | findstr xdebug
```

### **2. Tạo file test**

Tạo file `phpinfo.php`:

```php
<?php
phpinfo();
?>
```

Truy cập `http://localhost/phpinfo.php` và tìm thông tin Xdebug.

## 🔹 **Lỗi Composer**

### **1. Cài đặt lại Composer**

```powershell
choco uninstall composer
choco install composer
```

### **2. Kiểm tra PHP cho Composer**

```powershell
composer diagnose
```

## 🔹 **Lỗi NodeJS/Yarn**

### **1. Cài đặt lại NodeJS**

```powershell
choco uninstall nodejs
choco install nodejs
```

### **2. Cài đặt lại Yarn**

```powershell
npm uninstall -g yarn
npm install -g yarn
```

# 📋 **12. Script tự động hóa**

### **🔹 Tạo file `setup-windows.bat`**

```batch
@echo off
echo Setting up Windows development environment...

REM Create directories
mkdir C:\php\php74\tmp
mkdir C:\php\php80\tmp
mkdir C:\php\php81\tmp
mkdir C:\php\php82\tmp
mkdir C:\php\php83\tmp
mkdir C:\php\php84\tmp

REM Set permissions
icacls "C:\nginx\logs" /grant Everyone:F
icacls "C:\php\php74\tmp" /grant Everyone:F
icacls "C:\php\php80\tmp" /grant Everyone:F
icacls "C:\php\php81\tmp" /grant Everyone:F
icacls "C:\php\php82\tmp" /grant Everyone:F
icacls "C:\php\php83\tmp" /grant Everyone:F
icacls "C:\php\php84\tmp" /grant Everyone:F

REM Start services
echo Starting Nginx...
cd /d C:\nginx
start nginx

echo Starting PHP-FPM services...
call start-php-fpm.bat

echo Setup complete!
pause
```

### **🔹 Tạo file `stop-services.bat`**

```batch
@echo off
echo Stopping all services...

REM Stop Nginx
cd /d C:\nginx
nginx -s stop

REM Stop PHP-FPM
taskkill /f /im php-cgi.exe

echo All services stopped!
pause
```

# 🎯 **13. Kiểm tra hệ thống**

### **🔹 Kiểm tra tất cả services**

```powershell
# Kiểm tra Nginx
netstat -an | findstr :80

# Kiểm tra PHP-FPM
netstat -an | findstr :9074
netstat -an | findstr :9080
netstat -an | findstr :9081
netstat -an | findstr :9082
netstat -an | findstr :9083
netstat -an | findstr :9084

# Kiểm tra MySQL
netstat -an | findstr :3306
```

### **🔹 Test các phiên bản PHP**

Tạo file `test-php.php`:

```php
<?php
echo "PHP Version: " . phpversion() . "\n";
echo "Server: " . $_SERVER['SERVER_SOFTWARE'] . "\n";
echo "Xdebug: " . (extension_loaded('xdebug') ? 'Loaded' : 'Not loaded') . "\n";
?>
```

### **🔹 Test database connection**

```php
<?php
try {
    $pdo = new PDO("mysql:host=127.0.0.1;dbname=test", "root", "password");
    echo "Database connection successful!\n";
} catch(PDOException $e) {
    echo "Connection failed: " . $e->getMessage() . "\n";
}
?>
```

---

# 📝 **Ghi chú quan trọng**

1. **Luôn chạy PowerShell với quyền Administrator**
2. **Kiểm tra Windows Defender và Firewall**
3. **Backup dữ liệu trước khi cài đặt**
4. **Sử dụng đường dẫn tuyệt đối trong cấu hình**
5. **Kiểm tra log thường xuyên**
6. **Cập nhật Windows và các phần mềm định kỳ**

# 🔗 **Tài liệu tham khảo**

- [Chocolatey Documentation](https://docs.chocolatey.org/)
- [PHP for Windows](https://windows.php.net/)
- [Nginx for Windows](http://nginx.org/en/docs/windows.html)
- [MySQL for Windows](https://dev.mysql.com/doc/refman/8.0/en/windows-installation.html)
- [Xdebug Documentation](https://xdebug.org/docs/)
- [Composer Documentation](https://getcomposer.org/doc/)
- [Node.js for Windows](https://nodejs.org/en/docs/) 
