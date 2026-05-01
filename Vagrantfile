Vagrant.configure("2") do |config|

  config.vm.box = "ubuntu/jammy64"

  NETWORK_PREFIX = "192.168.56"

  nodes = [
    { :name => "master", :ip => "#{NETWORK_PREFIX}.10", :memory => 4096, :cpus => 2 },
    { :name => "worker1", :ip => "#{NETWORK_PREFIX}.11", :memory => 2048, :cpus => 2 },
    { :name => "worker2", :ip => "#{NETWORK_PREFIX}.12", :memory => 2048, :cpus => 2 }
  ]

  nodes.each do |node|
    config.vm.define node[:name] do |node_config|
      node_config.vm.hostname = node[:name]

      node_config.vm.network "private_network", ip: node[:ip]

      node_config.vm.provider "virtualbox" do |vb|
        vb.name = node[:name]
        vb.memory = node[:memory]
        vb.cpus = node[:cpus]
      end

      node_config.vm.provision "shell",
        path: "./deploy.sh",
        args: [node[:name],]

    end
  end
end
