迄今为止，WAMP 依然是 PHP 开发环境的首选，在 Windows 下简单而且稳定。

本教程全面介绍如何手工搭建 WAMP 开发环境。

内容上从最简单的讲起，循序渐进、由浅入深，力求清晰易懂。

本教程适合初学者熟悉 WAMP 结构的入门教程，学完后就可以自己搭建建议的 WAMP 开发环境，也适合当作日常搭建 WAMP 开发环境的参考手册。

## 最新包下载地址

| 百度网盘地址                                                      | 密码 |
| ----------------------------------------------------------------- | ---- |
| [wamp-v6.6.4.7z](https://pan.baidu.com/s/1Tk0Aa4BVdjc5YkP-FEDTeA) | r1yd |

## 包列表

| 包名                                               | 描述               |
| -------------------------------------------------- | ------------------ |
| [httpd](https://www.apachelounge.com/download/)    | web 服务器         |
| [php](https://windows.php.net/download)            | php 解释器         |
| [MariaDB](https://downloads.mariadb.org/)          | 数据库系统         |
| [phpMyAdmin](https://www.phpmyadmin.net/files/)    | MySQL 操作工具     |
| [adminer](https://www.adminer.org/#download)       | 数据库系统操作工具 |
| [vcredist](https://github.com/abbodi1406/vcredist) | vc 运行库合集      |

## 目录结构

```text
====================================================
WAMP 开发环境目录
====================================================
├─ base                    wamp核心目录
|   ├─ httpd               Web服务器
|   |  ├─ bin              阿帕奇可执行程序目录
|   |  ├─ conf             配置
|   |  |  ├─ httpd.conf    阿帕奇主配置文件
|   |  |  └─ ...
|   |  └─ ...
|   |
|   ├─ MariaDB             MariaDB 版本
|   |  ├─ bin              MariaDB 可执行程序目录
|   |  ├─ my.ini           MariaDB 配置文件
|   |  └─ ...
|   |
|   ├─ php                 php版本
|   |  ├─ ext              模块目录
|   |  ├─ php.ini          php主配置文件
|   |  └─ ...
|   |
|   ├─ conf                公用配置文件目录
|   |   ├─ key             自签署证书目录
|   |   |  ├─ server.key   服务器端私钥文件
|   |   |  ├─ server.crt   服务器端CA自签安全证书
|   |   |  └─ ...
|   |   ├─ cacert.pem      php的openssl扩展的ca证书
|   |   ├─ httpd.conf      httpd的公用配置文件
|   |   ├─ httpd-ssl.conf  https协议的公用配置文件
|   |   ├─ openssl.cnf     httpd的openssl公用配置文件
|   |
|   ├─ default             缺省站点路径
|   |   ├─ pma             MySQL 管理工具
|   |   ├─ adminer.php     数据库管理工具
|   |   ├─ phpinfo.php     PHP 配置的信息
|   |   ├─ index.php       缺省站点提示页面
|   |
├─ web                     wamp配置目录
|   ├─ sites               虚拟主机配置文件目录
|   |
|   ├─ www                 站点根目录
|   |
|   ├─ data                MariaDB的数据目录
|   |
|   ├─ logs                服务器相关日志文件目录
|   |  ├─ httpd            httpd日志目录
|   |  ├─ mariadb          MariaDB日志目录
|   |
|
├─ install.bat             安装wamp相关服务到系统服务
|
├─ uninstall.bat           卸载wamp相关系统服务
|
├─ menu.bat                日常用到的操作菜单
|
└─ force-delete.bat        net指令删除wamp相关系统服务
```

## 章节导航

| 章节                           |
| ------------------------------ |
| [httpd 操作篇](./httpd.md)     |
| [php 操作篇](./php.md)         |
| [maraidb 操作篇](./maraidb.md) |
| [wamp 管理篇](./wamp.md)       |
