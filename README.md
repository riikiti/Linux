# Linux

## Установка всего

### NGINX
- `sudo apt update`
- sudo apt install nginx`
- sudo systemctl reload nginx`

### MYSQL
- `sudo apt install mysql-server`
- `sudo mysql`
- `sudo mysql_secure_installation`
- `SELECT user,authentication_string,plugin,host FROM mysql.user;`
- `ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';`
- `CREATE USER 'username'@'host' IDENTIFIED WITH mysql_native_password BY 'password';`
- `CREATE DATABASE laravel;`
- `GRANT ALL ON laravel.* TO 'laravel'@'localhost';`
- `FLUSH PRIVILEGES;`

### PHP-FPM-8.2
- `sudo apt update && sudo apt install -y software-properties-common`
- `sudo add-apt-repository ppa:ondrej/php`
- `sudo apt update`
- `sudo apt install php8.3-fpm`

 ### GIT
 `sudo apt install git`

 ### COMPOSER
- `sudo apt install php-cli unzip`
- `curl -sS https://getcomposer.org/installer -o /tmp/composer-setup.php`
- `HASH=`curl -sS https://composer.github.io/installer.sig``
- `echo $HASH`
- `php -r "if (hash_file('SHA384', '/tmp/composer-setup.php') === '$HASH') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"`
- `sudo php /tmp/composer-setup.php --install-dir=/usr/local/bin --filename=composer`
- `composer`


 ### PHP Extensions
- `sudo apt-get install -y php-common php-mysql php-zip php-gd php-mbstring php-curl php-xml php-bcmath php-pgsql`
- `apt-get install php8.3-intl`

 ### Git clone
 - `cd ~/../var/www/`
 - `git clone {repository}`
 - `composer update --ignore-platform-req=ext-intl `

 ### Setup project
 - `cp .env.example .env`
 - `php artisan key:generate`
 - `php artisan storage:link`
 - If laravel.log error
 1)`chmod -R 755 /var/www/security/storage`
 2)`chmod -R 755 /var/www/security/storage/logs`
 3)`chown -R www-data:www-data /var/www/security/storage`
 4)`chown -R www-data:www-data /var/www/security/storage/logs`
 5)`touch /var/www/security/storage/logs/laravel.log`

### New nginx link
`ln -s /etc/nginx/sites-available/security security` (be here `/etc/nginx/sites-enabled/security`) `

### LetsEncrypt
- `apt install certbot python3-certbot-nginx`
- `certbot —nginx -d security-diplom.ru`

### Crontab 
- new sertificate `0 0 * * 1 certbot renew —quiet ` 
- schedule:run `* * * * * php /var/www/notifications/artisan schedule:run »/dev/null`
- storage `* * * * * chown -R www-data:www-data /var/www/notifications/storage`

### Supervisor
`sudo apt-get install supervisor`

Supervisor conf 
`
[program:laravel-worker]
process_name=%(program_name)s_%(process_num)02d
command=php /var/www/bikini/source-code/backend/artisan queue:work —sleep=3 —tries=3 —max-time=7200
autostart=true
autorestart=true
stopasgroup=true
killasgroup=true
user=root
numprocs=8
redirect_stderr=true
stdout_logfile=/var/www/bikini/source-code/backend/public/worker.log`

Restart workers 
`stopwaitsecs=3600`

`sudo supervisorctl reread`

`sudo supervisorctl update`

`sudo supervisorctl start "laravel-worker:*"`
