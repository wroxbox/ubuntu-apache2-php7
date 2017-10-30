FROM ubuntu:16.04

LABEL maintainer="Antti Tuppurainen"

RUN apt-get update && apt-get install -y --no-install-recommends \
		apache2 \
		vim \
		nano \
		git \
		zip \
		curl \
		gcc \
		make \
		autoconf \
		libc-dev \
		pkg-config \
		software-properties-common \
		python-software-properties \
		language-pack-en-base \
		supervisor \
	&& apt-get clean \
	&& rm -fr /var/lib/apt/lists/*

RUN LC_ALL=C.UTF-8 add-apt-repository ppa:ondrej/php

RUN apt-get update && apt-get install -y --no-install-recommends \
		libapache2-mod-php7.1 \
		php7.1 \
		php7.1-cli \
		php7.1-curl \
		php7.1-dev \
		php7.1-gd \
		php7.1-imap \
		php7.1-mbstring \
		php7.1-mcrypt \
		php7.1-mysql \
		php7.1-pgsql \
		php7.1-pspell \
		php7.1-xml \
		php7.1-xmlrpc \
		php7.1-dev \
		php-apcu \
		php-memcached \
		php-pear \
		php-redis \
		php-xdebug \
	&& apt-get clean \
	&& rm -fr /var/lib/apt/lists/*

#RUN pecl install apcu_bc-1.0.3 -y
#RUN pecl install apcu
#RUN echo "extension=apcu.so" >> /etc/php/7.1/apache2/conf.d/apcu.ini
RUN echo "extension=apc.so" >> /etc/php/7.1/apache2/conf.d/apc.ini
RUN a2enmod rewrite
RUN echo "xdebug.max_nesting_level=250" >> /etc/php/7.1/apache2/conf.d/20-xdebug.ini
RUN echo 'xdebug.remote_enable=1' >> /etc/php/7.1/apache2/conf.d/20-xdebug.ini
RUN echo 'xdebug.idekey=PHPSTORM' >> /etc/php/7.1/apache2/conf.d/20-xdebug.ini
RUN echo 'xdebug.remote_host=192.168.33.1' >> /etc/php/7.1/apache2/conf.d/20-xdebug.ini
RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
RUN php composer-setup.php
RUN cp composer.phar /usr/bin/composer
RUN sed -i 's/a/b/g' /etc/php/7.1/apache2/php.ini
RUN service apache2 restart


COPY conf/000-default.conf /etc/apache2/sites-available/000-default.conf
COPY conf/000-default-ssl.conf /etc/apache2/sites-available/000-default-ssl.conf
COPY conf/server.crt /etc/ssl/certs/server.crt
COPY conf/server.key /etc/ssl/private/server.key
COPY conf/php.ini /etc/php/7.0/apache2/php.ini

RUN a2enmod ssl
RUN a2ensite 000-default-ssl

COPY conf/supervisord.conf /etc/supervisor/conf.d/supervisord.conf

COPY script/run.sh /run.sh
RUN chmod 755 /run.sh

COPY conf/config /config

RUN ln -sf /dev/stdout /var/log/apache2/access.log
RUN ln -sf /dev/stderr /var/log/apache2/error.log

VOLUME ["/var/www/html"]

EXPOSE 80
CMD ["/run.sh"]
