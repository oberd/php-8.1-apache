
[Docker Hub](https://hub.docker.com/repository/docker/oberd/php-8.0-apache)

[ECR](https://gallery.ecr.aws/v6d3r0a9/php-8.0-apache)


## PHP 8.0 Apache Base Image

This base image contains some helper functionality to get a basic PHP project up and running. It utilizes [Task](https://taskfile.dev/) and contains some basic tasks to perform common functionality.

### Pre-installed PHP Extensions
The following PHP extensions are installed into this base image by default. You can view the modules by running `docker run oberd/php-8.0-apache php -m`

To include additional extensions, follow the steps from the [official PHP base image documentation](https://hub.docker.com/_/php). _Although, You should consider if the extension should be added to the base image or your project specific Dockerfile._

```
[PHP Modules]
bcmath
Core
ctype
curl
date
dom
fileinfo
filter
ftp
hash
iconv
json
libxml
mbstring
mysqlnd
openssl
pcre
PDO
pdo_mysql
pdo_sqlite
Phar
posix
readline
Reflection
session
SimpleXML
soap
sodium
SPL
sqlite3
standard
tokenizer
xml
xmlreader
xmlwriter
zip
zlib

[Zend Modules]
```

### Usage

The [usage folder](/usage) folder contains an example Dockerfile and Taskfile.yml that you can include in your project to get up and running.

### Helpers

#### Commands
If you're making a development stage of a multi-stage Dockerfile and want to enable Xdebug (should not be enabled in a production environment), you can add the following line to your development stage. It will install and enable the xdebug extension.

```Dockerfile
RUN enable-xdebug
```

### Taskfile

The following tasks to be ran in a Taskfile are already pre-configured in `/usr/local/tasks/` for usage in your projects. Check the sample [Taskfile.yml](/usage/Taskfile.yml) for a standard usage in a Laravel environment.

#### Apache

##### start
This task will start Apache in the foreground

`task -t tasks/apache.yml start`

#### Laravel

##### routes
This task will [cache your Laravel routes](https://laravel.com/docs/8.x/controllers#route-caching). Should only really be used in a production environment.

`task -t tasks/laravel.yml cache`

##### migrate
This task will [run your Laravel migrations](https://laravel.com/docs/8.x/migrations#running-migrations) using the `--force` flag.

`task -t tasks/laravel.yml migrate`

#### Composer
The Composer tasks should really only be ran in a development environment where the vendor directory may be volume mounted and cannot be included into the Docker image itself. A production image should contain the installed vendor directory within the image for consistency.

##### configure

This task will configure composer with your Github OAuth token to allow access to private repositories and not be rate limited.

**Requires:** the environment variable `GITHUB_OAUTH_TOKEN` to be set.

`task -t tasks/composer.yml configure`

##### install
This task is dependent upon the `configure` task being ran. It will run composer install.

`task -t tasks/composer.yml install`

#### PHP

##### xdebug-host
This task will configure the Xdebug remote host into the Xdebug config.

**Optional:** the environment variable `XDEBUG_HOST_IP` can be set. Else, it will default to `10.254.254.254`

### Sites

Using the built in Apache commands `a2ensite` and `a2dissite` to enable and disable site configurations, this base image contains pre-defined configurations for some standard PHP frameworks.

#### Laravel
To configure a standard Laravel project your code to `/var/www/app` and this configuration will point the DocumentRoot to the public folder `/var/www/app/public`

```Dockerfile
RUN a2ensite laravel
COPY --chown=www-data:www-data . /var/www/app
```
