# -*- mode: ruby -*-
# vi: set ft=ruby :

# Use this Vagrant configuration file for local installation of the Oro application.
# Please, refer to the Oro Applications installation guides for the detailed instructions:
# https://oroinc.com/b2b-ecommerce/doc/current/install-upgrade/one-step-automated-installation/vagrant

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "centos/7"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  config.vm.network "forwarded_port", guest: 80, host: 8005

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/home/vagrant/www"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  #
   config.vm.provider "virtualbox" do |vb|
     # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  
     # Customize the amount of memory on the VM:
     vb.memory = 2048
     vb.cpus = 2
   end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
   config.vm.provision "shell", inline: <<-SHELL

		echo "\n*****************************************************"
		echo "************* Provision process started *************"
		echo "*****************************************************\n"

		# --------------------- Provision configuration ---------------------
		
		# --- VM settings ---
		
		FORWARDED_PORT=8005
		
		# --- Database settings ---

		DB_USER="dbuser"
		DB_PASSWORD="DBP@ssword123"
		DB_NAME="oro"
		
		echo "$DB_USER" > /tmp/oro_install_DB_USER
		echo "$DB_PASSWORD" > /tmp/oro_install_DB_PASSWORD
		echo "$DB_NAME" > /tmp/oro_install_DB_NAME

		# --- RabbitMQ settings ---
		
		RABBITMQ_USER="rbmquser"
		RABBITMQ_PASSWORD="rbmqpassword"
		
		echo "$RABBITMQ_USER" > /tmp/oro_install_RABBITMQ_USER
		echo "$RABBITMQ_PASSWORD" > /tmp/oro_install_RABBITMQ_PASSWORD

		# --- Oro application settings ---

		APP_HOST="localhost"
		APP_USER="admin"
		APP_PASSWORD="adminpass"
		APP_LOAD_DEMO_DATA="y"		# y | n

		echo "$APP_HOST" > /tmp/oro_install_APP_HOST
		echo "$APP_USER" > /tmp/oro_install_APP_USER
		echo "$APP_PASSWORD" > /tmp/oro_install_APP_PASSWORD
		echo "$APP_LOAD_DEMO_DATA" > /tmp/oro_install_APP_LOAD_DEMO_DATA

		LICENCE_KEY=#{ENV['licence']}
		GITHUB_TOKEN=#{ENV['gittoken']}

		if [ -z "$LICENCE_KEY" -o -z "$GITHUB_TOKEN" ]; then
			echo "\n\n\n!!!!!!! Warninig! You must provide your Enterprise Licence Key and your GitHub Token to be able to install Oro Enterprise Edition application before 'vagrant up' command (licence=YourEnterpsiseLicenceKey gittoken=yourgithubtoken vagrant up --provision) !!!!!!!\n\n\n"
			exit
		fi

		echo "$GITHUB_TOKEN" > /tmp/oro_install_GITHUB_TOKEN
		
		echo "\n*******************************************************"
		echo "************** Step 1: Environment Setup **************"
		echo "*******************************************************\n"

		echo "\n~~~~~~~~~~~~~~ Enable Required Package Repositories ~~~~~~~~~~~~~~\n"
		
		yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm yum-utils scl-utils centos-release-scl centos-release-scl-rh
		yum-config-manager --add-repo http://koji.oro.cloud/rpms/oro-el7.repo
		yum update -y

		echo "\n~~~~~~~~~~~~~~ Install Nginx, PostgreSQL, Redis, ElasticSearch, NodeJS, Git, Supervisor, and Wget ~~~~~~~~~~~~~~\n"
		
		yum install -y rh-postgresql96 rh-postgresql96-postgresql rh-postgresql96-postgresql-server rh-postgresql96-postgresql-contrib rh-postgresql96-postgresql-syspaths oro-elasticsearch24 oro-elasticsearch24-runtime oro-elasticsearch24-elasticsearch oro-redis4 oro-redis4-runtime oro-redis4-redis oro-rabbitmq-server36 oro-rabbitmq-server36-runtime oro-rabbitmq-server36-rabbitmq-server nginx nodejs npm git bzip2 supervisor
		
		echo "\n~~~~~~~~~~~~~~ Install PHP ~~~~~~~~~~~~~~\n"
		
		yum install -y oro-php71 oro-php71-php-cli oro-php71-php-fpm oro-php71-php-opcache oro-php71-php-mbstring oro-php71-php-mcrypt oro-php71-php-pgsql oro-php71-php-process oro-php71-php-ldap oro-php71-php-gd oro-php71-php-intl oro-php71-php-bcmath oro-php71-php-xml oro-php71-php-soap oro-php71-php-tidy oro-php71-php-zip
		
		echo "\n~~~~~~~~~~~~~~ Install Composer ~~~~~~~~~~~~~~\n"
		
		scl enable oro-php71 'php -r "copy('"'"'https://getcomposer.org/installer'"'"', '"'"'composer-setup.php'"'"');" && php composer-setup.php'
		scl enable oro-php71 'php -r "unlink('"'"'composer-setup.php'"'"');"'
		mv composer.phar /usr/bin/composer
		
		echo "********************************************************************************"
		echo "************** Step 2: Pre-installation Environment Configuration **************"
		echo "********************************************************************************"

		echo "\n~~~~~~~~~~~~~~ Perform Security Configuration ~~~~~~~~~~~~~~\n"

		sed -i 's/SELINUX=enforcing/SELINUX=permissive/g' /etc/selinux/config
		setenforce permissive

		echo "\n~~~~~~~~~~~~~~ Prepare PostgreSQL Database ~~~~~~~~~~~~~~\n"

		# --- Initialize a PostgreSQL Database Cluster ---

		scl enable rh-postgresql96 'postgresql-setup --initdb'

		# --- Enable Password Protected PostgreSQL Authentication ---
		
		sed -i 's/all[ ]*127.0.0.1\\/32[ ]*ident/all   127.0.0.1\\/32   md5/g' /var/opt/rh/rh-postgresql96/lib/pgsql/data/pg_hba.conf 
		sed -i 's/all[ ]*::1\\/128[ ]*ident/all   ::1\\/128   md5/g' /var/opt/rh/rh-postgresql96/lib/pgsql/data/pg_hba.conf 

		# --- Change the Password for the postgres User ---

		systemctl start rh-postgresql96-postgresql
		su - postgres -c "psql -U postgres -d postgres -c \\"alter user postgres with password '$DB_PASSWORD';\\""

		# --- Create a Database for OroCRM Enterprise Edition Application ---

		su - postgres -c "psql -U postgres -d postgres -c \\"CREATE DATABASE oro;\\""
		su - postgres -c "psql -U postgres -d oro -c 'CREATE EXTENSION IF NOT EXISTS \\"uuid-ossp\\";'"

		echo "\n~~~~~~~~~~~~~~ Configure Web Server ~~~~~~~~~~~~~~\n"

		cat > /etc/nginx/conf.d/default.conf <<____NGINXCONFIGTEMPLATE
