# -*- mode: ruby -*-
# vi: set ft=ruby :


$bootstrap = <<SCRIPT
  yum -y install wget
  wget -O /etc/yum.repos.d/bigtop.repo http://www.apache.org/dist/bigtop/bigtop-1.0.0/repos/centos7/bigtop.repo
  
  yum -y install hadoop\* java-1.7.0-openjdk-devel
  
  echo "127.0.0.1 localhost"            > /etc/hosts
  echo "192.168.77.100 hadoop-master"   >> /etc/hosts
  echo "192.168.77.101 hadoop-slave-1"  >> /etc/hosts
  echo "192.168.77.102 hadoop-slave-2"  >> /etc/hosts
  echo "192.168.77.103 hadoop-slave-3"  >> /etc/hosts
  echo "192.168.77.104 hadoop-slave-4"  >> /etc/hosts

SCRIPT


$master_bootstrap = <<SCRIPT

  cp -f /vagrant/config/common/* /etc/hadoop/conf/
  cp -f /vagrant/config/master/* /etc/hadoop/conf/

  # Init HDFS 
  sudo -u hdfs hdfs namenode -format -force
  service hadoop-hdfs-namenode start
  service hadoop-hdfs-datanode start
  
  # Create HDFS directory structure
  sudo -u hdfs hadoop fs -mkdir -p /user/$USER
  sudo -u hdfs hadoop fs -chown $USER:$USER /user/$USER
  sudo -u hdfs hadoop fs -chmod 770 /user/$USER

  sudo -u hdfs hadoop fs -mkdir /tmp
  sudo -u hdfs hadoop fs -chmod -R 1777 /tmp

  sudo -u hdfs hadoop fs -mkdir -p /var/log/hadoop-yarn
  sudo -u hdfs hadoop fs -chown -R yarn:mapred /var/log/hadoop-yarn

  sudo -u hdfs hadoop fs -mkdir -p /user/history
  sudo -u hdfs hadoop fs -chown mapred:mapred /user/history
  sudo -u hdfs hadoop fs -chmod 770 /user/history

  sudo -u hdfs hadoop fs -mkdir -p /tmp/hadoop-yarn/staging
  sudo -u hdfs hadoop fs -chmod -R 1777 /tmp/hadoop-yarn/staging

  sudo -u hdfs hadoop fs -mkdir -p /tmp/hadoop-yarn/staging/history/done_intermediate
  sudo -u hdfs hadoop fs -chmod -R 1777 /tmp/hadoop-yarn/staging/history/done_intermediate
  sudo -u hdfs hadoop fs -chown -R mapred:mapred /tmp/hadoop-yarn/staging
  
  # Start YARN daemons
  service hadoop-yarn-resourcemanager start
  service hadoop-yarn-nodemanager start
  
SCRIPT

$slave_bootstrap = <<SCRIPT

  yum -y install hadoop-hdfs-datanode
  yum -y install hadoop-yarn-nodemanager
  yum -y install hadoop-mapreduce

  cp -f /vagrant/config/common/* /etc/hadoop/conf/
  cp -f /vagrant/config/slave/* /etc/hadoop/conf/
  
  service hadoop-hdfs-datanode start
  service hadoop-yarn-nodemanager start
  
SCRIPT



Vagrant.configure(2) do |config|
  
  config.vm.provider "virtualbox" do |v|
    v.gui    = true
    v.memory = 2048
  end
  
  config.vm.synced_folder ".", "/vagrant", type: "rsync", rsync__exclude: ".git/"
  
  config.vm.provision "common", type: "shell", inline: $bootstrap

  config.vm.define "hadoop-master" do |master|
    master.vm.box      = "centos/7"
    master.vm.hostname = "hadoop-master.selvaraj.com"
    master.vm.network    "private_network", ip: "192.168.77.100"
    master.vm.provision  "shell", preserve_order: true, inline: $master_bootstrap
  end
  
  (1..2).each do |i|
    config.vm.define "hadoop-slave-#{i}" do |node|
      node.vm.box      = "centos/7"
      node.vm.hostname = "hadoop-slave-#{i}.selvaraj.com"
      node.vm.network "private_network", ip: "192.168.77.10#{i}"
      node.vm.provision "shell", preserve_order: true, inline: $slave_bootstrap
    end
  end

end
