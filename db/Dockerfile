FROM mysql/mysql-server:5.7

COPY docker-entrypoint.sh /usr/local/bin/

RUN cat /dev/null > /etc/my.cnf
RUN echo -e "[mysqld]" >> /etc/my.cnf
RUN echo -e "user = mysql"  >> /etc/my.cnf
RUN echo -e "datadir = /var/lib/mysql"  >> /etc/my.cnf
RUN echo -e "socket = /var/lib/mysql/mysql.sock"  >> /etc/my.cnf
RUN echo -e "explicit_defaults_for_timestamp = 1"  >> /etc/my.cnf
RUN echo -e "symbolic-links = 0"  >> /etc/my.cnf

ENTRYPOINT ["docker-entrypoint.sh"]

EXPOSE 3306
CMD ["mysqld"]
