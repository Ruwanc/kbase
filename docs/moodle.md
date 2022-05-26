#Moodle
##Upgrade moodle from 3.1 to 3.11

Moodle 3.1 system 
MySql 5.5.68
Php 5.4.16
Apache 2.4.6
OS: Centos7

We can not directly update moodle from 3.1 version to 3.11 version. First we have to get moodle into intermediate version (moodle version 3.6) and then we can upgrade it to moodle version 3.11.
###Upgrade to moodle 3.6

- https://docs.moodle.org/36/en/Upgrading#Moodle_3.1.2C_3.2.2C_3.3.2C_3.4_and_3.5_improvements
- https://docs.moodle.org/31/en/Upgrading

Before upgrading, check that your server meets all requirements for 3.6 in Site administration > Server > Environment.

Put moodle in maintenance mood.

    ```
    Site administration > server > Maintenance Mode
    ```
Set upgrade key in config.php file. (Optional)

    ```
    $CFG->upgradekey = ‘Put upgrade key here’; Also take a copy/screen shot of config.php file)
    ```

    - https://docs.moodle.org/36/en/Upgrade_key

Backup important data.

    - https://docs.moodle.org/36/en/Site_backup

Three parts needed to be backed-up.

1. The data stored in the database (For example, a MySQL database)
2. The uploaded files (For example, site and course files uploaded via Moodle located in moodledata)
3. The Moodle code (For example, everything in server/htdocs/moodle)

#####Backup MySql database
(Using mysqldump Tool)
Create a directory as /opt/backup/sqlfiles/
Goto above directory. Cd /opt/backup/sqlfiles/
Run mysqldump tool.
```
Sudo mysqldump -u root --password=PUT_PASSWORD_HERE -C -Q -e --create-options DB_NAME_HERE > moodle-database.sql
```

[ If above not worked

```
Sudo mysqldump --single-transaction -u root --password=PUT_PASSWORD_HERE -C -Q -e --create-options DB_NAME_HERE > moodle-database.sql
```
]

```
gzip moodle-database.sql
```

To check the file size use 

```
du -sh <directry_name>
```

#####Backup Moodledata directory
Backup using Filezilla

#####Backup Moodle Code
Backup using Filezilla

https://www.educative.io/edpresso/how-to-gzip-a-directory-in-linux

####Converting InnoDB tables to Barracuda
https://docs.moodle.org/311/en/Administration_via_command_line

Sites using MySQL with database tables using Antelope as the file format are recommended to convert the tables to the Barracuda file format. 

This can be achieve running the mysql_compressed_rows.php script in moodle/admin/cli directory.

It should be run as 
```
sudo -u apache /usr/bin/php admin/cli/php_script_name
```
    . (user should change working directory to www/http/moodle before run the scripts) 
To get help run below command.
```
sudo -u apache /usr/bin/php admin/cli/php_script_name --help
```

```
sudo -u apache /usr/bin/php admin/cli/mysql_compressed_rows.php --list
```

This will view the tables requiring conversion.

To proceed with the conversion, run the command using the fix option: 

```
sudo -u apache /usr/bin/php admin/cli/mysql_compressed_rows.php --fix
```

Successful table conversion will be reported in the output.

If the fix command not worked and an error like below arises, follow the below steps.
Error:

[show warnings; to see the mysql warnings]

Cannot enable GLOBAL innodb_file_per_table setting, use --showsql option and execute the statements manually. !!! Error writing to database !!!

```
sudo -u apache /usr/bin/php admin/cli/mysql_compressed_rows.php --help
sudo -u apache /usr/bin/php admin/cli/mysql_compressed_rows.php --showsql
```

(--showsql flag will show the sql queries which need to be run manually)

Run the required sql statements with root privileges.

####Converting to the new character set and collation [do this with moodle 3.6]
Run the below command as above.

```
sudo -u apache /usr/bin/php admin/cli/mysql_collation.php --collation=utf8mb4_unicode_ci
```

Adjust the $CFG->dboptions Array in your config.php to make sure that Moodle uses the right Collation when connecting to the MySQL Server:

