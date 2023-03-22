FROM mysql:8.0

ENV MYSQL_ROOT_PASSWORD=my-secret-pw

ENV MYSQL_DATABASE=myappdb
ENV MYSQL_USER=myappuser
ENV MYSQL_PASSWORD=myappw

COPY init.sql /docker-entrypoint-initdb.d/

EXPOSE 3306
