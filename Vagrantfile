Vagrant.configure("2") do |config|
  config.vm.box = "debian/bookworm64"

  config.vm.define "vb" do |vb|

    vb.vm.network "forwarded_port", guest: 80, host: 8080
    vb.vm.network "private_network", ip: "192.168.57.20"

    vb.vm.provision "shell", inline: <<-SHELL
      sudo apt update
      sudo apt install -y apache2


      # Iniciar Apache
      sudo systemctl start apache2
      sudo systemctl enable apache2

      # Crear directorios y copiar los archivos del proyecto
      sudo mkdir -p /var/www/webHosting/html
      sudo cp -r /vagrant/web/ /var/www/webHosting/html
      sudo chown -R www-data:www-data /var/www/webHosting/html/web
      sudo chmod -R 755 /var/www/webHosting/html 

      sudo htpasswd -cb /etc/apache2/.htpasswd admin asir
      sudo htpasswd -b /etc/apache2/.htpasswd sysadmin risa 


      sudo cp /vagrant/_.aroldan.org_private_key.key /etc/ssl/private/webHosting.org.key
      sudo cp /vagrant/aroldan.org_ssl_certificate.cer /etc/ssl/certs/webHosting.org.cer

      sudo chmod 600 /etc/ssl/private/webHosting.org.key
      sudo chown root:root /etc/ssl/private/webHosting.org.key


      # Copiar archivos de configuración de Apache
      cp /vagrant/default-ssl.conf /etc/apache2/sites-available/default-ssl.conf
      cp /vagrant/000-default.conf /etc/apache2/sites-available/000-default.conf

      cp /vagrant/apache2.conf /etc/apache2/apache2.conf

      # Habilitar sitios y módulos SSL
      sudo a2ensite 000-default.conf
      sudo a2ensite default-ssl.conf
      sudo a2enmod auth_basic
      sudo a2enmod headers
      sudo a2enmod ssl
      sudo systemctl restart apache2
    SHELL
  end
end
