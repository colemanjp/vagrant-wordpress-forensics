# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|
  config.vm.box = "fedora/23-cloud-base"
  config.vm.network "forwarded_port", guest: 80, host: 8080
  config.vm.provider :libvirt do |libvirt|
	libvirt.memory = "2048"
  end
  config.vm.provision "shell", inline: <<-SHELL
     # install various deps
     dnf install -v -y community-mysql-server community-mysql httpd php php-mysqlnd
     dnf install -v -y gcc ruby-devel libxml2 libxml2-devel libxslt libxslt-devel libcurl-devel patch rpm-build
     dnf install -v -y git make
     dnf install -v -y words
     dnf install -v -y nc telnet
     dnf install -v -y php-pear-XML-Parser
     dnf update -v -y libnghttp2
     #
     # Back off selinux
     setenforce Permissive
     #
     # copy the wordpress site from the vagrant dir into the vm wordpress
     rsync -a /vagrant/k/ /var/www/html
     # copy the mysql db into the vm mysql
     rsync -a /vagrant/mysql/k /var/lib/mysql
     #
     # Keep wp from redirecting to localhost port 80
     echo "define('WP_HOME','http://localhost:8080');" >> /var/www/html/wp-config.php
     echo "define('WP_SITEURL','http://localhost:8080');" >> /var/www/html/wp-config.php
     #
     # Enable and start services
     systemctl enable httpd
     systemctl enable mysqld
     systemctl restart httpd
     systemctl restart mysqld
     #
     # Set up wpscan wp testing tool
     cd /vagrant && git clone https://github.com/wpscanteam/wpscan.git
     cd /vagrant/wpscan && gem install bundler 
     cd /vagrant/wpscan && /usr/local/bin/bundle install --without test
     #
     # I didn't bother with editing the db connection strings in vagrant
     echo "YOU MUST EDIT THE DB CONNECTION MANUALLY in wp-config.php"
  SHELL
end
