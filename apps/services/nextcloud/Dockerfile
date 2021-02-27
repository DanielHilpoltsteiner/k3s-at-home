FROM nextcloud:21.0.0-apache

RUN set -ex; \
    apt update && apt install nano sudo; \
    mkdir /data && chown www-data:root /data
