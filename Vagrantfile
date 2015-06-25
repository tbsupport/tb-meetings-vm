Vagrant.configure("2") do |config|

  config.vm.box       = "precise64"
  config.vm.box_url   = "http://files.vagrantup.com/precise64.box"
  config.vm.network :forwarded_port, host: 2200, guest: 22 

  config.vm.provider "virtualbox" do |v|
  	v.memory = 1024
  end

  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "priv/ansible/teachbase_dev.yml"
    ansible.inventory_path = "priv/ansible/dev_hosts"
    ansible.tags = ENV["TAGS"]
    ansible.skip_tags = ENV["SKIP_TAGS"]
    ansible.verbose = 'v'
    ansible.limit = 'all'
  end

  config.vm.network :forwarded_port, host: 2202, guest: 22 

  # rails app
  config.vm.network "forwarded_port", guest: 3000, host: 3000
  
  # erly app http
  config.vm.network "forwarded_port", guest: 8082, host: 8082
  
  # erly app rtmp
  config.vm.network "forwarded_port", guest: 8935, host: 1935
  
  # varnish
  config.vm.network "forwarded_port", guest: 80, host: 8081

  config.vm.network "private_network", ip: "192.168.50.113"

  config.vm.synced_folder ".", "/vagrant", :disabled => true
  config.vm.synced_folder "src/", "/webapps", create: true, id: "vagrant-root",
    owner: "vagrant",
    group: "www-data",
    mount_options: ["dmode=775,fmode=764"]

end