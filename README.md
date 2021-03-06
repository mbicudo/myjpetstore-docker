# MyJPetStore

Based on centos7 & iBatis JPetStore

## Build and Run

Clone this repository

  ```
  $ git clone https://github.com/mbicudo/myjpetstore-docker.git
  ```

Add the tgz for Apache Tomcat and JDK of your choice, and edit Dockerfile accordingly.
The versions that this image has been tested are:

  ```
   $ ls -1 deps/
   apache-tomcat-9.0.12.tar.gz
   jdk-8u40-linux-x64.tar.gz
  ```

Build Docker Image

  ```
  $ cd myjpetstore-docker
  $ docker build -t myjpetstore .
  ```

Run Docker Image

  ```
  $ docker run \
        -p 8080:8080 \
        --name mypetstore \
        -e MYPET_DBHOST=db \
        -e MYPET_DBPORT=3306 \
        -e MYPET_DATABASE=jpetstore \
        -e MYPET_USERNAME=jpetstore \
        -e MYPET_PASSWD=jpetstore \
        myjpetstore
  ```

### Dependencies

JPetStore requires an external DB. By default the driver is mysql. See jdbc.properties below:

  ```
  jdbc.driverClassName=com.mysql.jdbc.Driver
  jdbc.url=jdbc:mysql://${MYPET_DBHOST}:${MYPET_DBPORT}/${MYPET_DATABASE}
  jdbc.username=${MYPET_USERNAME}
  jdbc.password=${MYPET_PASSWD}
  ```

## MyJPetStore MySQL

For more info on how to set up a mysql docker image and import jpetstore.sql initial db scheme.

### Run MySQL Container

This will run the mysqld in the container, create the database 'jpetstore, grant all the permissions for user/pwd 'jpetstore/jpetstore' and tell mysqld to store that into /var/lib/mysql/, which is mapped to the directory in the docker host '/opt/mysql-datadir. This allows you to have persistent data even if the mysql container dies.

  ```
  $ docker run -d \
        -p 3306:3306 \
        --name myjpetstore-db \
        -e MYSQL_DATABASE=jpetstore \
        -e MYSQL_USER=jpetstore \
        -e MYSQL_PASSWORD=jpetstore \
        -e MYSQL_ROOT_PASSWORD=myrootpwd
        -v /opt/mysql-datadir:/var/lib/mysql \
        mysql:5.5
  ```

### Initial Setup

If this is the first time that you run your mysql docker for the JPetStore app, then you will have to create the tables.
To do this, run a mysql cmd and stdin the dump .sql file:
  ```
  $ docker exec -i myjpetstore-db mysql -u jpetstore -pjpetstore -D jpetstore < JPetStore/jpetstore.sql
  ```

### Troubleshoot

Exec bash to get into the container and do debugging:

  ```
  $ docker exec -it myjpetstore-mysql bash
  ```
