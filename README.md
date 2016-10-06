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
Ex:
```
//x.php
namespace A\B;
class X{
  public function m(){}
}
```
```
//y.php
namespace A\B;
class Y{
  public function n(){}
}
```
```
//functions.php
namespace A/B;
function f():string{}
```

uae namespace:
```
use A\B\X;
use A\B\Y;
$x=new X();
$y=new Y();
```
we can group it:
#####Mixed group use declarations
```
use A\B\{X,Y,function f};
```

####The anonymous classes
ex
```
$name = new class() {
  public function __construct()
  {
    echo 'ke';
  }
};
```
Or, with a argument
```
$name = new class('ke') {
  public function __construct(string $name)
  {
    echo $name;
  }
};
```

####The throwable interface
1 Error
```
function iHaveError($object){return $object->iDontExist();}
```
handle this error
```
try 
{
  iHaveError(null);
} catch(Error $e){
  echo $e->getMessage();
}
```
2 DivisionByZeroError
```
try
{
  $a = 20;
  $division = $a / 0;
} catch(DivisionByZeroError $e) {
  echo $e->getMessage();
}
```

###New operators
####The Spaceship operator (<=>)
rule:
- It returns 0 if both the operands on left- and right-hand sides are equal
- It returns -1 if the right operand is greater than the left operand
- It returns 1 if the left operand is greater than the right one  

```
echo 1<=>2 //-1
```
ex, sorting function
```
function normal_sort($a, $b) : int 
{
  if( $a == $b )
    return 0;
  if( $a < $b )
    return -1;
  return 1;
}

function space_sort($a, $b) : int
{
  return $a <=> $b;
}
```
####The null coalesce operator(??)
```
$post = isset($_POST['title']) ? $_POST['title'] : NULL;  //old
$post = $_POST['title'] ?? NULL;  //new
```
It can be chained.
```
$title = $_POST['title'] ?? $_GET['title'] ?? 'No POST or GET';
```




###Uniform variable syntax
```
$first = ['name' => 'second'];
$second = 'Howdy';
echo $$first['name'];   //Prints Howdy in 5.6, failed in 7
```
to make it work,
```
echo ${$first['name']};
```

###Miscellaneous features and changes
####Constant arrays
```
const STORES = ['en', 'fr', 'ar'];    //starting from 5.6
define('STORES', ['en', 'fr', 'ar']);   //starting from 7
```
####Multiple default cases in the switch statement
not allowed in 7.

####The options array for session_start function
7:allow arguments in aession_start
```
session_start(['cookie_lifetime' => 3600,'read_and_close'  => true]);
```

##Chapter 3. Improving PHP 7 Application Performance
###HTTP server optimization
####Caching static files
#####Apache
```
<FiledMatch "\.(ico|jpg|png|gif|css|js|woff)$">
Header set Cache-Control "max-age=604800, public"
</FilesMatch>
```
#####nginx
```
Location ~* .(ico|jpg|png|gif|css|js|woff)$ {
  Expires 7d;
}
```

###HTTP persistent connection
HTTP keep-alive, a single TCP/IP connection is used for multiple requests or responses.  
Benefits of the HTTP keep-alive:
- The load on the CPU and memory is reduced because fewer TCP connections are opened at a time.
- Reduces latency in subsequent requests after the TCP connection is established.   
- Network congestion is reduced because only a few TCP connections are opened to the server at a time.  


####Apache
.htaccess
```
<ifModule mod_headers.c>
  Header set Connection keep-alive
</ifModule>
```
and add following
```
KeepAlive On
MaxKeepAliveRequests 100
KeepAliveTimeout 100
```
####nginx
```
keepalive_requests 100
keepalive_timeout 100
```



####GZIP compression
#####Apache
```
<IfModule mod_deflate.c>
  SetOutputFilter DEFLATE
  filters to different content types
  AddOutputFilterByType DEFLATE text/html text/plain text/xml    text/css text/javascript application/javascript
  #Don't compress images
  SetEnvIfNoCase Request_URI \.(?:gif|jpe?g|png)$ no-gzip dont-vary
</IfModule>
```

#####nginx
```
gzip on;
gzip_vary on;   //used to enable varying headers
gzip_types text/plain text/xml text/css text/javascript application/x-javascript;
gzip_com_level 4; //1-9
```

####Disabling unused modules
```
apachectl –M
nginx -V
```

####Web server resources
#####nginx
Simply speaking, ```worker_connections``` tells NGINX how many simultaneous requests can be handled by NGINX.  
check the correct number
```
ulimit -n
```
####Content Delivery Network (CDN)
Each browser has limitations for sending simultaneous requests to a domain. Mostly, it's three requests. 



####Varnish
If install 5.0, select from
```
https://repo.varnish-cache.org/pkg/5.0.0/
```
old 4.1 version,
```
deb https://repo.varnish-cache.org/debian/ Jessie varnish-4.1
sudo apt-get update
sudo apt-get install varnish
```
The first thing to do is configure Varnish to listen at port 80 and make your web server listen at another port, such as 8080.then
```
vim /etc/default/varnish
```
edit
```
DAEMON_OPS="-a :80 \
  -T localhost:6082 \ 
  -f /etc/varnish/default.vcl \
  -S /etc/varnish/secret \
  -s malloc,256m"
```
```
sudo service varnish restart
```
for nginx, change
```
listen 8080;
```
```
sudo service nginx restart
```
edit
```
/etc/varnish/default.vcl
```
edit
```
   backend default {
      .host = "127.0.0.1";
      .port = "8080";
    }
```
get stat:
```
varnishstat
```
####HAProxy load balancing
#####HAProxy installation
```
vim /etc/haproxy/haproxy.cfg
```
a config
```
frontend http
  bind *:80
  mode http
  default_backend web-backends
  
backend web-backend 
  mode http
  balance roundrobin
  option forwardfor
  server web1 1.0.0.1:8080 check
  server web2 1.0.0.0:8080 check
```
stat page
```
listen stats *:1434
  stats enable
  stats uri /haproxy-stats
  stats auth user:pass
```
##Chapter 4. Improving Database Performance
###The MySQL database
####Query caching
```
SHOW VARIABLES LIKE 'have_query_cache';
```
ebable query caching, in my.cnf
```
query_cache_type = 1
query_cache_size = 128MB
query_cache_limit = 1MB
```
If query_cache_size is greater than 0, then query cache is enabled, memory is allocated.  
Queries that do not exceed the query_cache_limit value or use the SQL_NO_CACHE option are cached.  
###Storage engines
```
ALTER TABLE tbname ENGINE=INNODB;
```
####The MyISAM storage engine
summary:
- MyISAM does not have support for foreign keys.
- MyISAM supports full-text search.
- MyISAM does not support transactions.
- Data compression, replication, query caching, and data encryption is supported.
- The cluster database is not supported.



####The InnoDB storage engine
summary
- InnoDB is designed for high reliability and high performance when processing a high volume of data.
- InnoDB supports foreign keys and forcing foreign keys constraints.
- Transactions are supported.
- Data compression, replication, query caching, and data encryption is supported.
- InnoDB can be used in a cluster environment, but it does not have full support. However, InnoDB tables can be converted to the NDB storage engine, which is used in the MySQL cluster by changing the table engine to NDB.

#####innodb_buffer_pool_size
For a dedicated MySQL server, the recommended value is 50-80% of the installed memory on the server.
#####innodb_buffer_pool_instances
Tto use one instance per GB of innodb_buffer_pool_size.  
If the value of innodb_bufer_pool_size is 16 GB, we will set innodb_buffer_pool_instances to 16.  

