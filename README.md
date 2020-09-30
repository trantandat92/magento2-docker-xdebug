# magento2-docker-xdebug

- php 7.3-fpm
- nginx lasted
- elastic search 7.4.2 
- mysql
- redis
- mail-server
- adminer

# To fix error _max virtual memory areas vm.max_map_count 65530 is too low, increase to at least 262144 elastic search
https://www.elastic.co/guide/en/elasticsearch/reference/7.x/docker.html#_set_vm_max_map_count_to_at_least_262144

Docker Desktop WSL 2 backend: running command line in power shell

# To debug - Setting in PHP Storm:
- File | Settings | Languages & Frameworks | PHP | Debug | DBGp Proxy | Port : 9000
- File | Settings | Languages & Frameworks | PHP | Debug | Debug Port: 9001
- File | Settings | Languages & Frameworks | PHP | Servers  
  Create New Server with: Name: Docker, Host: localhost, Port: 83, Debugger: Xdebug
  Path mappings: 
    File/Directory: path/to/project 
    Absolute path on the server: /var/www/html/magento
- Run | Debug Configurations
  Add PHP Remote Debug Server
  IDE Key: PHPSTORM
# To debug - Update ~dev\docker\php\php-custom.ini
- Set xdebug.remote_host=your_ip

# To install magento 2
- Clone or download this repo, extract to project folder
- Docker Up and running:
<pre>
cd ~/Project/magento2-test  
docker-compose up -d  
</pre>
- Magento Install:

<pre>
docker-compose exec php bash  

cd /var/www/html/magento

# Setup file permissions, except folder `dev/docker`
find var generated vendor pub/static pub/media app/etc -type f -exec chmod g+w {} + \
    && find var generated vendor pub/static pub/media app/etc -type d -exec chmod g+ws {} + \
    && chown -R :www-data $(ls -Idev/docker)

# Run this to install db if it is new installation,
# or you can import your SQL in adminer: http://localhost:83
php bin/magento setup:install \
    --db-host=mysql \
    --db-name=magento_db \
    --db-user=magento \
    --db-password=secret \
    --backend-frontname=admin_mn \
    --admin-firstname=Super \
    --admin-lastname=Admin \
    --admin-email=admin@example.com \
    --admin-user=admin \
    --admin-password=s3cret@123
  
# Flush cache
php bin/magento cache:flush
# Update module
php bin/magento set:upgrade
# deploy static file
php bin/magento setup:static-content:deploy -f
</pre>

# Updating....
