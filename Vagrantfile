# -*- mode: ruby -*-
# vi: set ft=ruby :


$bootstrap = <<SCRIPT
  yum -y install wget
  wget -O /etc/yum.repos.d/bigtop.repo http://www.apache.org/dist/bigtop/bigtop-1.0.0/repos/centos7/bigtop.repo
  
  yum -y install hadoop\*
SCRIPT

$master_bootstrap = <<SCRIPT
df
SCRIPT

Vagrant.configure(2) do |config|
  
  config.vm.provision "common", type: "shell", inline: $bootstrap

  config.vm.define "hadoop-master" do |master|
    master.vm.box = "centos/7"
    master.vm.provision "shell", preserve_order: true, inline: $master_bootstrap
  end
  
  (1..4).each do |i|
    config.vm.define "hadoop-slave-#{i}" do |node|
      node.vm.box = "centos/7"
    end
  end

end
