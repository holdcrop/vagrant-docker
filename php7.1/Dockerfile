FROM centos:7

RUN yum install -y https://$(rpm -E '%{?centos:centos}%{!?centos:rhel}%{rhel}').iuscommunity.org/ius-release.rpm
RUN rpm -Uvh http://rpms.famillecollet.com/enterprise/remi-release-7.rpm
RUN yum install -y tzdata && echo "UTC" | tee /etc/timezone
RUN yum -y update

RUN yum install -y \
    openssh \
    make \
    php71u-common \
    php71u-devel \
    php71u-cli \
    php71u-bcmath \
    php71u-fpm \
    php71u-json \
    php71u-mbstring \
    php71u-mysqlnd \
    php71u-pecl-igbinary \
    php71u-pecl-redis \
    php71u-pecl-yaml \
    php71u-pdo \
    php71u-process \
    php71u-xml \
    pecl \
    wget

RUN sed -i 's/;igbinary.compact_strings=On/igbinary.compact_strings=On/' /etc/php.d/40-igbinary.ini
RUN sed -i 's/;session.serialize_handler=igbinary/session.serialize_handler=igbinary/' /etc/php.d/40-igbinary.ini
    
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

COPY www.conf /etc/php-fpm.d/www.conf