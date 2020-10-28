# DedeCMSV6下载安装
文档将介绍如何下载DedeCMSV6系统，并且通过之前已经[部署好的系统环境](quick_env.md)来安装配置一个安全的站点。

## 下载、解压
通过[DedeCMSV6系统下载页面](/download)可以获取到最新的程序安装包。当然也可以通过Git的方式检出[DedeCMSV6系统源代码](/git)。

如果下载的是程序包，默认打包的格式是`zip`，可以通过使用Windows默认解压程序或者Linux中的`unzip`命令来解压代码包。

程序安装包的目录结构如下：
```
./docs/       文档资料
./src/        系统源代码
./README.MD   说明
./dedecms     DedeCMSV6开发环境运行脚本
```
将代码解压到`/var/www/DedeCMSV6`中，这样站点的根目录就是`/var/www/DedeCMSV6/src`。

如果通过Git的方式，则可以切换到`/var/www/DedeCMSV6`目录中，执行`git clone https://www.zhelixie.com/DedeBiz/DedeCMSV6.git`，则同样会将代码检出到目录中。

## 安装DedeCMSV6
我们可以访问我们的站点，这里假设我们已经将自己的域名`www.mydomain.com`解析到了我们的服务器，通过浏览器访问`http://www.mydomain.com`打开站点目录，如果第一次运行，则系统会自动跳转到`/install`目录进行安装。

根据安装界面提示，按步骤完成系统基本的设定。

这里除了仔细阅读[许可协议](/license)之外，需要注意后台管理员账号、密码尽量设置复杂一些。

## 配置站点
至此我们已经完成了DedeCMSV6系统的安装，接下来我们将结合之前的[Linux系统](quick_env.md#heading-2)来配置一个安全的站点。我们先来了解一下DedeCMSV6系统源代码中的目录结构。
```
./src
  |__a              用于生成静态文件的目录【可写+可访问】
  |__data           一些缓存、配置数据存放目录【可写+部分可访问】
  |__dede           管理后台目录，一般建议上线后更名或者对目录访问权限做限制【不可写+可访问】
  |__include        核心文件存放目录【不可写+不可访问】
  |__install        系统安装目录，建议安装后屏蔽这个目录对外访问权限【不可写+不可访问】
  |__member         会员中心目录【不可写+不可访问】
  |__plus           动态扩展目录【不可写+不可访问】
  |__static         静态文件（JS、CSS、图片）存放目录【不可写+不可访问】
  |__templets       模板文件【不可写+不可访问】
  |__uploads        上传文件存放目录【可写+可访问】
```
目录结构后面的`【】`中，我们提供了目录的权限建议。在这里需要特殊说明下`data`目录，其中可访问的目录为`enums`、`js`、`rss`、`vote`，其他目录及文件建议屏蔽其访问能力。

一般在Nginx中禁止目录访问，可以通过下面配置设定：
```
location ~ /(dede|include|templets|member) {
    deny all;
    return 404;
}
```
读写权限，可以通过Linux权限进行设定。
