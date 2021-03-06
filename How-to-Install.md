# 爱百应 - 百度云搜索引擎，安装部署教程

## 运行环境

开始之前你需要安装

* PHP 5.3.7 +
* MySQL
* Python 2.7 ~
* [xunsearch](http://xunsearch.com/) 搜索引擎



## 获取源码

```
git clone git@github.com:k1995/BaiduyunSpider.git
```

或手动下载

```
https://github.com/k1995/BaiduyunSpider/archive/master.zip
```



下载完毕后，___项目的目录结构___大致是这样的

```
--- indexer/  #索引
--- spider/   #爬虫
--- sql/      
--- web/	  #网站
	--- application/
		--- config/ # 配置相关
				--- config.php
				--- database.php # 数据库配置
				...
		...
	--- static/ # 存放静态资源，css|js|font
	--- system/
	--- index.php
	...
```





## 开始部署

### 创建数据库

创建名为`pan`的数据库，编码设为`utf-8`。然后导入`sql`，完成表的创建。



### 网站部署

支持`nginx`，`apache` 服务器。

__apache__ 需要开启 *mod_rewrite* 。

__nginx__  配置如下

```
location /
{   
	index index.php;
	try_files $uri $uri/ /index.php/$uri;
}

location ~ [^/]\.php(/|$)
{
	fastcgi_pass  127.0.0.1:9000;
	fastcgi_index index.php;
	include fastcgi.conf;
	include pathinfo.conf;
}
```



####  配置文件修改

`config.php` 文件修改网站标题，描述等信息

`database.php` 修改数据库账号，密码等信息

> 网站是基于CodeIgniter 框架开发的，如安装，部署，或二次开发有问题，请参考[官网文档]( http://codeigniter.org.cn/user_guide/general/welcome.html)



### 启动爬虫

进入 `spider/`目录，修改`spider.py` 中数据库信息。

__如果你是第一次部署，需运行下面命令，完成做种__

```
python spider.py --seed-user
```

上面其实就是抓取百度云热门分享用户的相关信息，然后从他们开始入手爬取数据

然后运行

```
python spider.py
```

此时爬虫已经开始工作了.根据热门用户的分享开始爬，获取热门用户的粉丝。再爬粉丝的文件，一层一层的爬取



### 安装xunsearch

目前使用__xunsearch__作为搜索引擎，后面会更换为`elasticsearch`。

安装过程请参考（不需要安装，PHP SDK，我已经整合到web里了）

http://xunsearch.com/doc/php/guide/start.installation

Xunsearch 是免费开源的专业全文检索解决方案，旨在帮助一般开发者针对既有的海量数据，快速而方便地建立自己的全文搜索引擎。全文检索可以帮助您降低服务器搜索负荷、极大程度的提高​​​搜索速度和用户体验。

高性能：后端是采用 C/C++ 开发多线程服务端，索引设计基于 Xapian 和 scws 中文分词。单库最多支持 40 亿条数据，在 5 亿网页大约 1.5TB 的数据中检索时间不超过 1 秒(非缓存)。

简单易用：前端是使用脚本语言编写的开发工具 (SDK)，目前仅支持 PHP 语言。API 简单清晰，开发难度极低，提供全中文的示例代码、文档、辅助脚本工具等。

全功能：除支持基础的自定义分词、字段检索、布尔搜索外，还直接支持用户急需的相关搜索、拼音搜索、搜索建议等专业功能。
cd $prefix ; bin/xs-ctl.sh restart


### 索引数据
上面我们完成了爬虫的数据抓取，网站的搭建，但还不能搜索，下面开始最后一步，索引的建立。

进入 `indexer/`目录，在`indexer.php`中将$prefix，替换为你web的根路径
```
require '$prefix/application/helpers/xs/lib/XS.php';
```
并修改数据库账号密码

然后运行
```
python ./index.php 
```



到此为止程序已全部安装完毕，若有疑问请在 [github 中文社区](http://www.githubs.cn/topic/118) 发帖