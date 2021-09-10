# httpd 操作篇

httpd 是当前最流行的 web 服务器软件之一，当下全球排行第二，已经被 nginx 超过

## httpd 配置文件

httpd 配置文件分为 `根配置文件` 和 `自定义配置文件`

> 根配置文件

| 属性     | 说明                               |
| -------- | ---------------------------------- |
| 路径     | C:/wamp/base/httpd/conf/httpd.conf |
| 数量     | 有且仅有 1 个                      |
| 生成方式 | 由官方源码编译成二进制包时指定     |

> 自定义配置文件

| 属性 | 说明                         |
| ---- | ---------------------------- |
| 路径 | 由开发者自行指定路径         |
| 数量 | 不限，如：站点虚拟机配置文件 |
| 依赖 | 依赖 mod_include.so 扩展     |

httpd 配置文件常用知识点都在这里了，下面开始讲解

## 设置变量

httpd 配置文件中经常使用路径或端口，我们可以为它们设置变量：

| 变量属性 | 内容                   |
| -------- | ---------------------- |
| 格式     | `Define 变量名 变量值` |
| 使用     | `${变量名}`            |

-   wamp 包中，httpd 变量列表及其作用说明：

    | 变量名称 | wamp 根目录                |
    | -------- | -------------------------- |
    | 定义变量 | Define WAMP_ROOT "c:/wamp" |
    | 输出变量 | ${WAMP_ROOT}               |

    | 变量名称 | httpd 根目录                             |
    | -------- | ---------------------------------------- |
    | 定义变量 | Define SRVROOT "${WAMP_ROOT}/base/httpd" |
    | 输出变量 | ${SRVROOT}                               |
    | 默认值   | Define SRVROOT "c:/Apache24"             |

    | 变量名称 | 站点根目录                           |
    | -------- | ------------------------------------ |
    | 定义变量 | Define HTDOCS "${WAMP_ROOT}/web/www" |
    | 输出变量 | ${HTDOCS}                            |

    | 变量名称 | 站点日志根目录                              |
    | -------- | ------------------------------------------- |
    | 定义变量 | Define HTLOGS "${WAMP_ROOT}/web/logs/httpd" |
    | 输出变量 | ${HTLOGS}                                   |

    | 变量名称 | http 协议端口         |
    | -------- | --------------------- |
    | 定义变量 | Define HTTP_PORT "80" |
    | 输出变量 | ${HTTP_PORT}          |

    | 变量名称 | https 协议端口          |
    | -------- | ----------------------- |
    | 定义变量 | Define HTTPS_PORT "443" |
    | 输出变量 | ${HTTPS_PORT}           |

提示：尽量少定义变量，这会增加 httpd 的负荷！

## 增加自定义配置文件

| 概述                                                  |
| ----------------------------------------------------- |
| 在父级配置文件中，通过 Include 语句加载自定义配置文件 |
| Include 语句依赖于 mod_include.so 扩展                |
| Include 的值支持相对路径和绝对路径                    |

-   根配置文件中，载入子配置文件：

    ```conf
    <IfModule include_module>
        Include "${WAMP_ROOT}/base/conf/httpd.conf"
    </IfModule>
    ```

-   子配置文件中，载入 ssl 配置文件以及虚拟主机配置文件

    ```conf
    <IfModule include_module>
        Include "${WAMP_ROOT}/base/conf/httpd-ssl.conf"
        Include "${WAMP_ROOT}/web/sites/*.conf"
    </IfModule>
    ```

## 清理多余配置项目

httpd 自带的根配置文件有很多注释说明以及多余配置，我们可以将其移除，具体如下：

-   移除所有注释内容
-   除了载入 mod_include.so 模块外，其余配置都可以在自定义配置文件里配置

| 项目配置文件列表                          |
| ----------------------------------------- |
| [根配置文件](./source/httpd-root.md)      |
| [子配置文件](./source/httpd-sub.md)       |
| [ssl 配置文件](./source/httpd-ssl.md)     |
| [虚拟主机配置文件模板](./source/sites.md) |

下面我们开始讲解 httpd 配置文件中我们可能需要改变的内容

## 加载必要模块

我们搭建的 wamp 环境额外增加如下插件，这些插件默认不开启，需自己启用，加载模块具体格式：

```conf
LoadModule 模块标识符 模块路径
```

1. 虚拟主机模块

    ```conf
    LoadModule vhost_alias_module modules/mod_vhost_alias.so
    ```

2. 伪静态模块

    ```conf
    LoadModule rewrite_module modules/mod_rewrite.so
    ```

3. 文件缓存模块

    ```conf
    LoadModule headers_module modules/mod_headers.so
    ```

4. 压缩传输模块

    传输速度加快，服务器负荷增加，配合文件缓存模块效果更佳

    ```conf
    LoadModule deflate_module modules/mod_deflate.so
    ```

