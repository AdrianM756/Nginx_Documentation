## Configure Nginx + PHP-FPM Using Unix Sock

In this demo, we will be Configure Nginx + PHP-FPM Using Unix Sock on a ```CentOS``` machine.
<br>

First, make sure to install and enable ```nginx```.
```
yum install nginx -y
```
```
systemctl enable nginx
```
<br>

Navigate to the ```/etc/nginx/nginx.conf``` and add the following:
```
 server {
        listen       8091 ;
        listen       [::]:8091;
        server_name  <name of the server>;
        root /var/www/html;
 index index.php index.html;

 location / {
 try_files $uri $uri/ =404;
 }

 location ~\.php$ {
    include fastcgi_params;
    fastcgi_pass unix:/var/run/php-fpm/default.sock;
    fastcgi_index index.php;
    fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
}

  error_page 404 /404.html;
  error_page 500 502 503 504 /50x.html;
```
<br>

*Explaination:*
<br>

This configuration sets up an Nginx server to handle web traffic on port ```8091```, serving content from ```/var/www/html``` and processing PHP files via ```PHP-FPM```. It includes custom error pages and basic routing logic.

## Key Settings
| Commands |  Description                       |
| :-------- | :-------------------------------- |
| `Port Listening(listen 8091 && listen [::]:8091)`      | 	The server listens on both IPv4 and IPv6 interfaces at port ```8091``` |
| `Server_Name`      | 	Refers to the name of this server. |
| `root /var/www/html`      | All files are served from ```/var/www/html```. |
| `index index.php index.html`      | 	When accessing a directory, ```Nginx``` will look for ```index.php``` first, then ```index.html```. |

## Routing Logic
Root Location ```/```: Uses try_files to verify if the requested URI exists. If not, it returns a 404 error.

**PHP Handling:** PHP files (```.php```) are processed using ```FastCGI```:

* Includes standard ```FastCGI``` parameters.

* Passes requests to the ```PHP-FPM``` socket at ```/var/run/php-fpm/default.sock```.

* Sets ```SCRIPT_FILENAME``` to the correct path for PHP execution.

## Error Handling

**404 Errors:** Redirects to ```/404.html```.

**Server Errors (500–504):** Redirects to ```/50x.html```.
<br>

Save the configuration file and restart the ```nginx``` service.
```
systemctl restart nginx
```
<br>

## Install PHP-FPM

Next, we will install ```PHP-FPM```. on this demo, we will be using version ```8.3```.
```
yum install epel-release -y
yum install https://rpms.remirepo.net/enterprise/remi-release-9.rpm -y
yum module list php
yum module enable php:remi-8.3 -y
yum install php-fpm php php-cli php-common php-mysqlnd php-gd php-xml php-mbstring php-pdo php-opcache -y
```
<br>

Once the installation is finish, start and enable the ```php-fpm``` service.
```
systemctl start php-fpm
```
```
systemctl enable php-fpm
```
<br>

We can use the ```php -v``` command to verify that were running the version ```8.3```.
<br>

## Configure PHP-FPM

Let's configure ```php-fpm``` and ```nginx``` to work together. Navigate and edit ```/etc/php-fpm.d/www.conf```.
```
vi /etc/php-fpm.d/www.conf
```
<br>

Edit the following:
```
user = nginx

group = nginx

listen = /var/run/php-fpm/default.sock

listen.owner = nginx

listen.group = nginx

listen.mode = 0660
```
<br>

Ensure that the directory exists then enable the ```php-fpm``` service.
```
mkdir -p /var/run/php-fpm
```
```
chown -R nginx:nginx /var/run/php-fpm
```
```
systemctl enable --now php-fpm
```
<br>

