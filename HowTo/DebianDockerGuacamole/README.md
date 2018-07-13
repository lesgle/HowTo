# Debian Stretch, Docker and Guacamole

## Install Docker and dependencies

```bash
apt-get install apt-transport-https ca-certificates curl gnupg2 software-properties-common zip -y
curl -fsSL https://download.docker.com/linux/debian/gpg | apt-key add -
add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/debian $(lsb_release -cs) stable"
apt-get update
apt-get install docker-ce -y
```

## Download Guacamole and MySQL containers

```bash
docker pull guacamole/guacd
docker pull guacamole/guacamole
docker pull mysql
```

## Start MySQL container. Change rootpassword to your own

```bash
docker run --name mysql -e MYSQL_ROOT_PASSWORD=rootpassword -d mysql
```

## Prepare database initialization script

```bash
docker run --rm guacamole/guacamole /opt/guacamole/bin/initdb.sh --mysql > initdb.sql
docker cp initdb.sql mysql:/
```

## Connect to MySQL container and create a database. Change userpassword to your own

```bash
docker exec -it mysql bash
mysql -u root -p
CREATE DATABASE guacamole_db;
CREATE USER 'guacamole_user' IDENTIFIED BY 'userpassword';
GRANT SELECT,INSERT,UPDATE,DELETE ON guacamole_db.* TO 'guacamole_user';
FLUSH PRIVILEGES;
quit;
cat initdb.sql | mysql -u root -p guacamole_db
exit
```

## Start guacd and guacamole containers

```bash
docker run --name guacd -d guacamole/guacd
docker run --name guacamole --link guacd:guacd --link mysql:mysql -e MYSQL_DATABASE=guacamole_db -e MYSQL_USER=guacamole_user -e MYSQL_PASSWORD=userpassword -d -p 8080:8080 guacamole/guacamole
```

## Fix blank start page

```bash
wget https://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-5.1.46.zip
unzip mysql-connector-java-5.1.46.zip
cd mysql-connector-java-5.1.46/
docker cp mysql-connector-java-5.1.46.jar guacamole:/root/.guacamole/lib/
docker cp mysql-connector-java-5.1.46.jar guacamole:/opt/guacamole/mysql/
docker restart guacamole
```

## Now you are ready to connect to your Guacamole instance at http://SERVERIP:8080/guacamole/ with guacadmin/guacadmin credentials

## Update containers so they start automatically after reboot

```bash
docker update --restart=always mysql
docker update --restart=always guacd
docker update --restart=always guacamole
```

## Docker CPU usage limit

Example below will limit mysql container to use not more than half of CPU core

```bash
docker update --cpus 0.5 mysql

```

The settings can be checked by command below

```bash
docker exec mysql cat /sys/fs/cgroup/cpu/cpu.cfs_quota_us
```