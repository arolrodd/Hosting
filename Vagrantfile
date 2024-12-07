Vagrant.configure("2") do |config|
  config.vm.box = "debian/bookworm64"

  config.vm.network "forwarded_port", guest: 80, host: 8080

  config.vm.network "private_network", ip: "192.168.57.20"

  config.vm.provision "shell", inline: <<-SHELL
    sudo apt update
    sudo apt install -y nginx
    sudo systemctl enable nginx
    sudo systemctl start nginx
  SHELL
end