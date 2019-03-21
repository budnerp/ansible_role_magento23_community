# Ansible role for Magento 2.3 Community
Ansible role for Magento 2.3 Community

## What's inside?
1. Magento 2.3 Community
    - Frontend: http://magento23ce.local/
    - Backend: http://magento23ce.local/admin (default admin user: `admin`, password: `Admin12`)
2. MySQL database
    - host: vagrant machine's ip
    - port: default (3306)
    - db name: `magento`
    - user: `magento`
    - password: `magento`
3. Use of Redis databases: 0, 1 and 2 (page cache, backed cache, sessions)
4. Sample data (not installed by default)
5. Custom settings as per `defaults/main.yml`
6. Host
    - server name `magento23ce.local` 
        - used for Magento installation
        - set in `/etc/hosts` 
    - docroot: `/var/www/magento23ce.local/html/`
5. Logs located in docroot:
    - var/log/connector.log
    - var/log/debug.log
    - var/log/magento.cron.log
    - var/log/setup.cron.log
    - var/log/system.log
    - var/log/update.cron.log
   
## Tested on
- Virtual Box 6.0.4, Vagrant 2.2.3, Ansible 2.7.6, ContOS 7 build 1812.01

## Installation
1. Navigate to Ansible's roles folder
2. Add the repo to git modules
    ```
    git submodule add https://github.com/budnerp/ansible_role_magento23_community.git ansible_role_magento23_community
    ```
3. Add the role to Ansible's playbook file
    ```    
    roles:
    [...]
        - ansible_role_magento23_community
    [...]
    ```
4. Set a domain in your hosts file (add a line in C:\Windows\System32\drivers\etc\hosts). Refer to Vagrantfile's web.vm.hostname configuration. Example:
    ```
    192.168.33.10 magento23ce.local
    ```
5. Provision your machine
6. SSH onto the machine
    ```
    vagrant ssh
    ```
7. Login to mysql console (see ansible_role_mysql's [README.md](https://github.com/budnerp/ansible_role_mysql/blob/master/README.md))
    ```
    mysql -u root -p
    ```
    or just
    ```
    mysql -u magento -p 
    ```
8. Validate
    - existence of `magento` user
        ```
        SELECT User FROM mysql.user;
        ```
    - privileges
        ```
        SHOW GRANTS FOR magento@localhost;
        SHOW GRANTS FOR magento@webapp;
        SHOW GRANTS FOR magento@192.186.33.10;
        ```
        Expect following and similar:
        ```
        +--------------------------------------------------------------+
        | Grants for magento@localhost                                 |
        +--------------------------------------------------------------+
        | GRANT USAGE ON *.* TO 'magento'@'localhost'                  |
        | GRANT ALL PRIVILEGES ON `magento`.* TO 'magento'@'localhost' |
        +--------------------------------------------------------------+
        ```
9. To verify that Redis and Magento are working together follow instructions from 
[https://devdocs.magento.com/guides/v2.3/config-guide/redis/redis-session.html]()
10. Login to admin panel
11. Change your password 

## Tuning
To increase memory_limit change memory_limit values in 
- `.htaccess` and `pub/.htaccess` (for both php versions)
- `.user.ini` and `pub/.user.ini` 

## PhpStorm configuration
1. Add new datasource - MySQL
2. Provide all mandatory fields on `General` tab:
    - name
    - host: `192.168.33.10` (or `webapp`)
    - port: `3306`
    - database: `magento`
    - user: `magento`
    - password: `magento`
3. Check `Use SSH tunnel` on `SSH/SSL` tab
    - Proxy host: `192.168.33.10` (or `webapp`)
    - Proxy port: 22
    - Proxy user: `vagrant`
    - Auth type: `key pair ...`
    - Private key file `~\.vagrant.d\insecure_private_key`
4. Click `Test connection`, expect green `Successfull` label
   
## Other links
- Magento 2 [https://devdocs.magento.com/#/individual-contributors]()
- Magento 2 - MySQL [https://devdocs.magento.com/guides/v2.3/install-gde/prereq/mysql.html]()
- Magento 2 - Get your authentication keys [https://devdocs.magento.com/guides/v2.3/install-gde/prereq/connect-auth.html]()

## Troubleshooting
####`Unable to unserialize` issues when sample data installed:
```
InvalidArgumentException: Unable to unserialize value, string is corrupted. in /var/www/magento23ce.local/html/vendor/magento/framework/Serialize/Serializer/Serialize.php:38 [...]
```
Try to flush Redis databases:
```
redis-cli flushall
```
Also refer to [https://magento.stackexchange.com/questions/194010/magento-2-2-unable-to-unserialize-value]()
***Warning: issue still to be resolved!***

####Corrupted CSS styles when sample data installed
1. Navigate to Admin panel `Content` > `Configuration` > Store view `Edit` page
2. Expand HTML Head section and update `Scripts and Style Sheets` textarea:
    ```
    <link  rel="stylesheet" type="text/css"  media="all" href="{{MEDIA_URL}}styles.css" />
    ```
    ```
    <link  rel="stylesheet" type="text/css"  media="all" href="pub/media/styles.css" />
    ```
3. Flush cashe if needed

## TO DO
-[ ] add dependencies 
-[x] test production
-[ ] test production mode with sample data
-[ ] get apache user automatically, not hardcoded
-[ ] check performance of Redis: connected through socket/tcp 
-[ ] cronjob writes to system.log all the time 
-[ ] set a umask
-[ ] set smtp
-[ ] set store transactionl emails

## License
Copyright (c) We Are Interactive under the MIT license.