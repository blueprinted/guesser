# guesser
an html5 finger-guessing game

    安装yaf
    在GitHub找yaf项目下载最新版 yaf项目("yaf主分支源码")
    在PECL官网找了最新版yaf-3.0.7.tgz，如果不会，请参看官网的安装[yaf官网](http://www.laruence.com/manual/ "yaf官网")
```shell
##phpize 与 php-config需要根据自己的机器来确定路径
##make完后可以执行 make test 用于测试是否存在bug导致不能改正常使用，但基本上很少会执行 make test 这里我也省略了 make test
##加上 sudo 避免因为权限不够导致安装失败
cd yaf-master
/usr/bin/phpize
./configure --with-php-config=/usr/bin/php-config
make
sudo make install
```
    在php.ini里面增加yaf模块的配置
```ini
extension=yaf.so

;yaf config
yaf.environ = product
;yaf.library =
yaf.cache_config = 0
yaf.name_suffix = 1
;yaf.name_separator =
yaf.forward_limit = 5
yaf.use_namespace = 1
yaf.use_spl_autoload = 0
```

    利用yaf源码里面提供的 [使用代码生成工具](http://www.laruence.com/manual/tutorial.last.html "使用代码生成工具") 这样就免去了手写yaf框架基础文件的麻烦

>yaf官网例子  
cd yaf-master/tools/cg  
php yaf_cg Sample  
##will generator folder "Sample" under output:  
$ ls output/Sample/  
application/  conf/  index.php  readme.txt  
php yaf_cg Sample '' n  
##will generate a namespace example  

```shell
cd tools/cg
php yaf_cg guesser '' n
DONE
##执行完会输出 DONE
ll output/guesser/
total 16
drwxr-xr-x  8 www  staff  256  5  8 01:49 application
drwxr-xr-x  3 www  staff   96  5  8 01:49 conf
-rw-r--r--  1 www  staff  174  5  8 01:49 index.php
-rw-r--r--  1 www  staff  446  5  8 01:49 readme.txt
```
    对于nginx服务器，由于yaf框架的直接放在web站的根目录 www/ ，yaf官网是这么配置的：
```nginx
server {
  listen ****;
  server_name  domain.com;
  root   document_root;
  index  index.php index.html index.htm;

  if (!-e $request_filename) {
    rewrite ^/(.*)  /index.php/$1 last;
  }
}
```
    与yaf官网情况不同，这个项目是放在web站根目录 www/ 的下一级目录 www/guesser/ ，因此，照抄配置是不行的（经过验证确实不行），经过尝试并验证，以下两种配置都可以生效
```nginx
server {
#...
    location /guesser {
        root html;
        index index.php;
        try_files $uri $uri/ /guesser/index.php;
    }
#...
}
```
```nginx
server {
#...
    location /guesser {
        root html;
        index index.php;
        if (!-e $request_filename) {
            rewrite ^/(.*)  /guesser/index.php last;
        }
    }
#...
}
```
    参照这个例子，稍加修改配置，可以将框架放置在相对于web根目录的任意深度的目录。

    浏览器访问 http://127.0.0.1/guesser/ 面输出
```html
Hello World! I am Stranger
```
    浏览器访问 http://127.0.0.1/guesser/index/index/index/name/phper 页面输出
```html
Hello World! I am phper
```
