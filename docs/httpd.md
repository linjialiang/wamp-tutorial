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

## 配置文件操作

httpd 配置文件常用知识点都在这里了！

### 设置变量

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

### 增加自定义配置文件

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

### 清理多余配置项目

httpd 自带的根配置文件有很多注释说明以及多余配置，我们可以将其移除，具体如下：

-   移除所有注释内容
-   除了载入 mod_include.so 模块外，其余配置都可以在自定义配置文件里配置

| 项目配置文件列表                          |
| ----------------------------------------- |
| [根配置文件](./source/httpd-root.md)      |
| [子配置文件](./source/httpd-sub.md)       |
| [ssl 配置文件](./source/httpd-ssl.md)     |
| [虚拟主机配置文件模板](./source/sites.md) |

## 修改配置文件

这里讲解是配置文件中我们可能需要改变的内容。

### 加载必要模块

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

### 加载 php 处理模块

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

### httpd 缺省设置

这些值会为稍后在文件中定义的任何虚拟主机容器提供缺省值。

1. 设置全局邮箱地址

    ```conf
    ServerAdmin qy@y746.com
    ```

2. 设置全局主机名

    ```conf
    ServerName localhost
    ```

3. 设置全局目录

    多站点模式下，没有指定 DocumentRoot 的虚拟主机都会指向该路径

    ```conf
    DocumentRoot "${WAMP_ROOT}/base/default"
    ```

4. 设置访问权限

    整个服务器文件系统，拒绝用户访问

    ```conf
    <Directory />
      AllowOverride none
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

### 单站点模式

如果服务器只有一个站点的话，不需要加载虚拟主机模块， 使用全局目录作为站点根目录

### 多站点模式

多站点需要加载虚拟主机模块，来创建多个虚拟主机

1. 虚拟主机缺省站点

    httpd 的 `mod_vhost_alias.so` 模块规则定义，第一条满足条件的虚拟站点为缺省站点，具体定义如下：

    ```conf
    <VirtualHost _default_:${HTTP_PORT}>
        DocumentRoot "${WAMP_ROOT}/base/default"
    </VirtualHost>
    ```

    > 提示：任何解析到当前服务器的域名，只要没有配置虚拟主机，都会指定到改缺省虚拟主机下；

    | 缺省值       | 描述                                   |
    | ------------ | -------------------------------------- |
    | 缺省虚拟主机 | 未定义虚拟主机的域名指向的虚拟主机配置 |
    | 站点缺省路径 | 未定义路径的虚拟主机指向的站点根目录   |

2. 站点访问权限

    我们可以为每个站点的各种目录设置访问权限，站点总目录权限设置如下：

    ```conf
    <Directory "${HTDOCS}">
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>
    ```

    > 提示：为了便于管理，所有站点我们都会放在一个路径下面，这个路径就是 `站点总目录` 。

3. `Options` 部分属性值

    | Options 属性值   | 描述               | 服务器建议 |
    | ---------------- | ------------------ | ---------- |
    | `Indexes`        | 允许展示目录式列表 | 关闭       |
    | `FollowSymLinks` | 允许访问 url 链接  | 开启       |

4. `AllowOverride` 部分属性值

    | AllowOverride 属性值 | 描述                      | 考虑与建议           |
    | -------------------- | ------------------------- | -------------------- |
    | `None`               | 完全忽略 `.htaccess` 文件 | 更安全，性能消耗降低 |
    | `All`                | 加载 `.htaccess` 文件     | 更方便，性能消耗增加 |

### 默认读取文件

`mod_dir.so` 模块允许通过 `DirectoryIndex` 参数设置默认文件，具体设置如下：

```conf
<IfModule dir_module>
DirectoryIndex index.html index.htm index.php
</IfModule>
```

> 提示：配置多个默认文件，会从左往右索引文件，直到找到为止。无法找到页面将无法显示或输出文件列表

### 阻止客户端查看特殊文件

httpd 可以让某些固定格式的文件不被浏览者访问，如：会禁用 `.htaccess` 和 `.htpasswd` 文件被 Web 客户端查看

```conf
<Files ".ht*">
Require all denied
</Files>
```

### 其他设置

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
