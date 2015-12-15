# -*- mode: ruby -*-
# vi: set ft=ruby :


$bootstrap = <<SCRIPT
  yum -y install wget
  wget -O /etc/yum.repos.d/bigtop.repo http://www.apache.org/dist/bigtop/bigtop-1.0.0/repos/centos7/bigtop.repo
  
  yum -y install hadoop\* java-1.7.0-openjdk-devel
SCRIPT

$master_bootstrap = <<SCRIPT

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
  sudo -u hdfs hadoop fs -chown yarn:mapred /var/log/hadoop-yarn

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

  # 

SCRIPT

Vagrant.configure(2) do |config|
  
  config.vm.provision "common", type: "shell", inline: $bootstrap

  config.vm.define "hadoop-master" do |master|
    master.vm.box = "centos/7"
    master.vm.network "private_network", ip: "192.168.10.100"
    master.vm.provision "shell", preserve_order: true, inline: $master_bootstrap
  end
  
  (1..4).each do |i|
    config.vm.define "hadoop-slave-#{i}" do |node|
      node.vm.box = "centos/7"
      node.vm.network "private_network", ip: "192.168.10.10#{i}"
    end
  end

end
