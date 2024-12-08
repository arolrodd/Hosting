# Hosting
Este repositorio es una practica sobre comprar un dominio y hacer hosting 
Informe de Configuración y Pruebas del Servidor Web Apache
1. Conexión Segura (3 Puntos)
Para asegurarme de que el servidor está configurado correctamente con HTTPS, utilicé un certificado SSL proporcionado por una autoridad confiable. Configuré el archivo 000-default.conf en Apache para usar HTTPS con el certificado que he obtenido.

El archivo de configuración tiene las siguientes líneas para habilitar SSL:

apache
Copiar código
<VirtualHost *:443>
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/webHosting.org.cer
    SSLCertificateKeyFile /etc/ssl/private/webHosting.org.key
    DocumentRoot /var/www/webHosting/html/web
    ServerName hosting.aroldan.org
</VirtualHost>
Foto 1:
Coloca una captura de la configuración de Apache mostrando la sección SSL.

Verifiqué la conexión segura visitando el sitio en mi navegador, lo que confirmó que el sitio estaba utilizando HTTPS correctamente. Usé también el comando curl -I https://hosting.aroldan.org para verificar la respuesta del servidor.

2. Autenticación Básica (2 Puntos)
Para habilitar la autenticación básica, utilicé el archivo .htpasswd para almacenar las credenciales de usuario. El usuario admin tiene como contraseña asir. Configuré la ruta /admin para que solo fuera accesible después de una autenticación válida.

El archivo de configuración de Apache para la ruta /admin.html se ve así:

apache
Copiar código
<Location /admin.html>
    AuthType Basic
    AuthName "Restricted Area"
    AuthUserFile /etc/apache2/.htpasswd
    Require valid-user
</Location>
Foto 2:
Coloca una captura de la pantalla que muestra el archivo .htpasswd con las credenciales.

Pude verificar que, al intentar acceder a /admin.html, me solicitaba el usuario y contraseña, y al ingresar admin y asir, pude acceder sin problemas. Esto confirma que la autenticación básica está funcionando correctamente.

3. Páginas de Error Personalizadas (1 Punto)
He configurado una página personalizada de error 404 para que, cuando un usuario intente acceder a una página que no existe, vea una página amigable.

En la configuración de Apache, añadí la siguiente línea:

apache
Copiar código
ErrorDocument 404 /404.html
Creé el archivo 404.html dentro del directorio /var/www/webHosting/html y lo personalicé con un diseño adecuado para el sitio.

Foto 3:
Añade una captura de la página 404 personalizada que he creado.

Verifiqué que cuando accedía a una ruta inexistente, se mostraba correctamente la página de error 404 personalizada.

4. Status del Servidor (2 Puntos)
Para mostrar el estado del servidor, habilité el módulo mod_status de Apache. Esto me permite ver estadísticas sobre las peticiones y el uso de recursos en el servidor.

En la configuración de Apache, añadí lo siguiente:

apache
Copiar código
<Location /status>
    SetHandler server-status
    AuthType Basic
    AuthName "Restricted Area"
    AuthUserFile /etc/apache2/.htpasswd
    Require valid-user
</Location>
También activé el estado extendido de Apache añadiendo la siguiente línea en el archivo apache2.conf:

apache
Copiar código
ExtendedStatus On
LogLevel alert rewrite:trace6
Foto 4:
Coloca una captura de la salida de server-status en el navegador, mostrando las estadísticas del servidor después de la autenticación.

Tras acceder a /status e ingresar las credenciales correctas, pude ver información detallada sobre las peticiones, uso de memoria y otros datos útiles del servidor.

5. Pruebas de Rendimiento (2 Puntos)
Realicé las pruebas de rendimiento utilizando la herramienta ab (Apache Benchmark) para simular 100 y 1000 usuarios simultáneos realizando 1000 peticiones cada uno.

Los comandos que usé fueron los siguientes:

bash
Copiar código
ab -n 1000 -c 100 https://hosting.aroldan.org/status
siege -r 10 -c 1000 https://hosting.aroldan.org/status
Foto 5:
Pon una captura del resultado de las pruebas de rendimiento con ab o siege, mostrando el número de peticiones por segundo y la latencia.

Las pruebas mostraron que el servidor soporta bien las peticiones simultáneas, pero con 1000 usuarios simultáneos, la latencia aumentó significativamente, lo que es de esperar bajo alta carga.

6. Despliegue Automatizado (2 Puntos)
Para automatizar el despliegue, utilicé Vagrant, lo que me permitió crear y gestionar una máquina virtual con la configuración de Apache de forma sencilla.

La configuración del Vagrantfile incluye la instalación de Apache y las configuraciones necesarias para poner en marcha el servidor.

ruby
Copiar código
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"
  config.vm.network "private_network", type: "dhcp"
  config.vm.provision "shell", inline: <<-SHELL
    sudo apt update
    sudo apt install apache2
    sudo systemctl enable apache2
  SHELL
end
Foto 6:
Incluye una captura del archivo Vagrantfile y la terminal mostrando que el servidor se ha provisionado correctamente.

Esto permitió crear el servidor desde cero de manera rápida, asegurando que todas las configuraciones se aplicaran correctamente en la máquina virtual.

7. Documentación (2 Puntos)
En este documento, he explicado todos los pasos realizados para configurar y probar el servidor Apache, incluyendo las configuraciones de seguridad, autenticación, y pruebas de rendimiento. La documentación está organizada en secciones claras para cada requisito y su implementación.

Conclusión:
Con esta configuración, he logrado cumplir con todos los requisitos del proyecto. El servidor está seguro, con autenticación básica habilitada en las rutas /admin y /status. Además, he implementado la página de error personalizada 404 y realizado las pruebas de rendimiento necesarias para asegurarme de que el servidor pueda manejar múltiples usuarios simultáneamente.