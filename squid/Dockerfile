FROM alpine:latest

RUN apk update && apk upgrade --no-cache

RUN apk add --no-cache squid

COPY ./etc/squid/squid.conf /etc/squid/squid.conf
COPY ./etc/squid/radius.conf /etc/squid/radius.conf

EXPOSE 8080

ENTRYPOINT ["/usr/sbin/squid", "-NYC"]
