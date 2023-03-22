# Create a dockerfile web application
1- nano app.py
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello():
    return 'Welcome from musa!\n'

if __name__ == '__main__':
    app.run()

2- nano Dockerfile
FROM python:3.6-alpine

WORKDIR /app

COPY . /app

RUN pip install --trusted-host pypi.python.org -r requirements.txt

ENV FLASK_APP=app.py

EXPOSE 5000

CMD ["flask", "run", "--host=0.0.0.0"]

3- nano requirements.txt
flask

4- sudo docker build -t my_webapp .
Sending build context to Docker daemon  9.216kB
Step 1/7 : FROM python:3.6-alpine
 ---> 3a9e80fa4606
Step 2/7 : WORKDIR /app
 ---> Using cache
 ---> e6151eddfad6
Step 3/7 : COPY . /app
 ---> Using cache
 ---> 6a2fc50d5003
Step 4/7 : RUN pip install --trusted-host pypi.python.org -r requirements.txt
 ---> Using cache
 ---> b1ae177a2c66
Step 5/7 : ENV FLASK_APP=app.py
 ---> Using cache
 ---> ac8cb97015e9
Step 6/7 : EXPOSE 5000
 ---> Using cache
 ---> b746448960ea
Step 7/7 : CMD ["flask", "run", "--host=0.0.0.0"]
 ---> Using cache
 ---> 1990ed756b47
Successfully built 1990ed756b47
Successfully tagged my_webapp:latest

5- sudo docker run -p 5000:5000 my_webapp
 * Serving Flask app 'app.py' (lazy loading)
 * Environment: production
   WARNING: This is a development server. Do not use it in a production deployment.
   Use a production WSGI server instead.
 * Debug mode: off
 * Running on all addresses.
   WARNING: This is a development server. Do not use it in a production deployment.
 * Running on http://172.17.0.2:5000/ (Press CTRL+C to quit)

# Create a docker file for database
1- nano Dockerfile
FROM mysql:8.0

ENV MYSQL_ROOT_PASSWORD=my-secret-pw

ENV MYSQL_DATABASE=myappdb
ENV MYSQL_USER=myappuser
ENV MYSQL_PASSWORD=myappw

COPY init.sql /docker-entrypoint-initdb.d/

EXPOSE 3306

2- CREATE TABLE users (
    id int AUTO_INCREMENT PRIMARY KEY,
    name varchar(255) NOT NULL,
    age int
);

INSERT INTO users (name, age) VALUES ('Musa', 25);

3- sudo docker build -t my_db .
Sending build context to Docker daemon  3.072kB
Step 1/7 : FROM mysql:8.0
 ---> c2c2eba5ae85
Step 2/7 : ENV MYSQL_ROOT_PASSWORD=my-secret-pw
 ---> Using cache
 ---> 9a5c948d84f9
Step 3/7 : ENV MYSQL_DATABASE=myappdb
 ---> Using cache
 ---> 1b8e3e740e94
Step 4/7 : ENV MYSQL_USER=myappuser
 ---> Using cache
 ---> 3edb9ba9d9d7
Step 5/7 : ENV MYSQL_PASSWORD=myappw
 ---> Using cache
 ---> 23ce673cd78c
Step 6/7 : COPY init.sql /docker-entrypoint-initdb.d/
 ---> 8e36aee72f06
Step 7/7 : EXPOSE 3306
 ---> Running in 301796ab9aa8
Removing intermediate container 301796ab9aa8
 ---> 3ba33d5e3941
Successfully built 3ba33d5e3941
Successfully tagged my_db:latest

4- sudo docker run -p 3306:3306 --name my_db_container -d my_db
9d0ba828f06412422c01b322ffb2ce8c449d458bc5c59fa04bb69d28b4ba1267

5- sudo docker run -p 3306:3306 --name my_db_container -d my_db

