# SSL

### Instalar servidor web apache y permitir firewall
  `sudo apt install apache2`

  `sudo ufw allow "Apache Full"`
  
### Habilitar SSL
  `sudo a2enmod ssl`

  `sudo systemctl restart apache2`

### Crear el certificado SSL

``` sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/apache-selfsigned.key -out /etc/ssl/certs/apache-selfsigned.crt ```

> ## Estructura de comando
>
> `openssl`: es la herramienta de línea de comandos para crear y administrar certificados, claves y otros archivos de OpenSSL.
> 
> `req -x509`: especifica que deseamos usar la administración de la solicitud de firma de certificados (CSR) X.509. El “X.509” es un estándar de infraestructura de claves públicas al que se adhieren SSL y TLS para la administración de claves y certificados.
>
> `-nodes`: indica a OpenSSL que omita la opción para proteger nuestro certificado con una frase de contraseña. Necesitamos que Apache pueda leer el archivo, sin intervención del usuario, cuando se inicie el servidor. Una frase de contraseña evitaría que esto suceda porque tendríamos que introducirla tras cada reinicio.
>
> `-days 365`: esta opción establece el tiempo durante el cual el certificado se considerará válido. En este caso, lo configuramos por un año. Muchos navegadores modernos rechazarán cualquier certificado válido por más de un año.
> 
> `-newkey rsa:2048`: especifica que deseamos generar un nuevo certificado y una nueva clave al mismo tiempo. No creamos la clave que se requiere para firmar el certificado en un paso anterior, por lo que debemos crearla junto con el certificado. La parte rsa:2048 le indica que cree una clave RSA de 2048 bits de extensión.
>
> `-keyout`: esta línea indica a OpenSSL dónde colocar el archivo de clave privada generado que estamos creando.
>
> `-out`: indica a OpenSSL dónde colocar el certificado que creamos.




> #### LLENAR SIGUIENTES INSTRUCCIONES:
> - Country Name (2 letter code) [XX]:**MX**
>
> - State or Province Name (full name) []:**Puebla**
>
> - Locality Name (eg, city) [Default City]:**Tecamachalco** 
>
> - Organization Name (eg, company) [Default Company Ltd]:**UTTECAM**
>
> - Organizational Unit Name (eg, section) []:**RIC**
>
> - Common Name (eg, your name or your server's hostname) []:**IP_MAQUINA**
>
> - Email Address []:**TUNOMBRE@EJEMPLO.com** 




## Configurar Apache para usar SSL

`sudo nano /etc/apache2/sites-available/IP_MAQUINA.conf`
> Contenido de archivo

```
<VirtualHost *:443>
   ServerName IP_MAQUINA
   DocumentRoot /var/www/IP_MAQUINA

   SSLEngine on
   SSLCertificateFile /etc/ssl/certs/apache-selfsigned.crt
   SSLCertificateKeyFile /etc/ssl/private/apache-selfsigned.key
</VirtualHost>
```
`sudo mkdir /var/www/IP_MAQUINA`

`sudo nano /var/www/IP_MAQUINA/index.html`

```
<h1>PAGINA CON HTTPS Y SSL</h1>
<h3>AGREGA LO QUE QUIERAS</h3>
```

`sudo a2ensite IP_MAQUINA.conf`


`sudo apache2ctl configtest`

```
AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 127.0.1.1. Set the 'ServerName' directive globally to suppress this message
Syntax OK
```

`sudo systemctl reload apache2`


## Redirigir HTTP a HTTPS
`sudo nano /etc/apache2/sites-available/IP_MAQUINA.conf`

```
<VirtualHost *:80>
	ServerName IP_MAQUINA
	Redirect / https://IP_MAQUINA/
</VirtualHost>
```
```
sudo apachectl configtest
sudo systemctl reload apache2
```
