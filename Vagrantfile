Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/focal64"
  config.vm.provider "virtualbox" do |vb|
     vb.memory = "3072"
     vb.cpus = 2
 end
  config.vm.provision :shell, path: "bootstrap.sh"
end

