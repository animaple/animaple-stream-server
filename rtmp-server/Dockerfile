FROM debian:buster-slim
LABEL maintainer="Richard Gan <richard@animaple.moe>"

ENV NGINX_VERSION nginx-1.17.10
ENV NGINX_RTMP_MODULE_VERSION 1.2.1

RUN set -ex && \
    # Create nginx user
    addgroup --system --gid 101 nginx && \
    adduser --system --disabled-login --ingroup nginx --no-create-home --home /nonexistent --gecos "nginx user" --shell /bin/false --uid 101 nginx && \
    # Install build deps
    apt-get update && \
    apt-get install -y --no-install-recommends --no-install-suggests \
    build-essential wget ca-certificates libpcre3-dev zlib1g-dev openssl libssl-dev && \
    # Get nginx build files
    mkdir -p /tmp/build/nginx && \
    cd /tmp/build/nginx && \
    wget -O ${NGINX_VERSION}.tar.gz https://nginx.org/download/${NGINX_VERSION}.tar.gz && \
    tar -zxf ${NGINX_VERSION}.tar.gz && \
    # Get nginx-rtmp build files
    mkdir -p /tmp/build/nginx-rtmp-module && \
    cd /tmp/build/nginx-rtmp-module && \
    wget -O nginx-rtmp-module-${NGINX_RTMP_MODULE_VERSION}.tar.gz https://github.com/arut/nginx-rtmp-module/archive/v${NGINX_RTMP_MODULE_VERSION}.tar.gz && \
    tar -zxf nginx-rtmp-module-${NGINX_RTMP_MODULE_VERSION}.tar.gz && \
    # Build nginx
    cd /tmp/build/nginx/${NGINX_VERSION} && \
    ./configure \
        --sbin-path=/usr/local/sbin/nginx \
        --conf-path=/etc/nginx/nginx.conf \
        --error-log-path=/var/log/nginx/error.log \
        --pid-path=/var/run/nginx/nginx.pid \
        --lock-path=/var/lock/nginx/nginx.lock \
        --http-log-path=/var/log/nginx/access.log \
        --http-client-body-temp-path=/tmp/nginx-client-body \
        --with-http_ssl_module \
        --with-threads \
        --with-ipv6 \
        --add-module=/tmp/build/nginx-rtmp-module/nginx-rtmp-module-${NGINX_RTMP_MODULE_VERSION} && \
    make CFLAGS=-Wno-error -j $(getconf _NPROCESSORS_ONLN) && \
    make install && \
    mkdir /var/lock/nginx && \
    touch /var/log/nginx/access.log /var/log/nginx/error.log && \
    # Clean up build files
    rm -rf /tmp/build && \
    apt-get purge -y --auto-remove \
    build-essential wget ca-certificates && \
    rm -rf /var/lib/apt/lists/*

# Forward logs to Docker
RUN ln -sf /dev/stdout /var/log/nginx/access.log && \
    ln -sf /dev/stderr /var/log/nginx/error.log

COPY nginx.conf /etc/nginx/nginx.conf

EXPOSE 1935

STOPSIGNAL SIGTERM

CMD ["nginx", "-g", "daemon off;"]