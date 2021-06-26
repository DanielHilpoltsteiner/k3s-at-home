FROM nextcloud:21.0.2-apache

RUN set -ex; \
    apt-get update && \
    apt-get install nano sudo && \
    mkdir /data && \
    chown www-data:root /data

COPY nextcloud.ini /usr/local/etc/php/conf.d/nextcloud.ini
