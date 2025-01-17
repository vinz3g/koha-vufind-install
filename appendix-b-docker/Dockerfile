FROM debian:bullseye

ENV DEBIAN_FRONTEND noninteractive
ARG S6_OVERLAY_VERSION=3.1.3.0

LABEL org.opencontainers.image.source=https://github.com/teorgamm/koha-vufind-install

RUN apt-get  update \
    && apt-get install -y \
            wget \
            apache2 \
            gnupg2 \
            apt-transport-https \
            xz-utils \
    && rm -rf /var/cache/apt/archives/* \
    && rm -rf /var/lib/apt/lists/*

ADD https://github.com/just-containers/s6-overlay/releases/download/v${S6_OVERLAY_VERSION}/s6-overlay-noarch.tar.xz /tmp
RUN tar -C / -Jxpf /tmp/s6-overlay-noarch.tar.xz
ADD https://github.com/just-containers/s6-overlay/releases/download/v${S6_OVERLAY_VERSION}/s6-overlay-x86_64.tar.xz /tmp
RUN tar -C / -Jxpf /tmp/s6-overlay-x86_64.tar.xz

RUN mkdir /etc/apt/keyrings/ && \
    wget -qO - https://debian.koha-community.org/koha/gpg.asc | gpg --dearmor -o /etc/apt/keyrings/koha.gpg && \
    echo "deb [signed-by=/etc/apt/keyrings/koha.gpg] https://debian.koha-community.org/koha stable main bullseye" | tee /etc/apt/sources.list.d/koha.list

# Install Koha
RUN apt-get update \
    && apt-get install -y koha-core \
       idzebra-2.0 \
       apache2 libapache2-mpm-itk\
    && rm -rf /var/cache/apt/archives/* \
    && rm -rf /var/lib/apt/lists/*

RUN a2enmod rewrite \
    && a2enmod headers \
    && a2enmod proxy_http \
    && a2enmod cgi \
    && a2dissite 000-default \
    && echo "Listen 8081\nListen 8080" > /etc/apache2/ports.conf

COPY files/ /
WORKDIR /docker

EXPOSE 2100 6001 8080 8081

CMD [ "/init" ]
