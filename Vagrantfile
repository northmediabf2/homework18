# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|
  config.vm.box = "centos/7"
  config.vm.box_check_update = true
  #config.vm.network "forwarded_port", guest: 80, host: 8080
  #config.vm.synced_folder "../data", "/vagrant_data"
  config.ssh.forward_agent = true
  config.ssh.forward_x11 = true

  config.vm.provision "shell", inline: <<-SHELL
    set -x
    yum update -y && yum upgrade -y
    yum install -y policycoreutils-python xorg-x11-server-Xorg xorg-x11-xauth xorg-x11-apps firefox psmisc epel-release.noarch # Установка утилит
  SHELL

  config.vm.provider "virtualbox" do |v|
    v.memory = 4096
    v.cpus = 4
  end

  config.vm.define "server", primary: true do |s|
    s.vm.hostname = 'lab18'
    s.vm.network "private_network", ip: "192.168.111.10"
    s.vm.provision "shell", inline: <<-SHELL
    
    sudo yum install -y zlib-devel libuuid-devel libmnl-devel gcc make git autoconf autogen automake pkgconfig install curl jq nodejs  # Установка пакетов  
    sudo yum install -y Judy-devel MySQL-python autoconf-archive libuv-devel lm_sensors lz4-devel nmap-ncat openssl-devel python-psycopg2   
  
      cd ~
      git clone https://github.com/firehol/netdata.git --depth=1
      cd ~/netdata; sudo ./netdata-installer.sh --dont-wait
      sudo systemctl start firewalld
      sudo firewall-cmd --permanent --zone=public --add-port=19999/tcp
      sudo firewall-cmd --reload
      sed -i '/#[[:blank:]]*hostname/s/#//' /etc/netdata/netdata.conf
      sed -i "s/lab18/Roman/g" /etc/netdata/netdata.conf
      sudo systemctl restart netdata.service
      sudo systemctl enable netdata.service
      echo 1 >/sys/kernel/mm/ksm/run
      echo 1000 >/sys/kernel/mm/ksm/sleep_millisecs
      semanage port -a -t ssh_port_t -p tcp 19999
      systemctl restart netdata
      #  bconsole: label ... MyVolume, 2, ...
      #  run
    SHELL
  end
end