6- sudo docker ps -a
CONTAINER ID   IMAGE          COMMAND                  CREATED             STATUS                         PORTS                                                  NAMES
99dcecce417d   my_db          "docker-entrypoint.s…"   2 minutes ago       Up 2 minutes                   0.0.0.0:3306->3306/tcp, :::3306->3306/tcp, 33060/tcp   my_db_container
2c9932d6e35a   my_webapp      "flask run --host=0.…"   8 minutes ago       Exited (0) 6 minutes ago                                                              dazzling_albattani

7- sudo docker logs 99dcecce417d
2023-03-22 00:13:00+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 8.0.31-1.el8 started.
2023-03-22 00:13:00+00:00 [Note] [Entrypoint]: Switching to dedicated user 'mysql'
2023-03-22 00:13:00+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 8.0.31-1.el8 started.
2023-03-22 00:13:01+00:00 [Note] [Entrypoint]: Initializing database files
2023-03-22T00:13:01.498058Z 0 [Warning] [MY-011068] [Server] The syntax '--skip-host-cache' is deprecated and will be removed in a future release. Please use SET GLOBAL host_cache_size=0 instead.
2023-03-22T00:13:01.498327Z 0 [System] [MY-013169] [Server] /usr/sbin/mysqld (mysqld 8.0.31) initializing of server in progress as process 80
2023-03-22T00:13:01.526990Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
2023-03-22T00:13:02.642020Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
2023-03-22T00:13:06.019992Z 6 [Warning] [MY-010453] [Server] root@localhost is created with an empty password ! Please consider switching off the --initialize-insecure option.
2023-03-22 00:13:13+00:00 [Note] [Entrypoint]: Database files initialized
2023-03-22 00:13:13+00:00 [Note] [Entrypoint]: Starting temporary server
2023-03-22T00:13:13.694837Z 0 [Warning] [MY-011068] [Server] The syntax '--skip-host-cache' is deprecated and will be removed in a future release. Please use SET GLOBAL host_cache_size=0 instead.
2023-03-22T00:13:13.697612Z 0 [System] [MY-010116] [Server] /usr/sbin/mysqld (mysqld 8.0.31) starting as process 129
2023-03-22T00:13:13.741623Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
2023-03-22T00:13:14.091150Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
2023-03-22T00:13:14.820770Z 0 [Warning] [MY-010068] [Server] CA certificate ca.pem is self signed.
2023-03-22T00:13:14.821132Z 0 [System] [MY-013602] [Server] Channel mysql_main configured to support TLS. Encrypted connections are now supported for this channel.
2023-03-22T00:13:14.824596Z 0 [Warning] [MY-011810] [Server] Insecure configuration for --pid-file: Location '/var/run/mysqld' in the path is accessible to all OS users. Consider choosing a different directory.
2023-03-22T00:13:14.881585Z 0 [System] [MY-010931] [Server] /usr/sbin/mysqld: ready for connections. Version: '8.0.31'  socket: '/var/run/mysqld/mysqld.sock'  port: 0  MySQL Community Server - GPL.
2023-03-22T00:13:14.882076Z 0 [System] [MY-011323] [Server] X Plugin ready for connections. Socket: /var/run/mysqld/mysqlx.sock
2023-03-22 00:13:14+00:00 [Note] [Entrypoint]: Temporary server started.
'/var/lib/mysql/mysql.sock' -> '/var/run/mysqld/mysqld.sock'
Warning: Unable to load '/usr/share/zoneinfo/iso3166.tab' as time zone. Skipping it.
Warning: Unable to load '/usr/share/zoneinfo/leapseconds' as time zone. Skipping it.
Warning: Unable to load '/usr/share/zoneinfo/tzdata.zi' as time zone. Skipping it.
Warning: Unable to load '/usr/share/zoneinfo/zone.tab' as time zone. Skipping it.
Warning: Unable to load '/usr/share/zoneinfo/zone1970.tab' as time zone. Skipping it.
2023-03-22 00:13:25+00:00 [Note] [Entrypoint]: Creating database myappdb
2023-03-22 00:13:25+00:00 [Note] [Entrypoint]: Creating user myappuser
2023-03-22 00:13:25+00:00 [Note] [Entrypoint]: Giving user myappuser access to schema myappdb

