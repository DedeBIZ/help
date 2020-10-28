# DedeCMSV6环境搭建
DedeCMSV6系统基于PHP+MySQL开发，同时数据库支持SQLite3用于本地开发调试。本篇主要从开发、生产两个部分来介绍如何筹备DedeCMS系统需要的运营环境。

## 推荐运行环境
1.操作系统：Linux、Windows、OpenBSD

2.PHP：PHP7.2+

3.MySQL：MySQL5+

5.Web服务器：Nginx、Apache、IIS

## 开发环境
构建一个最小的开发环境，仅仅为了体验或者制作模板，DedeCMSV6会非常简单。从PHP5.4开始，PHP本身就包含了一个轻量级的开发Web Server，无需依赖任何第三方Web组件。

### 下载PHP
根据操作系统选择对应平台的PHP环境，由于我们开发环境采用Windows系统，可以通过[PHP的Windows站点](https://windows.php.net/download/)下载到相应的程序包。

### 安装PHP环境
下载对应的版本，解下压缩文件，我们可以将解压的压缩文件目录，例如`D:\apps\php`添加到系统环境PATH变量中去。

复制`php.ini-development`文件名为`php.ini`，修改这个`php.ini`文件。

我们需要保证开启几个DedeCMSV6系统需要的基本组件。
```
extension_dir = "ext" ;由于是Windows环境，设定下载扩展目录
extension=curl    ;开启后主要用于HTTP通信，比如获取远程服务器数据信息、接口调用
extension=gd2     ;验证码
extension=mysqli  ;数据库存储
extension=openssl ;用于系统安全校验、授权
extension=sockets ;用于和DedeB商业组件通信
extension=sqlite3 ;开发环境下的SQLite3数据库
```
好了，一个轻量级的PHP环境搭建完毕了，如果是Linux下面例如Ubuntu系统，通过`sudo apt install php php-cli openssl php-gd php-mysql php-sqlite3 php-sockets`。

### 运行PHP
在DedeCMSV6系统src目录下采用PHP内置的Web Server运行，执行命令`php -S localhost:8088 -t .`，访问地址`http://localhost:8088`就可以访问到DedeCMSV6安装界面。

## 生产环境
生产环境建议采用Nginx+PHP-FPM的方式运行DedeCMSV6，这里我们以在[Ubuntu 20.04 LTS](http://releases.ubuntu.com/focal/)的Linux环境下为例。

### 安装Nginx
执行命令`sudo apt install nginx`，系统将会自动安装Nginx服务器。通过执行`systemctl status nginx`可以查看Nginx服务运行状态。

如果你的服务器环境启用了防火墙，可以执行`sudo ufw allow proto tcp from any to any port 80,443`来允许80、443端口访问站点。

### 安装PHP
执行命令`sudo apt install php php-cli php-fpm php-json php-mysql php-zip php-gd php-curl php-sqlite3 php-sockets`来安装PHP及相关组件。

系统默认会安装PHP7.4版本，运行`systemctl status php7.4-fpm.service`查看PHP服务运行情况。

### 安装MySQL
执行命令`sudo apt install mysql-server`安装MySQL服务，同样，通过`systemctl status mysql`也能够查看服务运行情况。

执行命令`sudo mysql_secure_installation`根据提示初始化数据库，如果希望了解更为详细的步骤，可以[尝试通过搜索引擎了解](https://www.baidu.com/s?wd=ubuntu%2020.04%20%E5%AE%89%E8%A3%85mysql)。

### 配置站点
我们先创建一个PHP环境的Web站点，可以在系统目录`/var/www`下创建一个名为`DedeCMSV6`的文件夹。

修改`/etc/nginx/sites-available/default`文件。

配置如下：
```
server {
	listen 80;
	server_name www.mydomain.com; # 这里是你自己的站点域名
	index index.html index.php;
	charset utf-8;
	root /var/www/DedeCMSV6/src;
	location ~ \.php$ {
		include snippets/fastcgi-php.conf;
		fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
	}
}
```
至此我们完成了一个DedeCMSV6的运行环境的搭建。