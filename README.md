Step 0: Hume kya-kya chahiye?
1. Laravel Project

Ye website ka source code hai.

Example:

my-laravel-app/
2. Linux Server

Example:

AWS EC2 Ubuntu
GCP VM
Azure VM

Interview me mostly AWS EC2 hota hai.

3. PHP

Laravel PHP me likha hota hai.

PHP ke bina Laravel run nahi hoga.

4. Composer

Laravel ki dependencies install karta hai.

Jaise:

vendor/

folder Composer banata hai.

5. Web Server

Commonly:

Apache
Nginx

Ye browser request receive karta hai.

6. Database

Mostly:

MySQL
MariaDB

Laravel data yaha store karta hai.

Step 1: EC2 Launch

AWS me:

Ubuntu Server Launch

Example:

Ubuntu 22.04
t2.micro

Purpose:

Laravel run karne ke liye machine chahiye.

Step 2: SSH Login
ssh -i key.pem ubuntu@public-ip

Meaning:

Apne laptop se server me login kar rahe ho.

Step 3: Update Server
sudo apt update
sudo apt upgrade -y

Meaning:

Latest packages install karo.

Step 4: Install Nginx
sudo apt install nginx -y

Check:

systemctl status nginx

Purpose:

Browser requests handle karega.

Step 5: Install PHP
sudo apt install php php-fpm php-mysql -y

Purpose:

Laravel PHP application hai.

Without PHP:

Laravel run nahi hoga
Step 6: Install Composer
sudo apt install composer -y

Check:

composer --version

Purpose:

Laravel packages install karna.

Step 7: Install MySQL
sudo apt install mysql-server -y

Check:

mysql --version

Purpose:

Database store karna.

Step 8: Create Database

Login:

sudo mysql

Create:

CREATE DATABASE laraveldb;

Purpose:

Laravel data save karega.

Step 9: Get Source Code

GitHub se:

git clone https://github.com/project.git

OR

Jenkins se deploy.

Purpose:

Application code server par lana.

Step 10: Go Project Folder
cd project
Step 11: Install Dependencies
composer install

Purpose:

Vendor folder create hoga.

Laravel ki libraries install hongi.

Step 12: Configure Environment

Copy:

cp .env.example .env

Purpose:

Laravel settings store karta hai.

Step 13: Update Database Details
DB_DATABASE=laraveldb
DB_USERNAME=root
DB_PASSWORD=password

Purpose:

Laravel ko database ka address batana.

Step 14: Generate App Key
php artisan key:generate

Purpose:

Laravel encryption key create karta hai.

Without this:

Application key missing

error milega.

Step 15: Run Migration
php artisan migrate

Purpose:

Database tables create karna.

Example:

users
password_resets
jobs
Step 16: Permissions
sudo chmod -R 775 storage
sudo chmod -R 775 bootstrap/cache

Purpose:

Laravel logs aur cache write kar sake.

Step 17: Configure Nginx

File:

sudo nano /etc/nginx/sites-available/laravel

Basic config:

server {
    listen 80;

    root /var/www/project/public;

    index index.php;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        fastcgi_pass unix:/run/php/php8.2-fpm.sock;
        include fastcgi_params;
    }
}

Purpose:

Nginx ko batana ki Laravel kaha hai.

Step 18: Enable Site
sudo ln -s /etc/nginx/sites-available/laravel /etc/nginx/sites-enabled/

Check:

sudo nginx -t

Reload:

sudo systemctl reload nginx
Step 19: Open Browser
http://PUBLIC-IP

Laravel website open.

Production / Live Website

Ab interviewer bole:

Live bhi karo

To extra steps:

Step 20: Buy Domain

Example:

mywebsite.com

Purpose:

IP yaad nahi rakhni padti.

Step 21: Point Domain to Server

DNS:

A Record

Example:

mywebsite.com -> EC2 IP

Purpose:

Domain ko server se connect karna.

Step 22: SSL Install
sudo apt install certbot python3-certbot-nginx -y

Run:

sudo certbot --nginx

Purpose:

HTTP → HTTPS

Browser me:

🔒 Secure

show hoga.

Docker Way (Interview Favorite)

Interviewer DevOps wala ho to wo Docker puch sakta hai.

Flow:

Laravel Code
↓
Dockerfile
↓
Docker Image
↓
Docker Container
↓
Website Running

Commands:

docker build -t laravel-app .

Image ban gayi.

Run:

docker run -d -p 80:80 laravel-app

Container chal gaya.

Jenkins CI/CD Flow (Most Important)

Tumhare interview ke hisab se:

Developer Code Push
↓
GitHub
↓
Jenkins Trigger
↓
Build
↓
Composer Install
↓
Docker Build
↓
Docker Push
↓
EC2 Deploy
↓
Website Live

Ye flow tum confidently bol sakte ho.

Practice Karne Ke Liye Easy Project

Apne laptop par:

composer create-project laravel/laravel demo-app

Run:

cd demo-app
php artisan serve

Open:

http://127.0.0.1:8000

Phir:

git init

GitHub push karo.

Phir EC2 par deploy karo.

Phir Docker me run karo.

Phir Jenkins se automate karo.
