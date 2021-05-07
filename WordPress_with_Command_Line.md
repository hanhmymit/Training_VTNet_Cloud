# WordPress with Command Line!
Hello everyone, here are the steps for me to run the WordPress manually instead of using docker-comp. Hope you can contribute to guide my way, I can complete better.

**Step 1: Install Docker**
1. First, download and add the GPG key with the following command:  
```$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -```
2. Add the Docker repository with the following command:  
```$ add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"```
3. Once the repository is added, you can install the Docker and Docker Compose using the following command:  
```$ apt-get install docker-ce docker-ce-cli containerd.io  -y```
4. After installing both packages, check the installed version of Docker with the following command:  
```$ docker --version```

**Step 1: Create a MariaDB Containerr**
1. download the WordPress image from the Docker repository using the following command:  
```# docker pull mariadb```  

Output:
```root@myhanh:~# docker pull mariadb
Using default tag: latest
latest: Pulling from library/mariadb
345e3491a907: Pull complete
57671312ef6f: Pull complete
5e9250ddb7d0: Pull complete
2d512e2ff778: Pull complete
57c1a7dc2af9: Pull complete
5f1da40ab8a2: Pull complete
5d5cfc668726: Pull complete
deb86f297614: Pull complete
6861f77e80d5: Pull complete
81a5dea1514d: Pull complete
e3b5cf124c10: Pull complete
3352c2c9d21c: Pull complete
Digest: sha256:36288c675a192bd0a8a99cd6ba0780e31df85f0bfd0cbb204837cd108be3d236
Status: Downloaded newer image for mariadb:latest
docker.io/library/mariadb:latest
```
2. create a directory structure for WordPress on your server:
```mkdir ~/wordpress
mkdir -p ~/wordpress/database
mkdir -p ~/wordpress/html
```
3. create a MariaDB container with name wordpressdb by running the following command  
```docker run -e MYSQL_ROOT_PASSWORD=root-password -e MYSQL_USER=wpuser -e MYSQL_PASSWORD=password -e MYSQL_DATABASE=wpdb -v /root/wordpress/database:/var/lib/mysql --name wordpressdb -d mariadb```
Output  
```e8c780b34cdcb66db9278635b109debb1775d6a6b6785c4e74c8e0815e3ba5e3```
4. check the IP address of MariaDB container with the following command  
```docker inspect -f '{{ .NetworkSettings.IPAddress }}' wordpressdb```  
Output  
```172.17.0.2
```
5. connect to your MariaDB container using the database user and password    
```mysql -u wpuser -h 172.17.0.2 -p
Enter password:
```
Output:  
```Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 3
Server version: 10.5.9-MariaDB-1:10.5.9+maria~focal mariadb.org binary distribution

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
```
```> show databases;
```
Output  
```+--------------------+
| Database           |
+--------------------+
| information_schema |
| wpdb               |
+--------------------+
2 rows in set (0.00 sec)
```
```exit;```