5. 过滤器模块

    可以实现，只允许部分格式的文件，进行压缩传输

    ```conf
    LoadModule filter_module modules/mod_filter.so
    ```

6. ssl 模块

    ```conf
    LoadModule ssl_module modules/mod_ssl.so
    ```

7. 共享对象缓存

    主要是根 ssl 模块结合使用，提高 https 访问效率

    ```conf
    LoadModule socache_shmcb_module modules/mod_socache_shmcb.so
    ```

## 加载 php 处理模块

在 wamp 里 php 通常以 httpd 模块的形式被加载，具体加载步骤如下：

1. 加载 httpd 自带的 php8 模块

    建议以绝对路径的方式加载 dll 形式的模块

    ```conf
    LoadModule php_module ${WAMP_ROOT}/base/php/php8apache2_4.dll
    ```

2. 获取 php 模块必备配置信息

    ```conf
    <IfModule php_module>
        PHPINIDir "${WAMP_ROOT}/base/php"
        # 一下loadFIle 语句是为了解决 php 的 curl 扩展问题
        LoadFile  "${WAMP_ROOT}/base/php/libssh2.dll"
        LoadFile  "${WAMP_ROOT}/base/php/libcrypto-1_1-x64.dll"
        LoadFile  "${WAMP_ROOT}/base/php/libssl-1_1-x64.dll"
    </IfModule>
    ```

## httpd 缺省设置

这些值会为稍后在文件中定义的任何虚拟主机容器提供缺省值。

1. 设置全局邮箱地址

    多站点模式下，没有指定邮箱地址的虚拟主机都会指向该邮箱

    ```conf
    ServerAdmin qy@y746.com
    ```

2. 设置全局主机名

    多站点模式下，没有指定主机名的虚拟主机都会指向该主机名

    ```conf
    ServerName localhost
    ```

    > 提示：这会造成一个问题，就是访问 localhost 的时候，可能是某个虚拟主机的路径

3. 设置全局目录

    多站点模式下，没有指定 DocumentRoot 的虚拟主机都会指向该路径

    ```conf
    DocumentRoot "${WAMP_ROOT}/base/default"
    ```

4. 设置访问权限

    整个服务器文件系统，拒绝用户访问

    ```conf
    <Directory />
        Options None
        AllowOverride None
        Require all denied
    </Directory>
    ```

    httpd 全局目录访问权限，允许用户访问文件

    ```conf
    <Directory "${WAMP_ROOT}/base/default">
        Options FollowSymLinks
        AllowOverride None
        Require all granted
    </Directory>
    ```

    虚拟主机根目录访问权限，允许用户访问文件以及索引

    ```conf
    <Directory "${HTDOCS}">
        Options FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>
    ```

5. 设置监听端口

    新版本在没有设置监听端口时，httpd 将无法启动

    可以通过多个 Listn 语句，来添加多个监听端口

    ```conf
    Listen ${HTTP_PORT}
    Listen ${HTTPS_PORT}
    ```

## 单站点模式

如果服务器只有一个站点的话，不需要加载虚拟主机模块， 使用全局目录作为站点根目录

## 多站点模式

多站点需要加载虚拟主机模块，来创建多个虚拟主机

1. 设置虚拟主机缺省站点

    如果未通过 `_default_` 设置缺省站点，第 1 个虚拟站点会默认升级为缺省站点，具体定义如下：

    ```conf
    # http 协议的缺省站点
    <VirtualHost _default_:${HTTP_PORT}>
        DocumentRoot "${WAMP_ROOT}/base/default"
    </VirtualHost>
    # https 协议的缺省站点
    <VirtualHost _default_:${HTTPS_PORT}>
        DocumentRoot "${WAMP_ROOT}/base/default"
    </VirtualHost>
    ```

    缺省站点作用：解析到服务器的域名，如果没有配置虚拟主机，会自动跳转到虚拟主机的缺省站点

## 常用指令说明

日程开发掌握这些指令就能完成 95%的工作

### Options

Options 指令控制特定目录中可用的服务器功能，具体属性值如下：

| 属性值                 | 描述                           |
| ---------------------- | ------------------------------ |
| None                   | 不会启用任何其他功能           |
| Indexes                | 允许索引目录列表               |
| FollowSymLinks         | 允许打开文件，这是默认设置     |
| ~All~                  | ~除 MultiViews 以外的所有选项~ |
| ~ExecCGI~              | ~允许执行 CGI 脚本~            |
| ~Includes~             | ~允许服务器端包含~             |
| ~IncludesNOEXEC~       | ~允许部分服务器端包含~         |
| ~MultiViews~           | ~允许多视图~                   |
| ~SymLinksIfOwnerMatch~ | ~仅允许打开文件~               |

开发环境案例：

```conf
<Directory "${HTDOCS}">
    Options FollowSymLinks Indexes
</Directory>
```

部署环境案例：

