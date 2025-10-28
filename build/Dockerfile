FROM php:7.2-fpm

RUN cat > /etc/apt/sources.list << EOF
        deb https://archive.debian.org/debian buster main non-free contrib
        deb https://archive.debian.org/debian buster-updates main non-free contrib
        deb https://archive.debian.org/debian buster-backports main non-free contrib
EOF

RUN sed -i 's|https://archive.debian.org/debian|http://archive.debian.org/debian|g' /etc/apt/sources.list \
 && echo 'Acquire::Check-Valid-Until "false";' > /etc/apt/apt.conf.d/99disable-check-valid-until \
 && echo 'Acquire::AllowInsecureRepositories "true";' > /etc/apt/apt.conf.d/99insecure-repos \
 && echo 'Acquire::AllowDowngradeToInsecureRepositories "true";' >> /etc/apt/apt.conf.d/99insecure-repos \
 && apt-get -o Acquire::AllowInsecureRepositories=true update \
 && apt-get install -y --allow-unauthenticated \
    git nginx unzip libpng-dev libjpeg-dev libfreetype6-dev libzip-dev snmp libsnmp-dev libldap2-dev \
 && docker-php-ext-configure gd \
 && docker-php-ext-install gd mysqli pdo pdo_mysql zip bcmath snmp pcntl ldap \
 && rm -rf /var/lib/apt/lists/*


WORKDIR /var/www/html

COPY RackTables-0.22.0.tar.gz /tmp/racktables.tar.gz

RUN tar -xzf /tmp/racktables.tar.gz -C /var/www/html --strip-components=1 && \
    rm /tmp/racktables.tar.gz

COPY nginx.conf /etc/nginx/conf.d/default.conf

RUN chown -R www-data:www-data /var/www/html && rm /etc/nginx/sites-enabled/default

EXPOSE 80

CMD service nginx start && php-fpm