# Use a imagem oficial do PHP com o Apache
FROM php:8.2-apache

# Instale as extensões do PHP necessárias para o Joomla
RUN apt-get update \
    && apt-get install -y \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libpng-dev \
        libzip-dev \
        unzip \
        wget \
        curl \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install -j$(nproc) gd mysqli zip \
    && a2enmod rewrite

# Baixe e descompacte o Joomla
RUN mkdir -p /usr/src/joomla \
    && wget -q https://downloads.joomla.org/br/cms/joomla5/5-1-1/Joomla_5-1-1-Stable-Full_Package.zip -O /tmp/joomla.zip \
    && unzip /tmp/joomla.zip -d /usr/src/joomla \
    && rm /tmp/joomla.zip

# Exponha a porta 80 para acessar o Joomla
EXPOSE 80

# Defina o diretório de trabalho como o diretório do Apache
WORKDIR /var/www/html

# Script de inicialização para copiar arquivos do Joomla se o diretório estiver vazio
CMD ["sh", "-c", "if [ ! -f /var/www/html/index.php ]; then cp -r /usr/src/joomla/* /var/www/html/ && chown -R www-data:www-data /var/www/html; fi && apache2-foreground"]


# Script de inicialização para copiar arquivos do Joomla se o diretório estiver vazio
CMD ["sh", "-c", "if [ ! -f /var/www/html/index.php ]; then cp -r /usr/src/joomla/* /var/www/html/ && chown -R www-data:www-data /var/www/html; fi && apache2-foreground"]


# Configuração no EasyPanel
# Bind Mount para Joomla:
# No EasyPanel, vá até a seção de bind mounts.
# Adicione um novo bind mount.
# Preencha os campos com os seguintes valores:
# - Host Path: /srv/docker/joomla
# - Mount Path: /var/www/html

# Bind Mount para Configuração PHP:
# Adicione outro bind mount.
# Preencha os campos com os seguintes valores:
# - Host Path: /srv/docker/php
# - Mount Path: /usr/local/etc/php

# Resumo das Configurações
# Joomla Bind Mount:
# - Host Path: /srv/docker/joomla
# - Mount Path: /var/www/html

# PHP Config Bind Mount:
# - Host Path: /srv/docker/php
# - Mount Path: /usr/local/etc/php