2023-03-22 00:13:25+00:00 [Note] [Entrypoint]: /usr/local/bin/docker-entrypoint.sh: running /docker-entrypoint-initdb.d/init.sql


2023-03-22 00:13:25+00:00 [Note] [Entrypoint]: Stopping temporary server
2023-03-22T00:13:25.870692Z 14 [System] [MY-013172] [Server] Received SHUTDOWN from user root. Shutting down mysqld (Version: 8.0.31).
2023-03-22T00:13:27.808480Z 0 [System] [MY-010910] [Server] /usr/sbin/mysqld: Shutdown complete (mysqld 8.0.31)  MySQL Community Server - GPL.
2023-03-22 00:13:27+00:00 [Note] [Entrypoint]: Temporary server stopped

2023-03-22 00:13:27+00:00 [Note] [Entrypoint]: MySQL init process done. Ready for start up.

2023-03-22T00:13:28.411622Z 0 [Warning] [MY-011068] [Server] The syntax '--skip-host-cache' is deprecated and will be removed in a future release. Please use SET GLOBAL host_cache_size=0 instead.
2023-03-22T00:13:28.414470Z 0 [System] [MY-010116] [Server] /usr/sbin/mysqld (mysqld 8.0.31) starting as process 1
2023-03-22T00:13:28.444884Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
2023-03-22T00:13:28.768330Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
2023-03-22T00:13:29.332398Z 0 [Warning] [MY-010068] [Server] CA certificate ca.pem is self signed.
2023-03-22T00:13:29.332774Z 0 [System] [MY-013602] [Server] Channel mysql_main configured to support TLS. Encrypted connections are now supported for this channel.
2023-03-22T00:13:29.336503Z 0 [Warning] [MY-011810] [Server] Insecure configuration for --pid-file: Location '/var/run/mysqld' in the path is accessible to all OS users. Consider choosing a different directory.
2023-03-22T00:13:29.418835Z 0 [System] [MY-011323] [Server] X Plugin ready for connections. Bind-address: '::' port: 33060, socket: /var/run/mysqld/mysqlx.sock
2023-03-22T00:13:29.419154Z 0 [System] [MY-010931] [Server] /usr/sbin/mysqld: ready for connections. Version: '8.0.31'  socket: '/var/run/mysqld/mysqld.sock'  port: 3306  MySQL Community Server - GPL.

# create a docker compose file for both database and web application
1- nano docker-compose.yml
version: '3'

services:
  web:
    build: .
    ports:
      - "5000:5000"
    depends_on:
      - database

  database:
    build: ./march_22a
    environment:
      MYSQL_ROOT_PASSWORD: my-secret-pw
      MYSQL_DATABASE: myappdb
      MYSQL_USER: myappuser
      MYSQL_PASSWORD: myappw

# user the docker command to build and up the application

1- sudo docker-compose up
WARNING: The Docker Engine you're using is running in swarm mode.

Compose does not use swarm mode to deploy services to multiple nodes in a swarm. All containers will be scheduled on the current node.

To deploy your application across the swarm, use `docker stack deploy`.

