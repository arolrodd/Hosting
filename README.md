# Hosting

## Configuración del dominio
1. Compramos un dominio en IONOS (adjunto foto del menu de IONOS con el dominio ya comprado)
![alt text](images/menuPrincipalIONOS.png)

2. Configuramos la ApiKey de IONOS
  - Menu de configuración de la ApiKey
![alt text](images/ApiIONOS.png)
  - Creaos la nueva ApiKey
![alt text](images/CrearApiIONOS.png)
  - Le ponemos un nombre a la nueva ApiKey
![alt text](images/nombreApi.png)
  - Registro de la nueva ApiKey ya creada
![alt text](images/registroApiCreadaIONOS.png)

3. Configuramos el Dynamic Dns de IONOS
  - Autorización de DNS con la ApiKey
![alt text](images/authorizeDnsConApi.png)
  - Activando dynamic DNS
![alt text](images/cambiandoDynamicDns.png)
  - Respuesta de la request de activación de dynamic DNS con la url para actualizar la Ip asociada a nuestro dominio
![alt text](images/RespuestaConUpdateUrl.png)

4. Configuración de la tarea automática para actualizar la ip asociada a nuestro dominio
  - Creamos una nueva tarea en el programador de tareas de windows
    - Selecionamos el horario de repetición de la tarea en el desencadenador de la tarea
![alt text](images/horarioRepeticionActualizacionDns.png)
    - En la acción de la tarea agregamos como argumento la url de la respuesta de la request de activación de dynamic DNS con la url para actualizar la Ip asociada a nuestro dominio
![alt text](images/accionTareaActualizarDns.png)
  - Foto de la tarea ya configurada
![alt text](images/TareaProgramada.png)

5. Configuración del reenvio de puertos en nuestro Router
  - Accederemos al panel de control de nuestro router con la ip de enlace de internet y la clave del wifi si no lo has cambiado anteriormente
  - En la sección de configuración avanzada, en la sección de redireccionamiento de puertos de la interfaz de administración de red añadiremos dos nuevas redirecciones, el puerto 80 y el puerto 443 (Tienen que apuntar a la ip de nuestro servidor web)
![alt text](images/reenviosPuertosRouter.png)

6. Probamos quwe se vea el dominio en internet con una pagina de prueba
![alt text](images/comprobacionDominioWeb.png)

## Configuración del servidor web
1. Lo primero que tenemos que hacer es en la carpeta que hayamos creado para la práctica, hacer 'vagrant up' para iniciar vagrnat y la configuración de nuestro servidor web

2. Una vez iniciado vagrant tenemos que configurar el archivo vagrantFile para que vagrant pueda iniciar nuestro servidor web
  - Esta seria la configuración que tenemos que hacer en el archivo vagrantFile
  ```ruby
    Vagrant.configure("2") do |config|
      config.vm.box = "debian/bookworm64"

      config.vm.define "vb" do |vb|

        vb.vm.network "forwarded_port", guest: 80, host: 8080
        vb.vm.network "public_network", ip: "192.168.210.100"

        vb.vm.provision "shell", inline: <<-SHELL
          sudo apt update
          sudo apt install -y apache2
          sudo apt install ufw

          sudo ufw allow 80/tcp
          sudo ufw allow 443/tcp


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
```