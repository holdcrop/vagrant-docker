FROM centos:7

RUN yum install -y https://$(rpm -E '%{?centos:centos}%{!?centos:rhel}%{rhel}').iuscommunity.org/ius-release.rpm
RUN rpm -Uvh http://rpms.famillecollet.com/enterprise/remi-release-7.rpm
RUN yum install -y tzdata && echo "UTC" | tee /etc/timezone
RUN yum -y update
RUN yum install -y sendmail

RUN yum remove -y php*
RUN yum install -y \
    openssh \
    make \
    php72u-common \
    php72u-devel \
    php72u-cli \
    php72u-bcmath \
    php72u-fpm \
    php72u-json \
    php72u-mbstring \
    php72u-mysqlnd \
    php72u-opcache \
    php72u-pecl-igbinary \
    php72u-pecl-redis \
    php72u-pecl-xdebug \
    php72u-pdo \
    php72u-process \
    php72u-soap \
    php72u-xml \
    php72u-gd \
    wget

RUN sed -i 's/;igbinary.compact_strings=On/igbinary.compact_strings=On/' /etc/php.d/40-igbinary.ini
RUN sed -i 's/;session.serialize_handler=igbinary/session.serialize_handler=igbinary/' /etc/php.d/40-igbinary.ini

RUN sed -i "s/^;date.timezone =$/date.timezone = \"Europe\/Dublin\"/" /etc/php.ini
RUN sed -i "s/^;\?display_errors \?= \?off/display_errors = On/Ig" /etc/php.ini
RUN sed -i 's/^;\?expose_php \?= \?off/expose_php = On/Ig' /etc/php.ini
RUN sed -i 's/^;\?allow_url_fopen \?= off\?off/allow_url_fopen = On/Ig' /etc/php.ini
    
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

COPY www.conf /etc/php-fpm.d/www.conf