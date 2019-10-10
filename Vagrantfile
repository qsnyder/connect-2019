# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.define "n9kv1" do |n9kv1|
    n9kv1.vm.box = "n9000v-9-2-2" #change to your own box name on import
    n9kv1.ssh.insert_key = false
    n9kv1.vm.boot_timeout = 180
    n9kv1.vm.synced_folder '.', '/vagrant', disabled: true
    n9kv1.vm.network "forwarded_port", guest: 8080, host: 8080
    n9kv1.vm.network "forwarded_port", guest: 8443, host: 8443
    n9kv1.vm.network "private_network", auto_config: false, virtualbox__intnet: "nxosv_network1"
    n9kv1.vm.provider :virtualbox do |vb|
      vb.customize ['modifyvm',:id,'--nicpromisc2','allow-all']
    end
  end
# Still working to complete -- resource contention with telnet ports
  # config.vm.define "n9kv2" do |n9kv2|
  #   n9kv2.vm.box = "n9000v-9-2-2"
  #   n9kv2.ssh.insert_key = false
  #   n9kv2.vm.boot_timeout = 180
  #   n9kv2.vm.synced_folder '.', '/vagrant', disabled: true
  #   n9kv2.vm.network "forwarded_port", guest: 8081, host: 8081
  #   n9kv2.vm.network "forwarded_port", guest: 8444, host: 8444
  #   n9kv2.vm.network "private_network", auto_config: false, virtualbox__intnet: "nxosv_network1"
  #   n9kv2.vm.provider :virtualbox do |vb|
  #     vb.customize ['modifyvm',:id,'--nicpromisc2','allow-all']
  #   end
  # end
end
