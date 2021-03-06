FROM nginx:stable

ENV SSP_VERSION="1.16.0"

RUN apt-get update && \
    apt-get install -y --no-install-recommends --no-install-suggests \
        ca-certificates \
        git \
        curl \
        php-cli

RUN git clone --quiet https://github.com/simplesamlphp/simplesamlphp.git && \
    cd simplesamlphp && \
    git checkout --quiet tags/v${SSP_VERSION} && \
    curl -sS https://getcomposer.org/installer | php -- --filename=composer.phar && \
    php composer.phar install \
        --quiet \
        --no-dev \ 
        --ignore-platform-reqs \
        --no-suggest && \
    php composer.phar require colourbox/simplesamlphp-module-redis \
        --quiet \
        --prefer-dist \
        --no-suggest \
        --update-no-dev \
        --ignore-platform-reqs && \
    php composer.phar require simplesamlphp/simplesamlphp-module-modinfo \
        --quiet \
        --prefer-dist \
        --no-suggest \
        --update-no-dev \
        --ignore-platform-reqs && \
    cp -r config-templates/* config && \
    cp -r metadata-templates/* metadata

RUN apt-get purge -y \
        ca-certificates \
        git \
        curl \
        php5-cli && \
    apt-get autoremove -y && \
    apt-get clean && \
    rm -r /var/lib/apt/lists/* && \
    apt-get clean

COPY nginx-site.conf /etc/nginx/conf.d/nginx-site.conf
COPY simplesamlphp-overrides /simplesamlphp/
RUN mkdir /var/www && echo "Hello world!" > /var/www/index.html && \
    chown -R www-data:www-data /var/www && \
    chown -R www-data:www-data /simplesamlphp

EXPOSE 80
