# Guide_for_windows

# **1. Cài đặt Chocolatey (Package Manager cho Windows)**

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

# **2. Cài đặt nhiều phiên bản PHP**

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

# **3. Cấu hình PHP để chạy song song**

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

# **4. Cài đặt & Cấu hình Nginx**

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
net start nginx
```

# **5. Cài đặt & Cấu hình MySQL**

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

# **6. Cài đặt Xdebug cho từng phiên bản PHP**

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

# **7. Virtual Host project Laravel**

### **🔹 Chỉnh sửa file hosts**

Mở file `C:\Windows\System32\drivers\etc\hosts` với quyền Administrator:
Thêm vào cuối file
```
127.0.0.1 laravel83.test
```
**Test**
Truy cập thử http://laravel83.test/folder1
# **8. Cài đặt Composer**

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

# **9. Cài đặt NodeJS**

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

# **10. Cài đặt dự án mới**

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
### **🔹 Mở cổng 9083**
```powershell
C:\php\php83>php-cgi.exe -b 127.0.0.1:9083
```

### **🔹 Tạo file cấu hình Nginx**

**Tại file `C:\tools\nginx-1.29.0\conf\nginx.conf`:**

```nginx
server {
    listen 80;
    server_name abc.test;
    root C:/task/public;
    index index.php index.html;

    location / {
       root C:/task/public;
       index index.php;
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
# Git flow cơ bản
