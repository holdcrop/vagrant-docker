FROM nginx

# Add conf files
ADD nginx.conf /etc/nginx

RUN mkdir -p /etc/nginx/sites-enabled

# Add the enabled sites
RUN ln -s /etc/nginx/sites-available/api.conf /etc/nginx/sites-enabled/api.conf

# Add connection to PHP-FPM
RUN echo "upstream main-upstream { server main:9000; }\n" > /etc/nginx/conf.d/upstream.conf

# Add the user
RUN usermod -u 1000 nginx

CMD ["nginx", "-g", "daemon off;"]

EXPOSE 80
EXPOSE 443