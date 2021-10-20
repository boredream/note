

# Nginx

## 目录结构

```sh
# 配置文件
/etc/nginx/nginx.conf

# 日志
/var/log/nginx

# 项目
/usr/share/nginx/html

```



## 配置

```sh
# nginx.conf
user  nginx;
worker_processes  auto;

error_log  /var/log/nginx/error.log notice;
pid        /var/run/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;

    include /etc/nginx/conf.d/*.conf;
    
    # 监听来自80端口，host为 api.papikoala.cn 的接口
    server {
        listen          80;
        server_name     api.papikoala.cn;

				# 代理转发 / 到 proxy_pass
        location / {
            proxy_pass  http://localhost:8080/;
        }
    }
}
```



## 使用

```sh
# 1. 启动容器
$ docker run --rm -d --name nginx1 nginx

# 2. 拷贝容器配置文件等，到本地(先 cd 到目标目录)
$ docker cp nginx1:/etc/nginx/nginx.conf ./nginx/nginx.conf
$ docker cp nginx1:/usr/share/nginx/html/ ./nginx/html/
# log是启动容器后生成的，启动容器时还是空的，不需要开始就cp过来
# $ docker cp nginx1:/var/log/nginx/ ./nginx/log/ 

# 3. 关闭临时容器 --rm 的会自动删除

# 4. 启动容器并设置挂载文件
$ docker run -d --name ng1 \
-p 80:80 \
-v ./nginx/nginx.conf:/etc/nginx/nginx.conf \
-v ./nginx/log/:/var/log/nginx/ \
-v ./nginx/html/:/usr/share/nginx/html/ \
nginx

# 5. 修改配置文件后，nginx重载（修改html无需）
$ docker exec nginx1 nginx -s reload

```







# MySql

## 目录结构

```sh
# 配置文件
/etc/mysql

# 日志
/var/log/mysql

# 数据
/var/lib/mysql

 # mysql第一次启动时执行,之后重启容器不会重复执行
/docker-entrypoint-initdb.d

```



## 使用

```sh
# 1. 启动容器
$ docker run --rm -it --name mysql1 mysql bin/bash
# ctrl + p + q 退出容器，-d的mysql会自动关闭？

# 2. 拷贝容器配置文件等，到本地(先 cd 到目标目录)
$ docker cp mysql1:/etc/mysql ./mysql/conf

# 3. 手动删除容器
$ docker rm -f mysql1

# 4. 启动容器并设置挂载文件
$ docker run -d --name mysql1 \
-p 3306:3306 \
-v ./mysql/conf:/etc/mysql \
-v ./mysql/log:/var/log/mysql \
-v ./mysql/init:/docker-entrypoint-initdb.d \
nginx

# 5. 修改配置文件后，nginx重载（修改html无需）
$ docker exec nginx1 nginx -s reload

```





# Compose综合使用

正常情况下 nginx直接按需代理8080

但nginx在容器内，SpringBoot服务在另一个容器，无法直接网络互通

这就牵涉到容器间网路通信了，使用docker-compose解决

```yaml
# docker-compose-yml
version: "3.9"
services:
  web:
    build: .  # 使用Dockerfile新生成镜像
    ports: # 宿主机端口:容器端口
      - "443:443"
    depends_on: # 依赖先执行的容器
      - nginx
      - mysql

  nginx:
    image: nginx
    restart: always
    ports:
      - "80:80"
    volumes: # 宿主机目录:容器目录
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf  # 配置
      - ./nginx/log:/var/log/nginx  # 日志
      - ./nginx/html:/usr/share/nginx/html  # 网页

  mysql:
    image: mysql:8.0.26
    restart: always
    environment: # 环境变量配置去 https://hub.docker.com/_/mysql 官方查看
      MYSQL_ROOT_PASSWORD: "root" # root账号密码
    ports:
      - "3306:3306"
    volumes:
      - ./mysql/conf:/etc/mysql # 配置
      - ./mysql/log:/var/log/mysql # 日志
      - ./mysql/init:/docker-entrypoint-initdb.d # mysql第一次启动时执行,之后重启容器不会重复执行
```



CD到 docker-compose.yml 目录，构建

```sh
$ docker-compose up
```





