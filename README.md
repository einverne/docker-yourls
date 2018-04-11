# YOURLS with Docker

> Docker Official Image packaging for YOURLS

[![dockeri.co](https://dockeri.co/image/yourls/docker/)](https://hub.docker.com/r/yourls/docker/)

## About this Repo

这是官方维护的镜像的 fork [YOURLS](https://registry.hub.docker.com/r/yourls/docker/).

---

-	[Travis CI:  
	![build status badge](https://travis-ci.org/YOURLS/docker-yourls.svg?branch=master)](https://travis-ci.org/YOURLS/docker-yourls)

# What is YOURLS?

YOURLS 是由PHP写成的域名缩短服务。

![logo](https://raw.githubusercontent.com/YOURLS/YOURLS/master/images/yourls-logo.png)

# 如何使用镜像

```console
$ docker run --name some-yourls --link some-mysql:mysql -d yourls
```

镜像包含如下的环境变量：

- `-e YOURLS_DB_HOST=...` 指向数据库的ip和端口
- `-e YOURLS_DB_USER=...` 默认为 "root"
- `-e YOURLS_DB_PASS=...` 需要和mysql数据容器的 `MYSQL_ROOT_PASSWORD` 变量一致
- `-e YOURLS_DB_NAME=...` 默认为 "yourls"
- `-e YOURLS_TABLE_PREFIX=...` 默认为空 "", 只有当你需要覆盖 `wp-config.php` 中默认的前缀时使用
- `-e YOURLS_COOKIEKEY=...` (default to unique random SHA1s)
- `-e YOURLS_URL_CONVERT=...` 62 包含大小写
- `-e YOURLS_HOURS_OFFSET` 时区 +8
- `-e YOURLS_SITE` 域名，需要包含 http 或者 https
- `-e YOURLS_USER` 管理员名字
- `-e YOURLS_PASS` 管理员密码

如果 `YOURLS_DB_NAME` 中指定的数据库，在 MySQL 容器中不存在会创建

If you'd like to be able to access the instance from the host without the container's IP, standard port mappings can be used:

```console
$ docker run --name some-yourls --link some-mysql:mysql -p 8080:80 -d yourls
```

Then, access it via `http://localhost:8080` or `http://host-ip:8080` in a browser.

If you'd like to use an external database instead of a linked `mysql` container, specify the hostname and port with `YOURLS_DB_HOST` along with the password in `YOURLS_DB_PASS` and the username in `YOURLS_DB_USER` (if it is something other than `root`):

```console
$ docker run --name some-yourls -e YOURLS_DB_HOST=10.1.2.3:3306 \
    -e YOURLS_DB_USER=... -e YOURLS_DB_PASS=... -d yourls
```

## ... via [`docker stack deploy`](https://docs.docker.com/engine/reference/commandline/stack_deploy/) or [`docker-compose`](https://github.com/docker/compose)

Example `stack.yml` for `yourls`:

```yaml
version: '3.1'

services:

  yourls:
    image: yourls
    restart: always
    ports:
      - 8080:80
    environment:
      YOURLS_DB_PASS: example

  mysql:
    image: mysql:5.7
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: example
```

Run `docker stack deploy -c stack.yml yourls` (or `docker-compose -f stack.yml up`), wait for it to initialize completely, and visit `http://swarm-ip:8080`, `http://localhost:8080`, or `http://host-ip:8080` (as appropriate).

## Adding additional libraries / extensions

This image does not provide any additional PHP extensions or other libraries, even if they are required by popular plugins. There are an infinite number of possible plugins, and they potentially require any extension PHP supports. Including every PHP extension that exists would dramatically increase the image size.

If you need additional PHP extensions, you'll need to create your own image `FROM` this one. The [documentation of the `php` image](https://github.com/docker-library/docs/blob/master/php/README.md#how-to-install-more-php-extensions) explains how to compile additional extensions.

The following Docker Hub features can help with the task of keeping your dependent images up-to-date:

-	[Automated Builds](https://docs.docker.com/docker-hub/builds/) let Docker Hub automatically build your Dockerfile each time you push changes to it.
-	[Repository Links](https://docs.docker.com/docker-hub/builds/#repository-links) can ensure that your image is also rebuilt any time `yourls` is updated.

# Image Variants

The `yourls` images come in many flavors, each designed for a specific use case.

## `yourls:<version>`

This is the defacto image. If you are unsure about what your needs are, you probably want to use this one. It is designed to be used both as a throw away container (mount your source code and start the container to start your app), as well as the base to build other images off of.