server {
	server_name $APP_HOST www.$APP_HOST;
	root  /usr/share/nginx/html/oroapp/web;

	index app.php;

	gzip on;
	gzip_proxied any;
	gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;
	gzip_vary on;

	location / {
		# try to serve file directly, fallback to app.php
		try_files \\$uri /app.php\\$is_args\\$args;
	}

	location ~ ^/(app|app_dev|config|install)\\.php(/|$) {
		fastcgi_pass 127.0.0.1:9000;
		# or
		# fastcgi_pass unix:/var/run/php/php7-fpm.sock;
		fastcgi_split_path_info ^(.+\\.php)(/.*)$;
		include fastcgi_params;
		fastcgi_param SCRIPT_FILENAME \\$document_root\\$fastcgi_script_name;
		fastcgi_param HTTPS off;
		fastcgi_buffers 64 64k;
		fastcgi_buffer_size 128k;
	}

	location ~* ^[^(\\.php)]+\\.(jpg|jpeg|gif|png|ico|css|pdf|ppt|txt|bmp|rtf|js)$ {
		access_log off;
		expires 1h;
		add_header Cache-Control public;
	}

	error_log /var/log/nginx/${APP_HOST}_error.log;
	access_log /var/log/nginx/${APP_HOST}_access.log;
}
____NGINXCONFIGTEMPLATE
		
		echo "\n~~~~~~~~~~~~~~ Configure PHP ~~~~~~~~~~~~~~\n"

		sed -i 's/user = apache/user = nginx/g' /etc/opt/oro/oro-php71/php-fpm.d/www.conf
		sed -i 's/group = apache/group = nginx/g' /etc/opt/oro/oro-php71/php-fpm.d/www.conf
		sed -i 's/;catch_workers_output/catch_workers_output/g' /etc/opt/oro/oro-php71/php-fpm.d/www.conf

		sed -i 's/memory_limit = [0-9MG]*/memory_limit = 1G/g' /etc/opt/oro/oro-php71/php.ini
		sed -i 's/;realpath_cache_size = [0-9MGk]*/realpath_cache_size = 4M/g' /etc/opt/oro/oro-php71/php.ini
		sed -i 's/;realpath_cache_ttl = [0-9]*/realpath_cache_ttl = 600/g' /etc/opt/oro/oro-php71/php.ini

		sed -i 's/opcache.enable=[0-1]/opcache.enable=1/g' /etc/opt/oro/oro-php71/php.d/10-opcache.ini
		sed -i 's/;opcache.enable_cli=[0-1]/opcache.enable_cli=0/g' /etc/opt/oro/oro-php71/php.d/10-opcache.ini
		sed -i 's/opcache.memory_consumption=[0-9]*/opcache.memory_consumption=512/g' /etc/opt/oro/oro-php71/php.d/10-opcache.ini
		sed -i 's/opcache.interned_strings_buffer=[0-9]*/opcache.interned_strings_buffer=32/g' /etc/opt/oro/oro-php71/php.d/10-opcache.ini
		sed -i 's/opcache.max_accelerated_files=[0-9]*/opcache.max_accelerated_files=32531/g' /etc/opt/oro/oro-php71/php.d/10-opcache.ini
		sed -i 's/;opcache.save_comments=[0-1]/opcache.save_comments=1/g' /etc/opt/oro/oro-php71/php.d/10-opcache.ini

		echo "\n~~~~~~~~~~~~~~ Configure RabbitMQ ~~~~~~~~~~~~~~\n"

		# --- Create RabbitMQ User ---

		systemctl start oro-rabbitmq-server36-rabbitmq-server
		scl enable oro-rabbitmq-server36 'RABBITMQ_USER=$(cat /tmp/oro_install_RABBITMQ_USER) && RABBITMQ_PASSWORD=$(cat /tmp/oro_install_RABBITMQ_PASSWORD) && rabbitmqctl add_user $RABBITMQ_USER $RABBITMQ_PASSWORD'
		scl enable oro-rabbitmq-server36 'RABBITMQ_USER=$(cat /tmp/oro_install_RABBITMQ_USER) && rabbitmqctl set_user_tags $RABBITMQ_USER administrator'
		scl enable oro-rabbitmq-server36 'RABBITMQ_USER=$(cat /tmp/oro_install_RABBITMQ_USER) && rabbitmqctl set_permissions -p / $RABBITMQ_USER ".*" ".*" ".*"'
		
		scl enable oro-rabbitmq-server36 'rabbitmqctl delete_user guest'
		
		# --- Enable RabbitMQ Plugins ---
		
		scl enable oro-rabbitmq-server36 'rabbitmq-plugins enable rabbitmq_delayed_message_exchange'
		scl enable oro-rabbitmq-server36 'rabbitmq-plugins enable rabbitmq_management'

		echo "\n~~~~~~~~~~~~~~ Enable Installed Services ~~~~~~~~~~~~~~\n"

		systemctl restart rh-postgresql96-postgresql oro-rabbitmq-server36-rabbitmq-server oro-redis4-redis oro-elasticsearch24-elasticsearch oro-php71-php-fpm nginx supervisord
		systemctl enable rh-postgresql96-postgresql oro-rabbitmq-server36-rabbitmq-server oro-redis4-redis oro-elasticsearch24-elasticsearch oro-php71-php-fpm nginx supervisord

		echo "********************************************************************************************"
		echo "************** Step 3: OroPlatform Community Edition Application Installation **************"
		echo "********************************************************************************************"

		echo "\n~~~~~~~~~~~~~~ Get Application Source Code ~~~~~~~~~~~~~~\n"

		# --- Copy application source code from the current host folder to the nginx web folder ---

		cd /usr/share/nginx/html
		mkdir oroapp && cd oroapp
		cp -r /vagrant/* .

		echo "\n~~~~~~~~~~~~~~ Install Application Dependencies ~~~~~~~~~~~~~~\n"

		cp ./app/config/parameters.yml.dist ./app/config/parameters.yml
		
		# --- Install Dependencies ---

		ssh-keyscan -t rsa -H github.com >> ~/.ssh/known_hosts
		scl enable oro-php71 'GITHUB_TOKEN=$(cat /tmp/oro_install_GITHUB_TOKEN) && composer config -g github-oauth.github.com $GITHUB_TOKEN'
		scl enable oro-php71 'composer install --prefer-dist --no-dev'

		# --- Configure app/config/parameters.yml (to prevent composer interactive dialog) ---
		
	 	sed -i "s/database_driver:[ ]*pdo_mysql/database_driver: pdo_pgsql/g" ./app/config/parameters.yml
	 	sed -i "s/database_name:[ ]*[a-zA-Z0-9_]*/database_name: $DB_NAME/g" ./app/config/parameters.yml
	 	sed -i "s/database_user:[ ]*root/database_user: postgres/g" ./app/config/parameters.yml
	 	sed -i "s/database_password:[ ]*null/database_password: '$DB_PASSWORD'/g" ./app/config/parameters.yml
		
	 	sed -i "s/search_engine_name:[ ]*orm/search_engine_name: elastic_search/g" ./app/config/parameters.yml

	 	sed -i "s/session_handler:[ ]*[\\.a-zA-Z0-9_]*/session_handler: 'snc_redis.session.handler'\\n    redis_dsn_session: 'redis:\\/\\/127.0.0.1:6379\\/0'\\n    redis_dsn_cache: 'redis:\\/\\/127.0.0.1:6379\\/1'\\n    redis_dsn_doctrine: 'redis:\\/\\/127.0.0.1:6379\\/2'\\n    redis_setup: 'standalone'/g" ./app/config/parameters.yml
	 	
	 	sed -i "s/enterprise_licence:[ ]*null/enterprise_licence: '$LICENCE_KEY'/g" ./app/config/parameters.yml

	 	sed -i "s/message_queue_transport:[ ]*dbal/message_queue_transport: amqp/g" ./app/config/parameters.yml
	 	sed -i "s/message_queue_transport_config:[ ]*null/message_queue_transport_config: { host: 'localhost', port: '5672', user: '$RABBITMQ_USER', password: '$RABBITMQ_PASSWORD', vhost: '\\/' }/g" ./app/config/parameters.yml

		echo "\n~~~~~~~~~~~~~~ Install OroPlatform Community Edition Application ~~~~~~~~~~~~~~\n"

		scl enable oro-php71 'APP_HOST=$(cat /tmp/oro_install_APP_HOST) && APP_USER=$(cat /tmp/oro_install_APP_USER) && APP_PASSWORD=$(cat /tmp/oro_install_APP_PASSWORD) && APP_LOAD_DEMO_DATA=$(cat /tmp/oro_install_APP_LOAD_DEMO_DATA) && php ./app/console oro:install --env=prod --timeout=900 --no-debug --application-url="http://$APP_HOST/" --organization-name="Oro Inc" --user-name="$APP_USER" --user-email="admin@example.com" --user-firstname="Bob" --user-lastname="Dylan" --user-password="$APP_PASSWORD" --sample-data=$APP_LOAD_DEMO_DATA'

		echo "\n~~~~~~~~~~~~~~ Add Required Permissions for the nginx User ~~~~~~~~~~~~~~\n"

		setfacl -b -R ./
		cd /usr/share/nginx/html/oroapp
		find . -type f -exec chmod 0644 {} \\;
		find . -type d -exec chmod 0755 {} \\;
		chown -R nginx:nginx ./app/{attachment,cache,import_export,logs}
		chown -R nginx:nginx ./web/{media,uploads,js}

		echo "\n*********************************************************************************"
		echo "************** Step 4: Post-installation Environment Configuration **************"
		echo "*********************************************************************************\n"

		echo "\n~~~~~~~~~~~~~~ Schedule Periodical Command Execution ~~~~~~~~~~~~~~\n"

		echo "*/1 * * * * scl enable oro-php71 'php /usr/share/nginx/html/oroapp/app/console oro:cron --env=prod > /dev/null'" > /var/spool/cron/nginx

		echo "\n~~~~~~~~~~~~~~ Configure and Run Required Background Processes ~~~~~~~~~~~~~~\n"

		cat >> /etc/supervisord.conf <<____SUPERVISORDTEMPLATE
[program:oro_web_socket]
command=scl enable oro-php71 'php ./app/console clank:server --env=prod'
numprocs=1
autostart=true
autorestart=true
directory=/usr/share/nginx/html/oroapp
user=nginx
redirect_stderr=true

[program:oro_message_consumer]
command=scl enable oro-php71 'php ./app/console oro:message-queue:consume --env=prod'
process_name=%(program_name)s_%(process_num)02d
numprocs=5
autostart=true
autorestart=true
directory=/usr/share/nginx/html/oroapp
user=nginx
redirect_stderr=true
____SUPERVISORDTEMPLATE
		
		systemctl restart supervisord

		echo "\n**********************************************************************************************************************"
		echo "************** Congratulations! You’ve Successfully Installed OroCommerce Application **********************************"
		echo "**********************************************************************************************************************\n"
		echo "\n************** You should now be able to open the homepage http://$APP_HOST:$FORWARDED_PORT/ and use the application. **************\n"
   SHELL
end

