https://github.com/snykk/Laracoffee

# Update Server

sudo apt update && sudo apt upgrade -y

# Install Required Packages

sudo apt install -y software-properties-common git curl unzip mysql-server npm

# Add PHP Repository

sudo add-apt-repository ppa:ondrej/php -y
sudo apt update

# Install PHP 8.1

sudo apt install -y 
php8.1 php8.1-cli php8.1-common 
php8.1-mysql php8.1-mbstring php8.1-xml 
php8.1-curl php8.1-zip php8.1-bcmath 
php8.1-gd php8.1-intl

# Verify PHP

php -v

# Install Composer

curl -sS https://getcomposer.org/installer -o composer-setup.php

sudo php composer-setup.php 
--install-dir=/usr/local/bin 
--filename=composer

composer --version

# Clone Project

git clone https://github.com/snykk/Laracoffee.git

cd Laracoffee

# Install Dependencies

composer install

npm install

# Laravel Setup

cp .env.example .env

php artisan key:generate

# Start MySQL

sudo systemctl enable mysql

sudo systemctl start mysql

# Create Database

sudo mysql

CREATE DATABASE laracoffee;

CREATE USER 'laracoffee'@'localhost'
IDENTIFIED BY 'Laracoffee@123';

GRANT ALL PRIVILEGES
ON laracoffee.*
TO 'laracoffee'@'localhost';

FLUSH PRIVILEGES;

EXIT;

# Configure Database

nano .env

DB_DATABASE=laracoffee
DB_USERNAME=laracoffee
DB_PASSWORD=Laracoffee@123

# Clear Cache

php artisan config:clear

php artisan cache:clear

# Run Migration

php artisan migrate

# Seed Database (Optional)

php artisan db:seed

# Create Storage Link

php artisan storage:link

# Run Application
php artisan serve --host=0.0.0.0 --port=8000

Access Application
http://EC2_PUBLIC_IP:8000
Important

Tumhare pehle wale error ke hisab se pehle ye check kar lo:

sudo rm -f /etc/apt/sources.list.d/*ondrej*
sudo apt update

Uske baad hi:

sudo add-apt-repository ppa:ondrej/php -y

run karna. Warna purani broken repository phir error degi.

php artisan serve --host=0.0.0.0 --port=8000







pipeline 


pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                url: 'https://github.com/snykk/Laracoffee.git'
            }
        }

        stage('Build') {
            steps {
                sh '''
                composer install --no-dev
                npm install
                '''
            }
        }

        stage('Deploy') {
            steps {
                sshagent(['ec2-key']) {
                    sh '''
                    rsync -avz . ubuntu@EC2_IP:/var/www/laracoffee/

                    ssh ubuntu@EC2_IP "
                    cd /var/www/laracoffee

                    php artisan migrate --force
                    php artisan config:cache

                    sudo systemctl restart nginx
                    "
                    '''
                }
            }
        }
    }
}










-------------------------------------


EC2 Par One-Time Setup
1. Required Packages
sudo apt update

sudo apt install -y \
git curl unzip nginx mysql-server npm \
php8.1 php8.1-cli php8.1-fpm \
php8.1-mysql php8.1-mbstring \
php8.1-xml php8.1-curl \
php8.1-zip php8.1-bcmath \
php8.1-gd php8.1-intl
2. Composer Install
curl -sS https://getcomposer.org/installer -o composer-setup.php

sudo php composer-setup.php \
--install-dir=/usr/local/bin \
--filename=composer
3. Create App Directory
sudo mkdir -p /var/www/laracoffee

sudo chown -R ubuntu:ubuntu /var/www/laracoffee
4. Database Setup
sudo mysql
CREATE DATABASE laracoffee;

CREATE USER 'laracoffee'@'localhost'
IDENTIFIED BY 'Laracoffee@123';

GRANT ALL PRIVILEGES
ON laracoffee.*
TO 'laracoffee'@'localhost';

FLUSH PRIVILEGES;

EXIT;
5. Create .env Once
cd /var/www/laracoffee

nano .env

Add:

APP_ENV=production

DB_DATABASE=laracoffee
DB_USERNAME=laracoffee
DB_PASSWORD=Laracoffee@123
6. Nginx Config
sudo nano /etc/nginx/sites-available/laracoffee
server {
    listen 80;
    server_name _;

    root /var/www/laracoffee/public;
    index index.php;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php8.1-fpm.sock;
    }
}

Enable:

sudo ln -s /etc/nginx/sites-available/laracoffee \
/etc/nginx/sites-enabled/

sudo nginx -t

sudo systemctl restart nginx
