# Yale Related Snippets

## How to Mount a S@Y Share using Ubuntu.

``` bash
## UPDATE APT AND INSTALL CIFS
sudo apt update && sudo apt install cifs-utils -y
```
``` bash
# SET YOUR VARIABLES
SHARE_PATH="http://storage.yale.edu/home/Spinup_Demo-CC0000-ITSHTS"     ## The path to your S@Y share.
MOUNT_DIR="/mnt/share"                                                  ## The location you'd like to mount to.
NET_ID=abc123                                                           ## Your Yale netID
USER_ID=$(id -u $NET_ID)                                                ## The UID of your user.
GROUP_ID=$(id -g $NET_ID)                                               ## The GID of your user.
```
``` bash
sudo mount -t cifs $SHARE_PATH $MOUNT_DIR -o username=$NET_ID,uid=$USER_ID,gid=$GROUP_ID,forceuid,forcegid
```

## Upgrade MySQL 5.1 to MariaDB 10.x on CentOS 6

#### Step 1 - Housekeeping

Backup your database(s) using your preferred method. 
(For this tutorial, we’ll just use mysqldump. Also be sure to make a copy of your configuration file(s).)

```bash
mysqldump --all-databases > /tmp/backup.sql
cp /etc/my.cnf{,.bak}
```

Stop MySQL Service

```bash
service mysql stop
```

Make a copy of the data directory just in case.
```bash
cp -R /var/lib/mysql /tmp/mysql_backup
```
#### Step 2 - Upgrade MYSQL 5.1 to MariaDB 5.5

Configure the MariaDB 5.5 Repository.

```bash
vim /etc/yum.repos.d/MariaDB55.repo
```

Paste the <a href="https://downloads.mariadb.org/mariadb/repositories/">MariaDB</a> 5.5 `yum` repository</a> entry into `/etc/yum.repos.d/MariaDB55.repo`

```bash
# MariaDB 5.5 CentOS repository list - created 2020-10-21 16:47 UTC
# http://downloads.mariadb.org/mariadb/repositories/
[mariadb]
name = MariaDB
baseurl = http://yum.mariadb.org/5.5/centos6-amd64
gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
gpgcheck=1
```

Clean the Repository Cache Information
```bash
yum clean all
```

Remove MySQL 5.1
```bash
yum remove mysql-server
```

Install MariaDB 5.5
```bash
yum install mysql-server
```

Start MySQL Service
```bash
service mysql start
```

Upgrade the Database

```bash
mysql_upgrade
```

Check Upgrade and Verify Databases

```bash
mysql --version
```

```bash
mysql -uroot -p
```

```sql
SHOW DATABASES;
```

#### Step 3 - Upgrade MariaDB 5.5 to MariaDB 10.x

Stop `mysql` Service & Remove MariaDB 5.5

```bash
service stop mysql
yum remove mysql-server mysql-client
```

Disable the MariaDB 5.5 Repo

```bash
mv /etc/yum.repos.d/MariaDB55.repo{,.disabled}
```

Configure the MariaDB 10.x Repository

```bash
vim /etc/yum.repos.d/MariaDB10.repo
```

Paste the <a href="https://downloads.mariadb.org/mariadb/repositories/">MariaDB</a> 10.x `yum` repository</a> entry into `/etc/yum.repos.d/MariaDB55.repo`

```bash
# MariaDB 10.3 CentOS repository list - created 2020-10-21 16:59 UTC
# http://downloads.mariadb.org/mariadb/repositories/
[mariadb]
name = MariaDB
baseurl = http://yum.mariadb.org/10.3/centos6-amd64
gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
gpgcheck=1
```

Clean the Repository Cache Information
```bash
yum clean all
```

Install MariaDB 10.x
```bash
yum install mysql-server mysql-client
```

Start `mysql` Server & Fix `/var/log/mysqld.log`
```bash
touch /var/log/mysqld.log && chown mysql:mysql /var/log/mysqld.log
service mysql start
```

Upgrade the Database

```bash
mysql_upgrade
```

Check Upgrade & Verify Databases

```bash
mysql --version
```

```bash
mysql -uroot -p
```

```sql
SHOW DATABASES;
```
