# pkleaphp7hiperf
##Chapter 1. Setting Up the Environment
###Setting up Debian or Ubuntu
####Ubuntu
u14:
```
sudo add-apt-repository ppa:ondrej/php && sudo apt-get update -y && sudo apt-get install nginx -y
sudo apt-get install php7.0 php7.0-fpm php7.0-mysql php7.0-mcrypt php7.0-cli
```

link site-available to site-enabled
```
cd /etc/nginx/sites-available
sudo cp default yd.me.conf
sudo ln –s /etc/nginx /sites-available/yd.me.conf /etc/ nginx/sites-enabled/yd.me.conf
```
open
```
vim /etc/nginx /sites-available/yd.me.conf
```
edit
```
server {
  server_name ip:80;
  root /var/www/html;
  index index.php index.html index.htm;
  location ~ \.php$ {
    fastcgi_pass unix:/var/run/php/php7.0-fpm.sock;
      fastcgi_index index.php;
      include fastcgi_params;
  }
}
```
###Setting up Vagrant
```
vagrant box add rasmus/php7dev
```

##Chapter 2. New Features in PHP 7
###OOP features
####Type hints
#####Scalar type hints
type hint:
```
public function age(int $age){return $age; }
```
call
```
echo $person->age(30);
```
but
```
echo $person->age(30.1);
```
still works.  
   
To make it more restrictive, the following single-line code can be placed at the top of the file:
```
declare(strict_types = 1);
```
#####Return type hints
```
public function age:int(int $age){return $age; }
```



####Namespaces and group use declaration
