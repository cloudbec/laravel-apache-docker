FROM nuagebec/base-laravel-apache-docker

WORKDIR /var/www/html


ONBUILD COPY composer.json composer.lock artisan /var/www/html/
ONBUILD COPY database /var/www/html/database/

ONBUILD RUN composer install --prefer-dist --optimize-autoloader --no-scripts --no-dev --profile --ignore-platform-reqs -vvv

ONBUILD COPY package.json /var/www/html/
ONBUILD RUN npm install

ONBUILD COPY . /var/www/html

ONBUILD RUN php artisan clear-compiled
ONBUILD RUN php artisan optimize
ONBUILD RUN php artisan config:cache

# Configure directory permissions for the web server
ONBUILD RUN chgrp -R www-data storage /var/www/html/bootstrap/cache
ONBUILD RUN chmod -R ug+rwx storage /var/www/html/bootstrap/cache

ONBUILD RUN chgrp -R www-data storage /var/www/html/storage
ONBUILD RUN chmod -R ug+rwx storage /var/www/html/storage

# Configure data volume
ONBUILD VOLUME /var/www/html/storage/app
ONBUILD VOLUME /var/www/html/storage/framework/sessions
ONBUILD VOLUME /var/www/html/storage/logs

# Run frontend tasks
ONBUILD RUN gulp --production

# Transform into a lightweight image
ONBUILD RUN rm -R /var/www/html/node_modules
ONBUILD RUN rm -Rf tests/

# We need node no more
ONBUILD RUN rm /usr/local/bin/node \
    && rm /usr/local/bin/npm \
    && rm /usr/local/bin/gulp

COPY laravel-apache2-foreground /usr/local/bin/

CMD ["laravel-apache2-foreground"]
