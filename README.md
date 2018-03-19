# Wordpress Development Setup

**Context**   
macOS 10.13+  
Homebrew 1.5.10+  
PHP 7.2+  
MySQL 5.7+  
Media Temple Grid: Shared Hosting

## Setup
### Production Setup
`ssh yourdomain.com@yourdomain.com`
```shell
cd ~/domains/yourdomain.com/html
git init
cd ~/domains/yourdomain.com/repo
git --bare init
cd ~/domains/yourdomain.com/html
git remote add hub ~/domains/yourdomain.com/repo.git
touch ~/domains/yourdomain.com/html/.git/hooks/post-commit
vim ~/domains/yourdomain.com/html/.git/hooks/post-commit
```
```shell
#!/bin/bash
echo
echo "**** pushing changes to Hub"
echo

git push hub
```
```shell
touch ~/domains/yourdomain.com/repo.git/hoods/post-update
vim ~/domains/yourdomain.com/repo.git/hooks/post-update
```
```shell
#!/bin/bash
echo
echo "**** Deploying master to /html"
echo
cd ~/domains/yourdomain.com/html || exit
unset GIT_DIR
git pull hub master
git update-server-info
```

### Development Setup
`brew install php`  
`brew install mysql`
`git clone ssh://yourdomain.com@yourdomain.com/~/domains/yourdomain.com/repo.git yourdomain.com`

### Database Setup
`mysql -u root`  
```shell
CREATE DATABASE YourWPDatabaseName;
CREATE USER 'new_user'@'localhost' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON YourWPDatabaseName.* TO 'username'@'localhost';
```

### Wordpress Setup
```shell
curl -O https://wordpress.org/latest.zip
unzip latest.zip
mv wordpress/* ./
rm latest.zip
rm -rf wordpress
```
Start php server `php -S localhost:9000`  
Go to http://localhost:9000 to finish setup

## Deploy
Using git hooks setup earlier in this guide, simply pushing repo to origin deploys to production.
`git push`

### Database Dump: Production => Development
```shell
#SSH into server
ssh yourdomain.com@yourdomain.com

#Dump database
mysqldump --add-drop-table -h host -u user -p database_name > database_name.sql

#Download database dump
scp yourdomain.com@yourdomain.com:~/database_name.sql tmp/database_name.sql

#Import database dump
mysql -u longviewihn -p longviewihn < db148038_longviewihn_wp.sql
rm tmp/db148038_longviewihn_wp.sql

#Fix root url
mysql -u longviewihn -p longviewihn
UPDATE wp_options SET option_value = 'http://localhost:9000' WHERE option_name = 'siteurl' OR option_name = 'home';
\q

#Remove server's database dump
ssh yourdomain.com@yourdomain.com
rm database_name.sql
```

### Database Dump: Development => Production
```shell
#Dump database
mysqldump --add-drop-table -u user -p database_name > tmp/database_name.sql

#Upload database dump
scp tmp/database_name.sql yourdomain.com@yourdomain.com:~/database_name.sql

#Import database dump
ssh yourdomain.com@yourdomain.com
mysql -h host -u user -p database_name < database_name.sql

#Fix root url
mysql -h host -u user -p database_name
UPDATE wp_options SET option_value = 'http://yourdomain.com' WHERE option_name = 'siteurl' OR option_name = 'home';
\q

#Remove database dump
rm database_name.sql
exit
```


###### References
http://joemaller.com/990/a-web-focused-git-workflow/  
https://ajfleming.info/git-deployments-on-mediatemple/  
http://www.brodybits.com/wordpress/osx/homebrew/2016/03/09/quickstart-wordpress-testing-with-osx-homebrew.html

