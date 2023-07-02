# source: https://github.com/0916dhkim/vscode-devcontainer-tutorial

FROM ubuntu:22.04

ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=$USER_UID
ARG PASSWORD=vscode

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get -y update --no-install-recommends \
    && apt-get -y install --no-install-recommends \
    build-essential \
    curl \
    ca-certificates \
    apt-utils \
    dialog \
    git \
    vim \
    less \
    && apt-get autoremove -y \
    && apt-get clean -y

RUN apt-get install -y nginx \
    systemctl \
    supervisor \
    cron \
    python3-pip \
    python3-poetry \
    golang-go \
    libpq-dev \
    unixodbc-dev \
    sudo

RUN apt install php8.1-fpm \
                php8.1 \ 
                libapache2-mod-php8.1 \
                php8.1-common \
                php8.1-mysql \
                php8.1-pgsql \
                php8.1-xml \
                php8.1-xmlrpc \
                php8.1-curl \
                php8.1-gd \
                php8.1-imagick \
                php8.1-cli \
                php8.1-imap \
                php8.1-mbstring \
                php8.1-opcache \
                php8.1-soap \
                php8.1-zip \
                php8.1-intl \
                php8.1-bcmath \
                php8.1-dev \
                php-pear \
                unzip -y

COPY .devcontainer/build/php-ext/mongodb.ini /etc/php/8.1/mods-available/mongodb.ini
COPY .devcontainer/build/php-ext/redis.ini /etc/php/8.1/mods-available/redis.ini
COPY .devcontainer/build/php-ext/sqlsrv.ini /etc/php/8.1/mods-available/sqlsrv.ini

RUN pecl install mongodb && phpenmod mongodb
RUN pecl install redis && phpenmod redis
RUN pecl install sqlsrv && phpenmod sqlsrv
RUN pecl install pdo_sqlsrv && phpenmod pdo_sqlsrv

RUN curl -sS https://getcomposer.org/installer -o /tmp/composer-setup.php
RUN php /tmp/composer-setup.php --install-dir=/usr/local/bin --filename=composer

RUN curl -sL https://deb.nodesource.com/setup_18.x -o nodesource_setup.sh
RUN apt install -y nodejs

COPY .devcontainer/build/nginx/nginx.conf /etc/nginx/conf.d/default.conf
COPY .devcontainer/build/crontab/root /etc/cron.d/root
COPY .devcontainer/build/supervisor/supervisord.conf /etc/supervisor/supervisord.conf
COPY .devcontainer/build/supervisor/build.sh /etc/supervisor/build.sh

RUN chmod +x /etc/supervisor/build.sh
RUN chown -R $USER_UID:$USER_GID /etc/supervisor/build.sh

RUN curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
RUN unzip awscliv2.zip
RUN ./aws/install


RUN rm -rf /usr/share/nginx/html
# RUN ln -s /workspaces/web-app /usr/share/nginx/html

RUN groupadd --gid $USER_GID $USERNAME \
    && useradd --uid $USER_UID --gid $USER_GID -m $USERNAME \
    && usermod -aG sudo $USERNAME

RUN echo $USERNAME":"$PASSWORD | chpasswd

RUN crontab /etc/cron.d/root

RUN touch /var/log/cron.log
RUN touch /var/log/supervisor/supervisord.log

ENV DEBIAN_FRONTEND=dialog