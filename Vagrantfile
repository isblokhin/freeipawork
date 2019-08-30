# -*- mode: ruby -*- vi: set ft=ruby :
Vagrant.configure(2) do |config|
  config.vm.box = "centos/7"
#  config.vm.provision "ansible" do |ansible|
#    ansible.verbose = "vvv" 
#    ansible.playbook = "playbook-client.yml" 
#    ansible.sudo = "true"
#  end
  config.vm.provider "virtualbox" do |v|
	  v.memory = 2048
  end
  config.vm.define "ipaserver.otus.lab" do |server|
    server.vm.network "private_network", ip: "192.168.50.10"
    #server.vm.network 'forwarded_port', guest: 80, host: 8080, host_ip: '127.0.0.1' server.vm.network 'forwarded_port', guest: 443, host: 4443, host_ip: 
    #'127.0.0.1'
    server.vm.provision "shell", run: "always", inline: <<-SHELL
          #setenforce 0 sed -i "s/SELINUX=enforcing/SELINUX=disabled/" /etc/selinux/config systemctl stop firewalld systemctl disable firewalld
          firewall-cmd --permanent --zone=public --add-service={ntp,http,https,ldap,ldaps,kerberos,kpasswd,dns}
          firewall-cmd --permanent --zone=public --add-port=53/tcp
          firewall-cmd --permanent --zone=public --add-port=53/udp
          firewall-cmd --reload
          hostnamectl set-hostname ipaserver.otus.lab
          echo -e "192.168.50.11 client.otus.lab client" >> /etc/hosts
          echo -e "192.168.50.10 ipaserver.otus.lab ipaserver" >> /etc/hosts
          
          yum install epel-release -y
          yum install ipa-server -y
          ipa-server-install -a password --hostname=ipaserver.otus.lab -r OTUS.LAB -p password -n otus.lab -U
          SHELL
        
  end
  config.vm.define "client" do |client|
  client.vm.network "private_network", ip: "192.168.50.11"
  client.vm.hostname = 'client'
  client.vm.provision "ansible" do |ansible|
  ansible.verbose = "vvv"
  ansible.playbook = "playbook-client.yml"
  ansible.sudo = "true"

 
    #client.vm.provision "shell", run: "always", inline: <<-SHELL hostnamectl set-hostname client.otus.lab echo -e "192.168.50.11 client.otus.lab client" 
    #>> /etc/hosts
    #echo -e "192.168.50.10 ipaserver.otus.lab ipaserver" >> /etc/hosts yum install bind-utils -y yum install freeipa-client -y ipa-client-install 
    #--domain=example.com --server=ipaserver.otus.lab --realm=OTUS.LAB --principal=admin --password=password --hostname=client.otus.lab --no-ntp 
    #--unattended SHELL
  end
  end
end
