FROM dockercompose_php7.2

RUN yum install -y wget gcc make unzip epel-release cronie

RUN sed -i "s/^;date.timezone =$/date.timezone = \"Europe\/Dublin\"/" /etc/php.ini
RUN echo -e "\nallow_url_fopen=On\n" >> /etc/php.ini

# Add the Cron Tab
COPY cron.bak /home/crontab
RUN crontab /home/crontab

CMD ["crond", "-n"]