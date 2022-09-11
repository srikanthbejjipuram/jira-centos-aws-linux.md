# JIRA Installation on Centos/AWS linux OS:

## Prerequisits:
1. Java ver 8
2. a database (if using for prod env)

## Java Configuration
### Install:
1. Search for available package: `yum search java | grep 'java-'`
2. Select package to install: `yum install java-1.8.0-openjdk*`  for example
3. Check: `java -version`
### Configuring JAVA_HOME:
1. Change to root user: `sudo su`
2. Locate which java is being used: `update-alternatives --config java`
    (select your option or just hit enter. This is also to check the location of java installation)
3. Set JAVA_HOME: `export JAVA_HOME=/path/to/jdk`
4. Add JAVA_HOME to path variable: `export PATH=$PATH:$JAVA_HOME`

## Database set-up:
### MySQL:
1. Check if mysql is already installed `rpm -qa | grep mysql`
if not proceed further:
2. Download and add the repository: `wget https://repo.mysql.com/mysql80-community-release-el7-1.noarch.rpm`
3. Install the package: `yum localinstall mysql80-community-release-el7-1.noarch.rpm`
4. Verify if the yum repository has been added successfully: `yum repolist enabled | grep "mysql.*-community.*"`
5. Installing latest MYSQL: `yum install mysql-community-server`
6. **Starting mySql server**: `service mysqld start`
7. Verify the mySql server status: `service mysqld status`
8. Verify the installed mySql version: `mysql --version`
9. **Securing the mySql installation**:
   The command `mysql_secure_installation` allows to secure your MySQL installation by performing important settings like setting the root password, removing anonymous users, removing root login, and so on.

   **Note**: MySQL version 8.0 or higher generates a temporary random password in /var/log/mysqld.log after installation.
   
   * `grep 'temporary password' /var/log/mysqld.log` don't forget to copy the temp password.
   * `mysql_secure_installation`
   
   Sample Output:
   ```
   Securing the MySQL server deployment.
   
   Enter password for user root: Enter New Root Password
   
   VALIDATE PASSWORD PLUGIN can be used to test passwords 
   and improve security. It checks the strength of password
   and allows the users to set only those passwords which are
   secure enough. Would you like to setup VALIDATE PASSWORD plugin?
   
   Press y|Y for Yes, any other key for No: y
   
   There are three levels of password validation policy:
   
   LOW    Length >= 8
   MEDIUM Length >= 8, numeric, mixed case, and special characters
   STRONG Length >= 8, numeric, mixed case, special characters and dictionary   
   
   Please enter 0 = LOW, 1 = MEDIUM and 2 = STRONG: 2
   Using existing password for root.
   
   Estimated strength of the password: 50 
   Change the password for root ? ((Press y|Y for Yes, any other key for No) : y
   
   New password: Set New MySQL Password
   
   Re-enter new password: Re-enter New MySQL Password
   
   Estimated strength of the password: 100 
   Do you wish to continue with the password provided?(Press y|Y for Yes, any other key for No) : y
   By default, a MySQL installation has an anonymous user,
   allowing anyone to log into MySQL without having to have
   a user account created for them. This is intended only for
   testing, and to make the installation go a bit smoother.
   You should remove them before moving into a production
   environment.
   
   Remove anonymous users? (Press y|Y for Yes, any other key for No) : y
   
   Success.
   
   
   Normally, root should only be allowed to connect from
   'localhost'. This ensures that someone cannot guess at
   the root password from the network.
   
   Disallow root login remotely? (Press y|Y for Yes, any other key for No) : y
   Success.
   
   By default, MySQL comes with a database named 'test' that
   anyone can access. This is also intended only for testing,
   and should be removed before moving into a production
   environment.
   
   Remove test database and access to it? (Press y|Y for Yes, any other key for No) : y
   - Dropping test database...
   Success.
   
   - Removing privileges on test database...
   Success.
   
   Reloading the privilege tables will ensure that all changes
   made so far will take effect immediately.
   
   Reload privilege tables now? (Press y|Y for Yes, any other key for No) : y
   Success.
   
   All done! 
   ```
   
10. Updating mySQL with yum: `yum update mysql-server` (optional for updates).
11. Connecting to mySQL server: `mysql -u root -p`
12. Creating a database "jira-db": `CREATE DATABASE jira_db CHARACTER SET utf8 COLLATE utf8_bin;`
13. Creating a database user with password: `CREATE USER 'jira'@'localhost' IDENTIFIED BY 'yourPassword';`
14. Granting priviledges: `GRANT ALL PRIVILEGES ON * . * TO 'jira'@'localhost';`
15. Use `flush privileges;` in order to make `GRANT` effective.
16. Restart mySQL" `restart mysql`

## Jira
### Installation:
1. Download [jira](https://www.atlassian.com/software/jira/download?_ga=2.213851348.1358305773.1556954742-1434227542.1556954742). Download `.bin` file. e.g. `atlassian-jira-software-8.1.0-x64.bin`. 
2. To check your operating system bits, type `uname -m`
3. Make your file executable. In our case `chmod a+x atlassian-jira-software-8.1.0-x64.bin`
4. Change to root user: `sudo su`
5. Run the file: `./atlassian-jira-software-8.1.0-x64.bin`
6. If getting error `Could not display GUI. This application needs access to an X server`, use `yum install dejavu-sans-fonts` and restart from step 4.
7. Follow along the further steps, but do not start the jira server now.
8. Download mySQL connector from [here](https://dev.mysql.com/downloads/connector/j/) tar.gz file/platform independent one. e.g. `mysql-connector-java-8.0.16.tar.gz`.
9. Extract the file `tar -xvf mysql-connector-java-8.0.16.tar.gz`, cd to `mysql-connector-java-8.0.16` and copy the `jar` file `mysql-connector-java-8.0.16.jar` to `jira-install-directory/lib`; e.g. `cp mysql-connector-java-8.0.16.jar /opt/atlassian/jira/lib`
10. Now start our jira server: 
    * `cd /opt/atlassian/jira/bin/`
    * Run the script: `./startup.sh`
    * Now go to the `url:port` no into the browser. e.g. `localhost:8080`

### Application Setup:
1. Choose the following:
    * I'll set it up myself
    * My own databse.
    * Database Type: _MY SQL_
    * Hostname: type `hostname` on your terminal to get hostname
    * Port no: _leave the default value_
    * Database/Username/Password will be the same as created in [database set-up](https://gist.github.com/nicks9188/45eeef3980de48e1bf654c68fffb7999#database-set-up)
    * Test Connection
    * Next
2. follow along.

- - - -
**NOTES:**
* Defaults:
    * Install directory: `/opt/atlassian/jira`
    * Software data/Home directory: `/var/atlassian/application-data/jira`
    * Connection/http port: 8080
    * Control port: 8005
    * Backup: `/var/atlassian/application-data/jira/export/`
* Opening firewall from termianl:
    * sudo firewall-cmd --zone=public --add-port=8080/tcp --permanent
    * sudo firewall-cmd --reload
* AWS or GCloud or Azure: don't forget to open port via _Network & Security_
