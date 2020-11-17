FROM alpine:latest

RUN apk update && apk upgrade --no-cache

RUN apk add --no-cache \
    bash \
    freeradius \
    freeradius-utils \
    freeradius-pam \
    google-authenticator \
    libqrencode

COPY ./etc/pam.d/radiusd /etc/pam.d/radiusd 
COPY ./etc/raddb/radiusd.conf /etc/raddb/radiusd.conf
COPY ./etc/raddb/sites-available/default /etc/raddb/sites-available/default
COPY ./etc/raddb/users /etc/raddb/users
COPY ./etc/raddb/clients.conf /etc/raddb/clients.conf

EXPOSE 1812/udp 1813/udp

COPY docker-entrypoint.sh /
ENTRYPOINT ["/docker-entrypoint.sh"]
