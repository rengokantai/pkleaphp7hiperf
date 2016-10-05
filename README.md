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
