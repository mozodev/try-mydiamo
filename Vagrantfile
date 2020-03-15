# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.
  config.vm.box = "ubuntu/trusty64"
  config.vm.box_check_update = false
  config.vm.network "forwarded_port", guest: 3306, host: 33065, host_ip: "127.0.0.1"
  config.vm.provider "virtualbox" do |vb|
    vb.name = "try-mydiamo"
    vb.memory = "1024"
  end

  config.vm.provision "shell", inline: <<-SHELL
    # house keeping
    cd ~/
    apt-get -y -qq update && apt-get -y -qq upgrade
    echo "Asia/Seoul" > /etc/timezone
    dpkg-reconfigure -f noninteractive tzdata

    # install mysql and sample database
    echo "mysql-server-5.6 mysql-server/root_password password root" | debconf-set-selections
    echo "mysql-server-5.6 mysql-server/root_password_again password root" | debconf-set-selections
    apt-get -y -qq install mysql-server-5.6
    cp /vagrant/my.cnf /home/vagrant/.my.cnf && cp /vagrant/my.cnf /root/.my.cnf
    sed -i 's/127\.0\.0\.1/0\.0\.0\.0/g' /etc/mysql/my.cnf
    mysql -e 'USE mysql; UPDATE `user` SET `Host`="%" WHERE `User`="root" AND `Host`="localhost"; DELETE FROM `user` WHERE `Host` != "%" AND `User`="root"; FLUSH PRIVILEGES;'
    service mysql restart
    mysql < /vagrant/customers.sql

    # prepare for installing MyDiamo
    curl -q 'https://mydiamo.com/download/MyDiamo_for_MySQL_Package_4.0.6.3_Linux.tar.gz' | tar zx
    chmod +x install_MyDiamo_for_MySQL_v4.0.6.3_linux.run
    echo 'export PATH=/mydiamo/bin:$PATH' >> /etc/profile
  SHELL
end
