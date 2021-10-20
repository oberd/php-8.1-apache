FROM test/php7 as build

RUN echo "ServerName test-name" > /etc/apache2/conf-available/server-name.conf && a2enconf server-name.conf
RUN a2ensite laravel

WORKDIR /var/www/app

FROM build as dev
ARG GITHUB_OAUTH_TOKEN
ENV GITHUB_OAUTH_TOKEN=${GITHUB_OAUTH_TOKEN}
RUN mv "$PHP_INI_DIR/php.ini-development" "$PHP_INI_DIR/php.ini"
RUN enable-xdebug
CMD ["task", "dev"]

FROM build as prod
ARG GITHUB_OAUTH_TOKEN
COPY --chown=www-data:www-data . /var/www/app
RUN composer config -g github-oauth.github.com $GITHUB_OAUTH_TOKEN
RUN composer install --prefer-dist --no-interaction
CMD ["task", "prod"]
