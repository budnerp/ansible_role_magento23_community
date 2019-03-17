# Ansible role for Magento 2.3 Community
Ansible role for Magento 2.3 Community

## What's inside?
1. Magento 2.3 Community installator
    - Frontend: http://webapp/
    - Backend: http://webapp/admin (default admin user: `admin`, password: `Admin12`)
2. MySQL database
    - host: vagrant machine ip
    - port: default (3306)
    - db name: magento
    - user: magento
    - password: magento
3. Custom settings as per `defaults/main.yml`
4. Logs
    - <webroot>/var/log/connector.log
    - <webroot>/var/log/debug.log
    - <webroot>/var/log/magento.cron.log
    - <webroot>/var/log/setup.cron.log
    - <webroot>/var/log/system.log
    - <webroot>/var/log/update.cron.log
   
## Tested on

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
4. Provision your machine
5. SSH onto the machine
    ```
    vagrant ssh
    ```
6. Login to mysql console (see ansible_role_mysql's [README.md](https://github.com/budnerp/ansible_role_mysql/blob/master/README.md))
    ```
    mysql -u root -p
    ```
    or just
    ```
    mysql -u magento -p 
    ```
7. Validate
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
8. To verify that Redis and Magento are working together follow instructions from 
[https://devdocs.magento.com/guides/v2.3/config-guide/redis/redis-session.html]()
9. Login to admin panel
10. Change your password 

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

## TO DO
-[ ] add dependencies 
-[ ] test production mode
-[ ] get apache user automatically, not hardcoded
-[ ] check performane of Redis connected with socket 
-[ ] cronjob writes to system.log all the time 
-[ ] Optionally set a umask
-[ ] set smtp
-[ ] set store transactionl emails

## License
Copyright (c) We Are Interactive under the MIT license.