Creating network "march_22_default" with the default driver
Creating march_22_database_1 ... done
Creating march_22_web_1      ... done
Attaching to march_22_database_1, march_22_web_1
database_1  | 2023-03-22 00:20:20+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 8.0.31-1.el8 started.
database_1  | 2023-03-22 00:20:21+00:00 [Note] [Entrypoint]: Switching to dedicated user 'mysql'
database_1  | 2023-03-22 00:20:21+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 8.0.31-1.el8 started.
database_1  | 2023-03-22 00:20:22+00:00 [Note] [Entrypoint]: Initializing database files
database_1  | 2023-03-22T00:20:22.469039Z 0 [Warning] [MY-011068] [Server] The syntax '--skip-host-cache' is deprecated and will be removed in a future release. Please use SET GLOBAL host_cache_size=0 instead.
database_1  | 2023-03-22T00:20:22.469411Z 0 [System] [MY-013169] [Server] /usr/sbin/mysqld (mysqld 8.0.31) initializing of server in progress as process 79
database_1  | 2023-03-22T00:20:22.517668Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
database_1  | 2023-03-22T00:20:23.893175Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
web_1       |  * Serving Flask app 'app.py' (lazy loading)
web_1       |  * Environment: production
web_1       |    WARNING: This is a development server. Do not use it in a production deployment.
web_1       |    Use a production WSGI server instead.
web_1       |  * Debug mode: on
web_1       |  * Running on all addresses.
web_1       |    WARNING: This is a development server. Do not use it in a production deployment.
web_1       |  * Running on http://172.28.0.3:5000/ (Press CTRL+C to quit)
web_1       |  * Restarting with stat
web_1       |  * Debugger is active!
web_1       |  * Debugger PIN: 128-849-616
database_1  | 2023-03-22T00:20:27.461999Z 6 [Warning] [MY-010453] [Server] root@localhost is created with an empty password ! Please consider switching off the --initialize-insecure option.
database_1  | 2023-03-22 00:20:34+00:00 [Note] [Entrypoint]: Database files initialized
database_1  | 2023-03-22 00:20:34+00:00 [Note] [Entrypoint]: Starting temporary server
database_1  | 2023-03-22T00:20:35.424347Z 0 [Warning] [MY-011068] [Server] The syntax '--skip-host-cache' is deprecated and will be removed in a future release. Please use SET GLOBAL host_cache_size=0 instead.
database_1  | 2023-03-22T00:20:35.427488Z 0 [System] [MY-010116] [Server] /usr/sbin/mysqld (mysqld 8.0.31) starting as process 128
database_1  | 2023-03-22T00:20:35.477018Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
database_1  | 2023-03-22T00:20:35.868020Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
database_1  | 2023-03-22T00:20:36.682329Z 0 [Warning] [MY-010068] [Server] CA certificate ca.pem is self signed.
database_1  | 2023-03-22T00:20:36.682402Z 0 [System] [MY-013602] [Server] Channel mysql_main configured to support TLS. Encrypted connections are now supported for this channel.
database_1  | 2023-03-22T00:20:36.686086Z 0 [Warning] [MY-011810] [Server] Insecure configuration for --pid-file: Location '/var/run/mysqld' in the path is accessible to all OS users. Consider choosing a different directory.
database_1  | 2023-03-22T00:20:36.743421Z 0 [System] [MY-011323] [Server] X Plugin ready for connections. Socket: /var/run/mysqld/mysqlx.sock
database_1  | 2023-03-22T00:20:36.743467Z 0 [System] [MY-010931] [Server] /usr/sbin/mysqld: ready for connections. Version: '8.0.31'  socket: '/var/run/mysqld/mysqld.sock'  port: 0  MySQL Community Server - GPL.
database_1  | 2023-03-22 00:20:36+00:00 [Note] [Entrypoint]: Temporary server started.
database_1  | '/var/lib/mysql/mysql.sock' -> '/var/run/mysqld/mysqld.sock'
database_1  | Warning: Unable to load '/usr/share/zoneinfo/iso3166.tab' as time zone. Skipping it.
database_1  | Warning: Unable to load '/usr/share/zoneinfo/tzdata.zi' as time zone. Skipping it.
database_1  | Warning: Unable to load '/usr/share/zoneinfo/zone.tab' as time zone. Skipping it.
database_1  | Warning: Unable to load '/usr/share/zoneinfo/zone1970.tab' as time zone. Skipping it.
database_1  | 2023-03-22 00:20:47+00:00 [Note] [Entrypoint]: Creating database myappdb
database_1  | 2023-03-22 00:20:47+00:00 [Note] [Entrypoint]: Creating user myappuser
database_1  | 2023-03-22 00:20:47+00:00 [Note] [Entrypoint]: Giving user myappuser access to schema myappdb
database_1  | 
database_1  | 2023-03-22 00:20:47+00:00 [Note] [Entrypoint]: /usr/local/bin/docker-entrypoint.sh: running /docker-entrypoint-initdb.d/init.sql
database_1  | 
database_1  | 
database_1  | 2023-03-22 00:20:48+00:00 [Note] [Entrypoint]: Stopping temporary server
database_1  | 2023-03-22T00:20:48.145584Z 14 [System] [MY-013172] [Server] Received SHUTDOWN from user root. Shutting down mysqld (Version: 8.0.31).
database_1  | 2023-03-22T00:20:49.701867Z 0 [System] [MY-010910] [Server] /usr/sbin/mysqld: Shutdown complete (mysqld 8.0.31)  MySQL Community Server - GPL.
database_1  | 2023-03-22 00:20:50+00:00 [Note] [Entrypoint]: Temporary server stopped
database_1  | 
database_1  | 2023-03-22 00:20:50+00:00 [Note] [Entrypoint]: MySQL init process done. Ready for start up.
database_1  | 
database_1  | 2023-03-22T00:20:50.657724Z 0 [Warning] [MY-011068] [Server] The syntax '--skip-host-cache' is deprecated and will be removed in a future release. Please use SET GLOBAL host_cache_size=0 instead.
database_1  | 2023-03-22T00:20:50.661410Z 0 [System] [MY-010116] [Server] /usr/sbin/mysqld (mysqld 8.0.31) starting as process 1
database_1  | 2023-03-22T00:20:50.701900Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
database_1  | 2023-03-22T00:20:51.284368Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
database_1  | 2023-03-22T00:20:51.954918Z 0 [Warning] [MY-010068] [Server] CA certificate ca.pem is self signed.
database_1  | 2023-03-22T00:20:51.955139Z 0 [System] [MY-013602] [Server] Channel mysql_main configured to support TLS. Encrypted connections are now supported for this channel.
database_1  | 2023-03-22T00:20:51.958602Z 0 [Warning] [MY-011810] [Server] Insecure configuration for --pid-file: Location '/var/run/mysqld' in the path is accessible to all OS users. Consider choosing a different directory.
database_1  | 2023-03-22T00:20:52.019013Z 0 [System] [MY-011323] [Server] X Plugin ready for connections. Bind-address: '::' port: 33060, socket: /var/run/mysqld/mysqlx.sock
database_1  | 2023-03-22T00:20:52.019476Z 0 [System] [MY-010931] [Server] /usr/sbin/mysqld: ready for connections. Version: '8.0.31'  socket: '/var/run/mysqld/mysqld.sock'  port: 3306  MySQL Community Server - GPL.
^CGracefully stopping... (press Ctrl+C again to force)
Stopping march_22_web_1      ... done
Stopping march_22_database_1 ... done

