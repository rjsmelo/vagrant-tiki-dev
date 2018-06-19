# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  config.vm.box = "bento/ubuntu-16.04"

  # Remove comment to disable automatic box update checking
  # config.vm.box_check_update = false
  config.vm.provider :virtualbox do |v|
    v.customize ["modifyvm", :id, "--memory", 2048] # 2G
    # v.customize ["modifyvm", :id, "--memory", 4086] # 4G
  end

  # Create a private network, which allows host-only access to the machine
  # using DHCP
  config.vm.network "private_network", type: "dhcp"
  # You can use a using a specific IP instead
  # config.vm.network "private_network", ip: "192.168.33.10"

  config.vm.synced_folder "./", "/vagrant",
        :mount_options => ['dmode=777', 'fmode=777']

  config.vm.provision "shell", inline: <<-SHELL

    grep -q nameserver /etc/resolv.conf || chmod u+w /etc/resolv.conf && echo "nameserver 8.8.8.8" > /etc/resolv.conf

    export DEBIAN_FRONTEND=noninteractive

    apt-get update -y

    apt-get install -y software-properties-common
    add-apt-repository -y ppa:ondrej/php

    # Add Elasticsearch Repo for version 2.x
    wget -qO - https://packages.elastic.co/GPG-KEY-elasticsearch | apt-key add -
    # echo "deb https://artifacts.elastic.co/packages/2.x/apt stable main" > /etc/apt/sources.list.d/elasticsearch-2.x.list
    echo "deb https://artifacts.elastic.co/packages/5.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-5.x.list

    apt-get update -y
    apt-get install -y \
        php7.1-cli php7.1-fpm php7.1-mysql php7.1-xml php7.1-zip php7.1-curl php7.1-gd php7.1-mbstring php7.1-mcrypt \
	php7.1-sqlite3 sqlite3 \
        apache2 \
        mysql-server mysql-client \
        openjdk-8-jre-headless elasticsearch

    a2dismod php7.1
    a2enmod rewrite
    a2enmod proxy
    a2enmod proxy_fcgi
    a2enconf php7.1-fpm

    printf '<Directory /var/www/html>\nAllowOverride All\n</Directory>\n' >  /etc/apache2/conf-available/override.conf
    a2enconf override

    systemctl enable elasticsearch.service

    #
    # Dev Settings
    #

    # Dev tools
    apt-get install -y subversion git

    # Configure PHP
    apt-get install php-xdebug
    (
        echo '; Local configuration for PHP'
        echo '; priority=99'
        echo 'date.timezone = "UTC"'
        echo 'xdebug.remote_enable=1'
    ) > /etc/php/7.1/mods-available/local.ini
    phpenmod local
    sed -i 's/^display_errors.*$/display_errors = On/' /etc/php/7.1/fpm/php.ini
    sed -i 's/^display_startup_errors.*$/display_startup_errors = On/' /etc/php/7.1/fpm/php.ini
    sed -i 's/^display_errors.*$/display_errors = On/' /etc/php/7.1/cli/php.ini
    sed -i 's/^display_startup_errors.*$/display_startup_errors = On/' /etc/php/7.1/cli/php.ini

    # Configure Mysql
    sed -i 's/bind-address.*$/bind-address = 0.0.0.0/' /etc/mysql/mysql.conf.d/mysqld.cnf
    echo "GRANT ALL ON *.* TO 'admin' IDENTIFIED BY 'MysqlBoxPassword';" | mysql

    # Install Composer
    if [ ! -f /usr/local/bin/composer ] ; then
        EXPECTED_SIGNATURE=$(wget -q -O - https://composer.github.io/installer.sig)
        php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
        ACTUAL_SIGNATURE=$(php -r "echo hash_file('SHA384', 'composer-setup.php');")
        if [ "$EXPECTED_SIGNATURE" != "$ACTUAL_SIGNATURE" ]
        then
            rm composer-setup.php
            echo 'ERROR: Invalid installer signature'
        else
            php composer-setup.php --quiet --install-dir=/usr/local/bin  --filename=composer
            RESULT=$?
            rm composer-setup.php
            echo 'Composer Installed'
        fi      
    fi

    #
    # ~Dev Settings
    #

    service php7.1-fpm restart
    service apache2 restart
    service mysql restart
    systemctl start elasticsearch.service

    if [ -d /var/www/html ] ; then
      mv /var/www/html /var/www/html.old
      ln -s /vagrant/webroot /var/www/html
    fi

  SHELL

end
