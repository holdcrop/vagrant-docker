FROM centos:6

RUN yum install -y wget gcc make unzip epel-release
RUN wget https://centos6.iuscommunity.org/ius-release.rpm && rpm -Uvh ius-release.rpm
RUN wget -O /etc/yum.repos.d/couchbase.repo http://packages.couchbase.com/rpm/couchbase-centos62-x86_64.repo
RUN yum install -y libcouchbase-devel
RUN yum --noplugins install -y php56u-cli php56u-common php56u-devel php56u-fpm php56u-gd php56u-mbstring php56u-mcrypt php56u-mysql php56u-pecl-igbinary-devel php56u-process php56u-pecl-redis php56u-xml

RUN sed -i 's/;igbinary.compact_strings=On/igbinary.compact_strings=On/' /etc/php.d/40-igbinary.ini
RUN sed -i 's/;session.serialize_handler=igbinary/session.serialize_handler=igbinary/' /etc/php.d/40-igbinary.ini

ADD www.conf /etc/php-fpm.d/

RUN usermod -d 1000 php-fpm

RUN wget https://getcomposer.org/installer && php installer --install-dir=/usr/local/bin --filename=composer
RUN rm installer
