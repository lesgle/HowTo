# Build Wordpress website on Azure VM

Basic overview how to build a wordpress web site on Microsoft Azure for non production usage, personal blog etc.

## Requirements

Azure VM - Standard B1s (1 vcpus, 1 GB memory)  
Root credentials  
Enable swap inside the VM. Otherwise docker MySQL instance will eat all RAM and system will become very slow  
Installed docker

## Build procedure

Run MySQL container

```text
docker run --name mysql -e MYSQL_ROOT_PASSWORD='mysqlrootpassword' -d mysql --default-authentication-plugin=mysql_native_password
```

Then inside MySQL container, create a database

```text
docker exec -it mysql bash
mysql -u root -p
CREATE DATABASE wordpress_db;
CREATE USER 'wordpress_user' IDENTIFIED BY 'password';
GRANT ALL ON wordpress_db.* TO 'wordpress_user';
FLUSH PRIVILEGES;
quit;
```

Final step, run a wordpress container

```text
docker run --name wordpress --link mysql:mysql -e WORDPRESS_DB_USER='wordpress_user' -e WORDPRESS_DB_PASSWORD='password' -e WORDPRESS_DB_NAME='wordpress_db' -p 80:80 -d wordpress
```