2- sudo docker-compose down
Removing march_22_web_1      ... done
Removing march_22_database_1 ... done
Removing network march_22_default
  
# modify the docker file for the web and rebuild the web application and redeploy.
1- nano docker-compose.yml
version: '3'

services:
  web:
    build: .
    ports:
      - "5000:5000"
    depends_on:
      - database
    volumes:
      - .:/app
    environment:
      FLASK_DEBUG: "true"

  database:
    build: ./march_22a
    environment:
      MYSQL_ROOT_PASSWORD: my-secret-pw
      MYSQL_DATABASE: myappdb
      MYSQL_USER: myappuser
      MYSQL_PASSWORD: myappw

2- nano app.py 
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello():
    return 'Welcome from musa!\n'

if __name__ == '__main__':

    app.run()

3- sudo docker-compose up
sudo docker-compose up
WARNING: The Docker Engine you're using is running in swarm mode.

Compose does not use swarm mode to deploy services to multiple nodes in a swarm. All containers will be scheduled on the current node.

To deploy your application across the swarm, use `docker stack deploy`.

Creating network "march_22_default" with the default driver
Creating march_22_database_1 ... done
Creating march_22_web_1      ... done
Attaching to march_22_database_1, march_22_web_1
database_1  | 2023-03-22 00:30:31+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 8.0.31-1.el8 started.
database_1  | 2023-03-22 00:30:32+00:00 [Note] [Entrypoint]: Switching to dedicated user 'mysql'
database_1  | 2023-03-22 00:30:32+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 8.0.31-1.el8 started.
database_1  | 2023-03-22 00:30:33+00:00 [Note] [Entrypoint]: Initializing database files
database_1  | 2023-03-22T00:30:33.519807Z 0 [Warning] [MY-011068] [Server] The syntax '--skip-host-cache' is deprecated and will be removed in a future release. Please use SET GLOBAL host_cache_size=0 instead.
database_1  | 2023-03-22T00:30:33.529360Z 0 [System] [MY-013169] [Server] /usr/sbin/mysqld (mysqld 8.0.31) initializing of server in progress as process 78
database_1  | 2023-03-22T00:30:33.561029Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
database_1  | 2023-03-22T00:30:35.096597Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
web_1       |  * Serving Flask app 'app.py' (lazy loading)
web_1       |  * Environment: production
web_1       |    WARNING: This is a development server. Do not use it in a production deployment.
web_1       |    Use a production WSGI server instead.
web_1       |  * Debug mode: on
web_1       |  * Running on all addresses.
web_1       |    WARNING: This is a development server. Do not use it in a production deployment.
web_1       |  * Running on http://172.29.0.3:5000/ (Press CTRL+C to quit)
web_1       |  * Restarting with stat
web_1       |  * Debugger is active!
web_1       |  * Debugger PIN: 660-649-025
database_1  | 2023-03-22T00:30:38.713775Z 6 [Warning] [MY-010453] [Server] root@localhost is created with an empty password ! Please consider switching off the --initialize-insecure option.
database_1  | 2023-03-22 00:30:47+00:00 [Note] [Entrypoint]: Database files initialized
database_1  | 2023-03-22 00:30:47+00:00 [Note] [Entrypoint]: Starting temporary server
database_1  | 2023-03-22T00:30:48.313144Z 0 [Warning] [MY-011068] [Server] The syntax '--skip-host-cache' is deprecated and will be removed in a future release. Please use SET GLOBAL host_cache_size=0 instead.
database_1  | 2023-03-22T00:30:48.316144Z 0 [System] [MY-010116] [Server] /usr/sbin/mysqld (mysqld 8.0.31) starting as process 127
database_1  | 2023-03-22T00:30:48.400944Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
database_1  | 2023-03-22T00:30:49.073143Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
database_1  | 2023-03-22T00:30:50.002289Z 0 [Warning] [MY-010068] [Server] CA certificate ca.pem is self signed.
database_1  | 2023-03-22T00:30:50.002642Z 0 [System] [MY-013602] [Server] Channel mysql_main configured to support TLS. Encrypted connections are now supported for this channel.
database_1  | 2023-03-22T00:30:50.006831Z 0 [Warning] [MY-011810] [Server] Insecure configuration for --pid-file: Location '/var/run/mysqld' in the path is accessible to all OS users. Consider choosing a different directory.
database_1  | 2023-03-22T00:30:50.075846Z 0 [System] [MY-010931] [Server] /usr/sbin/mysqld: ready for connections. Version: '8.0.31'  socket: '/var/run/mysqld/mysqld.sock'  port: 0  MySQL Community Server - GPL.
database_1  | 2023-03-22T00:30:50.076096Z 0 [System] [MY-011323] [Server] X Plugin ready for connections. Socket: /var/run/mysqld/mysqlx.sock
database_1  | 2023-03-22 00:30:50+00:00 [Note] [Entrypoint]: Temporary server started.
database_1  | '/var/lib/mysql/mysql.sock' -> '/var/run/mysqld/mysqld.sock'
database_1  | Warning: Unable to load '/usr/share/zoneinfo/iso3166.tab' as time zone. Skipping it.
database_1  | Warning: Unable to load '/usr/share/zoneinfo/leapseconds' as time zone. Skipping it.
database_1  | Warning: Unable to load '/usr/share/zoneinfo/tzdata.zi' as time zone. Skipping it.
database_1  | Warning: Unable to load '/usr/share/zoneinfo/zone.tab' as time zone. Skipping it.
database_1  | Warning: Unable to load '/usr/share/zoneinfo/zone1970.tab' as time zone. Skipping it.
database_1  | 2023-03-22 00:31:01+00:00 [Note] [Entrypoint]: Creating database myappdb
database_1  | 2023-03-22 00:31:01+00:00 [Note] [Entrypoint]: Creating user myappuser
database_1  | 2023-03-22 00:31:01+00:00 [Note] [Entrypoint]: Giving user myappuser access to schema myappdb
database_1  | 
database_1  | 2023-03-22 00:31:01+00:00 [Note] [Entrypoint]: /usr/local/bin/docker-entrypoint.sh: running /docker-entrypoint-initdb.d/init.sql
database_1  | 
database_1  | 
database_1  | 2023-03-22 00:31:01+00:00 [Note] [Entrypoint]: Stopping temporary server
database_1  | 2023-03-22T00:31:01.866670Z 14 [System] [MY-013172] [Server] Received SHUTDOWN from user root. Shutting down mysqld (Version: 8.0.31).
database_1  | 2023-03-22T00:31:03.941437Z 0 [System] [MY-010910] [Server] /usr/sbin/mysqld: Shutdown complete (mysqld 8.0.31)  MySQL Community Server - GPL.
database_1  | 2023-03-22 00:31:04+00:00 [Note] [Entrypoint]: Temporary server stopped
database_1  | 
database_1  | 2023-03-22 00:31:04+00:00 [Note] [Entrypoint]: MySQL init process done. Ready for start up.
database_1  | 
database_1  | 2023-03-22T00:31:05.336211Z 0 [Warning] [MY-011068] [Server] The syntax '--skip-host-cache' is deprecated and will be removed in a future release. Please use SET GLOBAL host_cache_size=0 instead.
database_1  | 2023-03-22T00:31:05.339927Z 0 [System] [MY-010116] [Server] /usr/sbin/mysqld (mysqld 8.0.31) starting as process 1
database_1  | 2023-03-22T00:31:05.377588Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
database_1  | 2023-03-22T00:31:05.841624Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
database_1  | 2023-03-22T00:31:06.471689Z 0 [Warning] [MY-010068] [Server] CA certificate ca.pem is self signed.
database_1  | 2023-03-22T00:31:06.471847Z 0 [System] [MY-013602] [Server] Channel mysql_main configured to support TLS. Encrypted connections are now supported for this channel.
database_1  | 2023-03-22T00:31:06.474706Z 0 [Warning] [MY-011810] [Server] Insecure configuration for --pid-file: Location '/var/run/mysqld' in the path is accessible to all OS users. Consider choosing a different directory.
database_1  | 2023-03-22T00:31:06.557322Z 0 [System] [MY-011323] [Server] X Plugin ready for connections. Bind-address: '::' port: 33060, socket: /var/run/mysqld/mysqlx.sock
database_1  | 2023-03-22T00:31:06.558013Z 0 [System] [MY-010931] [Server] /usr/sbin/mysqld: ready for connections. Version: '8.0.31'  socket: '/var/run/mysqld/mysqld.sock'  port: 3306  MySQL Community Server - GPL.

4- Modify app.py
web_1       |  * Detected change in '/app/app.py', reloading
web_1       |  * Restarting with stat
web_1       |  * Debugger is active!
web_1       |  * Debugger PIN: 660-649-025

# docker-compose scale
1- sudo docker-compose up -d

2- sudo docker-compose scale web=2

# push the code to github