```
$CFG->dboptions = array(
  …
  'dbcollation' => 'utf8mb4_unicode_ci',
  …
);
```

If the fix command not worked and an error like below arise, follow the below steps.

Error: ‘utf8mb4_unicode_ci’ requires the setting ‘innodb_large_prefix’ be set to ‘ON’.
An attempt was made to change the format, but it failed. Please try doing this manually.

Run the below sql statement with root privileges.

```
SET GLOBAL innodb_large_prefix = 'on'
```

Now run the 

```
sudo -u apache /usr/bin/php admin/cli/mysql_collation.php --collation=utf8mb4_unicode_ci
```

command again.

For more info [https://docs.moodle.org/31/en/MySQL_full_unicode_support](https://docs.moodle.org/31/en/MySQL_full_unicode_support)

*****
Get a backup of sql moodle db using below command.

```
Sudo mysqldump -u root --password=PUT_PASSWORD_HERE -default-character-set=utf8mb4 -C -Q -e --create-options DB_NAME_HERE > moodle-database.sql
```

[https://webmasters.stackexchange.com/questions/100648/character-set-utf-8-is-not-a-compiled-character-set-and-is-not-specified-in-th](https://webmasters.stackexchange.com/questions/100648/character-set-utf-8-is-not-a-compiled-character-set-and-is-not-specified-in-th)

*****

####Update Mariadb version to 5.6 (/10.2)

Stop mariadb service    


```
sudo systemctl stop mariadb
```

List all mariadb rpm packages    

```
rpm -qa | grep mariadb
```

Remove MariaDB 5.5.x packages:    

```
sudo yum remove mariadb mariadb-server mariadb-libs
```

Add a new MariaDB repository:

```
vi /etc/yum.repos.d/mariadb.repo
```

```
[mariadb]
name = MariaDB
baseurl = http://yum.mariadb.org/10.2/centos7-amd64
gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
gpgcheck=1
```

Clean the repository cache on your server with:

```
yum clean all
```

####Install MariaDB 10.2

```
sudo yum install MariaDB-client MariaDB-server
```

Please note, you need to install Postfix and php-mysql packages if you used them prior this upgrade, so run:

```
sudo yum install postfix php-mysql
```

Enable MariaDB service to automatically start on server boot:

```
systemctl enable mariadb
```

Start the MariaDB service:

```
systemctl start mariadb
```

Run the mysql_upgrade command-line command to examine all tables in all MariaDB databases for incompatibilities with the currently installed version of the MariaDB server:

```
sudo mysql_upgrade -u root -p
```

```
sudo systemctl restart httpd
```

[https://linuxhostsupport.com/blog/how-to-upgrade-mariadb-on-centos-7/
](https://linuxhostsupport.com/blog/how-to-upgrade-mariadb-on-centos-7/)


####Upgrade PHP version to 7.2

```
php -v
```

Output:

```
PHP 5.4.16 (cli) (built: Nov 1 2019 16:04:20) 
Copyright (c) 1997-2013 The PHP Group
Zend Engine v2.4.0, Copyright (c) 1998-2013 Zend Technologies
```

Turn on Remi repo:

```
sudo yum install http://rpms.remirepo.net/enterprise/remi-release-7.rpm
```

Install yum-utils packages:

```
sudo yum install yum-utils
```

The yum-utils package includes yum-config-manager, which you can use to enable Remi repo as the default repository for installing different PHP versions.

```
sudo yum-config-manager --enable remi-php72
sudo yum update
php -v
Systemctl restart httpd
```

(At this point moodle will not work)

####Install the new Moodle software
Rename old moodle software folder in /var/www/html/ folder to moodle.old3_6 using below command

```
sudo mv moodle moodle.old3_6
```

Create folder(moodle_3_6)  in /opt to download new moodle3.6 software.
You can fetch the current version of the software through

```
wget http://sourceforge.net/projects/moodle/files/Moodle/stable36/moodle-latest-36.tgz
```

You may need to install the wget tool using the sudo yum install wget command.

This will download moodle-latest-36.tgz tar file.

To unpack this file use the below command.

```
sudo tar zxvf moodle-latest-36.tgz
```

[https://www.cyberciti.biz/faq/unpack-tgz-linux-command-line/](https://www.cyberciti.biz/faq/unpack-tgz-linux-command-line/)

This will create a moodle file containing new moodle 3.6 software.
Copy this folder to /var/www/html/ directory using 

```
sudo cp -R moodle /var/www/html/ command.
```

Copy your old config.php file back to the new Moodle directory.

```
Sudo cp config.php /var/www/html/moodle/ (To run this command you need to be inside of the old moodle directory)
```

Don't forget to make moodle/config.php (and the rest of the source code) readable by your www server. For maximum security the files should not be writeable by your server. This is especially important on a 'production' server open to the public internet.

```
chown -R root:root moodle (Linux debian - or even create a user especially for moodle. Don't use the web server user, e.g. www-data)
chmod -R 755 moodle  (In above both commands, moodle is the folder name which is located inside the web root directory.)
```

Now visit  [http://moodle_server_ip/moodle]http://moodle_server_ip/moodle

To install zip extension package

[https://www.programmersought.com/article/94124969653/]https://www.programmersought.com/article/94124969653/

[https://stackoverflow.com/questions/49583881/how-can-i-install-ziparchive-on-php-7-2-with-centos-7](https://stackoverflow.com/questions/49583881/how-can-i-install-ziparchive-on-php-7-2-with-centos-7)

```
sudo yum install php-pecl-zip
sudo systemctl restart httpd
PHP setting-> opcache.enable
Use the below command to install opcache extension.
sudo yum install php-pecl-zendopcache
sudo systemctl restart httpd
```

Converting to the new character set and collation
[This section NOT Worked]


Upgrade to Moodle 3.11
Follow update guide
Tack backups of moodle db, moodledata and moodle (3.6) code

[https://docs.moodle.org/311/en/Upgrading](https://docs.moodle.org/311/en/Upgrading)

Update php 7.2 to php 7.3

First disable remi-php72

```
sudo yum-config-manager --disable remi-php54
```

Enable remi-php74

```
sudo yum-config-manager --enable remi-php74
Sudo yum update
```

Restart apache and mariadb

Refresh and check if the moodle environment requirements are all set.

Download new moodle software.

```
wget http://sourceforge.net/projects/moodle/files/Moodle/stable311/moodle-latest-311.tgz
This will download moodle-latest-311.tgz tar file.
```

To unpack this file use the below command.

```
sudo tar zxvf moodle-latest-311.tgz
```

Rename old moodle software folder in /var/www/html/ folder to moodle.old3_6 using below command

```
Sudo mv moodle moodle.old3_6
```

This will create a moodle file containing new moodle 3.6 software.
Copy this folder to /var/www/html/ directory using 

```
sudo cp -R moodle /var/www/html/ command.
```

Copy your old config.php file back to the new Moodle directory.

```
chown -R root:root moodle (Linux debian - or even create a user especially for moodle. Don't use the web server user, e.g. www-data)
chmod -R 755 moodle
```

--- For mysql ---

[https://plenium.wordpress.com/2018/07/01/upgrade-mysql-5-6-to-5-7-on-centos-7/](https://plenium.wordpress.com/2018/07/01/upgrade-mysql-5-6-to-5-7-on-centos-7/)

[https://www.linuxhelp.com/questions/mysqldump-couldn-t-execute-show-variables-like-gtid-mode-table-performance-schema-session-variables-doesn-t-exist-1146](https://www.linuxhelp.com/questions/mysqldump-couldn-t-execute-show-variables-like-gtid-mode-table-performance-schema-session-variables-doesn-t-exist-1146)


--File copy--

[https://askubuntu.com/questions/86822/how-can-i-copy-the-contents-of-a-folder-to-another-folder-in-a-different-directo](https://askubuntu.com/questions/86822/how-can-i-copy-the-contents-of-a-folder-to-another-folder-in-a-different-directo)
