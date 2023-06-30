
# Installation on MacOS M1

## Ranger Admin

```bash
# Install MySQL in your localhost
$ brew install mysql
$ brew services start mysql

# Clone repository
$ git clone https://github.com/lam1051999/ranger_build_output.git
$ cd ranger_build_output
$ git lfs install
$ cp ranger-2.0.0-admin.tar.gz ~
$ cd ~
$ tar -xvf ranger-2.0.0-admin.tar.gz
$ cd ranger-2.0.0-admin

# Install mysql-connector
$ wget https://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-8.0.26.tar.gz
$ tar -xvf mysql-connector-java-8.0.26.tar.gz
$ mv mysql-connector-java-8.0.26/mysql-connector-java-8.0.26.jar mysql-connector-java.jar

# Login to mysql and create user
> mysql -u root -p
> CREATE USER 'admin'@'localhost' IDENTIFIED BY 'password12';
> GRANT ALL PRIVILEGES ON *.* TO 'admin'@'localhost' WITH GRANT OPTION;
> FLUSH PRIVILEGES;
> CREATE DATABASE ranger;

# Now we are ready to edit Ranger configurations
$ vi install.properties
SQL_CONNECTOR_JAR=~/ranger-2.0.0-admin/mysql-connector-java.jar

db_name=ranger
db_user=admin
db_password=password12

rangerAdmin_password=YourPassword@123456
rangerTagsync_password=YourPassword@123456
rangerUsersync_password=YourPassword@123456
keyadmin_password=YourPassword@123456

audit_solr_urls=http://localhost:6083/solr/ranger_audits

unix_user=<your_mac_user>
unix_user_pwd=<your_mac_user_password>
unix_group=<your_mac_group>

RANGER_PID_DIR_PATH=$PWD/var/run/ranger

# Comment setup user/group because it is currently compatible with Linux
$ vi setup.sh # then comment #setup_unix_user_group

# Install python 2 because setup scripts run python2
$ pyenv install 2.7.18
$ pyenv local 2.7.18

# After updating the required properties, run setup.sh
$ ./setup.sh

# Once above installation is successful, we are ready to start ranger admin service
$ ews/ranger-admin-services.sh start

# Stop ranger admin
$ ews/ranger-admin-services.sh stop
```

## Solr for audits
```bash
$ cd ~/ranger-2.0.0-admin/contrib/solr_for_audit_setup

# Change config
$ vi install.properties
SOLR_USER=<your_mac_user>
SOLR_GROUP=<your_mac_group>

SOLR_INSTALL=true

JAVA_HOME=$JAVA_HOME
SOLR_DOWNLOAD_URL=http://archive.apache.org/dist/lucene/solr/5.2.1/solr-5.2.1.tgz

SOLR_INSTALL_FOLDER=$PWD/data/solr
SOLR_RANGER_HOME=$PWD/data/solr/ranger_audit_server
SOLR_RANGER_DATA_FOLDER=$PWD/data/solr/ranger_audit_server/data
SOLR_LOG_FOLDER=$PWD/var/log/solr/ranger_audits

# Setup scripts run python2
$ pyenv local 2.7.18

# After updating the required properties, run setup.sh
$ ./setup.sh

# Start Solr
$ ~/ranger-2.0.0-admin/contrib/solr_for_audit_setup/data/solr/ranger_audit_server/start_solr.sh

# Stop Solr
$ ~/ranger-2.0.0-admin/contrib/solr_for_audit_setup/data/solr/ranger_audit_server/stop_solr.sh
```