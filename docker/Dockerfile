#FROM alpine:3.22.1 AS release
#
## LATEST_TAG=$( curl -s "https://api.github.com/repos/LinkStackOrg/LinkStack/releases/latest" | jq '(.name)' | sed 's/\"//g' )
## curl -OL https://github.com/LinkStackOrg/LinkStack/releases/download/${LATEST_TAG}/linkstack.zip 
#
#ARG LINKSTACK_VERSION="v4.8.4"
#
#RUN apk add --no-cache --update curl jq
#
#RUN curl -OL https://github.com/LinkStackOrg/LinkStack/releases/download/${LINKSTACK_VERSION}/linkstack.zip 
#
#RUN unzip -q linkstack.zip

FROM alpine:3.22.1

LABEL maintainer="JulianPrieber"
LABEL description="LinkStack Docker"

EXPOSE 80 443

# Setup apache and php
RUN apk add --no-cache --update \
    apache2 \
    apache2-ssl \
    curl \
    php82-apache2 \
    php82-bcmath \
    php82-bz2 \
    php82-calendar \
    php82-common \
    php82-ctype \
    php82-curl \
    php82-dom \
    php82-fileinfo \
    php82-gd \
    php82-iconv \
    php82-json \
    php82-mbstring \
    php82-mysqli \
    php82-mysqlnd \
    php82-openssl \
    php82-pdo_mysql \
    php82-pdo_pgsql \
    php82-pdo_sqlite \
    php82-phar \
    php82-session \
    php82-xml \
    php82-tokenizer \
    php82-zip \
    php82-xmlwriter \
    php82-redis \
    tzdata
    
COPY linkstack.zip /
RUN unzip /linkstack.zip
RUN mv /linkstack /htdocs

COPY configs/apache2/httpd.conf /etc/apache2/httpd.conf
COPY configs/apache2/ssl.conf /etc/apache2/conf.d/ssl.conf
COPY configs/php/php.ini /etc/php8.2/php.ini

RUN chown apache:apache /etc/ssl/apache2/server.pem
RUN chown apache:apache /etc/ssl/apache2/server.key

RUN chown -R apache:apache /htdocs
RUN find /htdocs -type d -print0 | xargs -0 chmod 0755
RUN find /htdocs -type f -print0 | xargs -0 chmod 0644

RUN ln -s /usr/bin/php82 /usr/bin/php

COPY --chmod=0755 docker-entrypoint.sh /usr/local/bin/

USER apache:apache

HEALTHCHECK CMD curl -f http://localhost -A "HealthCheck" || exit 1

# Set console entry path
WORKDIR /htdocs

CMD ["docker-entrypoint.sh"]
