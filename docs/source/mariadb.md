# MariaDB 操作篇

MariaDB 是 MySQL 的分支版本，各大 Linux 发行版镜像源都用 MariaDB 替代 MySQL 了

## my.ini

my.ini 是 MariaDB 默认配置文件名，可以有多种路径选择，具体如下：

### my.ini 文件路径

MariaDB 的根配置文件，只能读取 1 个，如果有多个，可以做为子配置文件，从根配置文件里加载

-   c:\my.ini

    ```text
    - Win下 MariaDB 全局配置文件
    - 所有的 mysqld.exe 被启动时都可以读取
    ```

-   c:\wamp\mariadb\my.ini

    ```text
    - 对应的 mysqld.exe 服务被启动时可读取
    - 这个是我们预先写好的配置文件，在安装MariaDB时被引入
    ```

-   c:\wamp\web\data\my.ini

    ```text
    - 对应的 mysqld.exe 服务被启动时可读取
    - MariaDB 安装指令，默认会生成此配置文件
    ```

### my.ini 优先级

如果 win 系统上有多个 MariaDB 配置文件，MariaDB 会按优先级依次读取，直到读取到 1 个配置文件后，就会结束继续读取

如果 win 系统上没有 MariaDB 配置文件，那么 MariaDB 将无法启动

my.ini 优先级具体如下：

1. --defaults-file="path"

    ```text
    - 由 --defaults-file 指定的配置文件
    - 优先级：1，具体最高优先权
    - 如果安装时没有指定该参数，或者该文件不存在，将继续查看下1个
    ```

2. c:\my.ini

    ```text
    - 系统盘根目录下的配置文件
    - 优先级： 2
    - 如果该文件不存在，将继续查看下1个
    ```

3. c:\wamp\mariadb\my.ini

    ```text
    - MariaDB 根目录下的配置文件
    - 优先级：3
    - 如果该文件不存在，将继续查看下1个
    ```

4. c:\wamp\web\data\my.ini

    ```text
    - MariaDB 数据目录下的配置文件
    - 优先级：4
    - 如果该文件不存在，系统将报错
    ```
