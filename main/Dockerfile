FROM dockercompose_php7.2

# Set up XDebug
RUN cat /dev/null > /etc/php.d/15-xdebug.ini
RUN echo -e "; Enable xdebug extension module" >> /etc/php.d/15-xdebug.ini
RUN echo -e "zend_extension=xdebug.so" >> /etc/php.d/15-xdebug.ini
RUN echo -e "xdebug.remote_enable=1" >> /etc/php.d/15-xdebug.ini
RUN echo -e "xdebug.remote_autostart=0" >> /etc/php.d/15-xdebug.ini
RUN echo -e "xdebug.remote_connect_back=1" >> /etc/php.d/15-xdebug.ini
RUN echo -e "xdebug.remote_port=9100" >> /etc/php.d/15-xdebug.ini
RUN echo -e "xdebug.profiler_enable=0" >> /etc/php.d/15-xdebug.ini
RUN echo -e "xdebug.profiler_enable_trigger=1" >> /etc/php.d/15-xdebug.ini
RUN echo -e "xdebug.profiler_append=0" >> /etc/php.d/15-xdebug.ini
RUN echo -e "xdebug.idekey=PHPSTORM" >> /etc/php.d/15-xdebug.ini

RUN sed -i 's/^;\?post_max_size \?= 8M\?8M/post_max_size = 32M/Ig' /etc/php.ini
RUN sed -i 's/^;\?upload_max_filesize \?= 2M\?2M/upload_max_filesize = 10M/Ig' /etc/php.ini

CMD ["php-fpm", "-F"]

EXPOSE 9000
EXPOSE 9100