```conf
<Directory "${HTDOCS}">
    Options FollowSymLinks
</Directory>
```

### AllowOverride

控制站点是否支持 `.htaccess` 文件，主要属性值如下：

| 属性值     | 描述                                 |
| ---------- | ------------------------------------ |
| None       | 忽略 `.htaccess` 文件伪指令          |
| All        | `.htaccess` 允许使用任何支持的伪指令 |
| AuthConfig | `.htaccess` 允许使用授权指令         |
| FileInfo   | `.htaccess` 允许使用控制文档类型指令 |
| Indexes    | `.htaccess` 允许使用控制索引的指令   |
| Limit      | `.htaccess` 允许使用控制访问的指令   |

开发环境案例：

```conf
<Directory "${HTDOCS}">
    AllowOverride All
</Directory>
```

部署环境案例：

```conf
<Directory "${HTDOCS}">
    AllowOverride None
</Directory>
```

### Require

Require 含容器和指令两块内容，具体内容我们放到授权里详细讲解

## 授权

一个授权工作可以由多个授权容器和一系列授权指令组合完成，最终得出是 true 还是 false

### 授权容器

mod_authz_core 模块包含 3 种授权容器，授权容器返回 3 种状态，具体如下：

> 授权容器状态值:

| 状态值 | 说明         |
| ------ | ------------ |
| true   | 授权验证成功 |
| false  | 授权验证失败 |
| none   | 授权验证无效 |

1. `<RequireAll>` 容器

    返回 true ，需要同时满足以下 2 个条件：

    ```text
    1. 该授权容器里的指令，不能出现验证不通过的
    2. 该授权容器里的指令，至少有1个指令验证通过
    ```

    返回 none，需要满足以下 2 个条件：

    ```text
    1. 该授权容器里的指令，既没有验证不通过的
    2. 该授权容器里的指令，也没有验证通过的
    ```

    其它情况下，始终返回 false

2. `<RequireAny>` 容器

    返回 true ，需要满足以下条件：

    ```text
    - 该授权容器里的指令，只要有1个指令验证通过
    ```

    返回 none，需要满足以下 2 个条件：

    ```text
    1. 该授权容器里的指令，既没有验证不通过的
    2. 该授权容器里的指令，也没有验证通过的
    ```

    其它情况下，始终返回 false

3. `<RequireNone>` 容器

    返回 true ，需要满足的条件：

    ```text
    - 该授权容器永远无法返回 true
    ```

    返回 none，需要满足以下条件：

    ```text
    - 该授权容器里的指令，不能出现验证通过的
    ```

    其它情况下，始终返回 false

### 授权语句

## 默认读取文件

`mod_dir.so` 模块允许通过 `DirectoryIndex` 参数设置默认文件，具体设置如下：

```conf
<IfModule dir_module>
DirectoryIndex index.html index.htm index.php
</IfModule>
```

> 提示：配置多个默认文件，会从左往右索引文件，直到找到为止。无法找到页面将无法显示或输出文件列表

## 阻止客户端查看特殊文件

httpd 可以让某些固定格式的文件不被浏览者访问，如：会禁用 `.htaccess` 和 `.htpasswd` 文件被 Web 客户端查看

```conf
<Files ".ht*">
Require all denied
</Files>
```

## 其他设置

1. 这 3 组都是默认已经配置的，如果没有特别需要，可以直接移除掉了：

    ```conf
    <IfModule alias_module>
        ScriptAlias /cgi-bin/ "${SRVROOT}/cgi-bin/"
    </IfModule>

    <Directory "${SRVROOT}/cgi-bin">
        AllowOverride None
        Options None
        Require all granted
    </Directory>

    <IfModule headers_module>
        RequestHeader unset Proxy early
    </IfModule>
    ```

2. 关联 php 文件的扩展名

    > 操作：在 `<IfModule mime_module>` 内新增一行 php 相关代码：

    ```conf
    <IfModule mime_module>
        TypesConfig conf/mime.types

        AddType application/x-compress .Z
        AddType application/x-gzip .gz .tgz
        AddType application/x-httpd-php .php
    </IfModule>
    ```

    > 格式： `AddType application/x-httpd-php .扩展名1 [.扩展名2 ...]`

3. 这 2 组不需要特别修改

    ```conf
    <IfModule proxy_html_module>
        Include conf/extra/proxy-html.conf
    </IfModule>

    <IfModule ssl_module>
        SSLRandomSeed startup builtin
        SSLRandomSeed connect builtin
    </IfModule>
    ```

4. 指定站点配置文件存放目录

    站点配置文件就是 httpd 的自定义配置文件，只需通过 `Include` 参数加载即可：

    ```conf
    <IfModule include_module>
        Include "${WAMP_ROOT}/web/sites/*.conf"
    </IfModule>
    ```

    > 提示：Include 支持简单的正则表达式，sites 目录下所有 `.conf` 文件都会被加载！
