# Casos prácticos.

**A) Versión de Nginx instalado.**

```console
nginx -v
```

![1](https://github.com/estebancr1993/nginx/blob/main/imagenes/1.JPG)

*la opcion nginx -V da más información.*

**B) Servicio asociado.**

Visualizar en el estado que se encuentra: ```systemctl status nginx```

Habilitar el servicio: ```systemctl enable nginx```

Deshabilitar el servicio: ```systemctl disable nginx```

Iniciar el servicio: ```systemctl start nginx```

Parar el servicio: ```systemctl stop nginx```

Reiniciar el servicio: ```systemctl restart nginx```

Recargar el servicio: ```systemctl reload nginx```

**C) Ficheros de configuración.**

Los ficheros de configuración del servicio nginx los encontramos en el directorio **```/etc/nginx```** y los importantes y que vamos a modificar seran: 

**```nginx.conf```**

**```sites-available```**

**```sites-enabled```**

> NOTA: Es recomendable hacer una copia de los ficheros que se vayan a modificar.

**D) Página web por defecto:**

Modifica la página web que lanza por defecto y personalízala.

```echo "<h1>Bienvenido a mi servidor web NGINX</h1>" > /var/html/index.html```

```echo "<h2>Mi nombre es Esteban.</h2>" >> /var/html/index.html```

Comprobar en el navegador:

![2](https://github.com/estebancr1993/nginx/blob/main/imagenes/2.JPG)

**E) Virtual Hosting:**

Queremos que nuestro servidor web ofrezca dos sitios web, teniendo en cuenta lo siguiente:
- Cada sitio web tendrá nombres distintos.
- Cada sitio web compartirán la misma dirección IP y el mismo puerto (80).
- Los dos sitios web tendrán las siguientes características:

    - El nombre de dominio del primero será ```www.web1.org```, su directorio base será /var/www/web1 y contendrá una página llamada index.html, donde sólo se verá una bienvenida a la página web1.

    - El nombre de dominio del primero será ```www.web2.org```, su directorio base será /var/www/web2 y contendrá una página llamada index.html, donde sólo se verá una bienvenida a la página web2.

Crear carpetas, archivos y dar permisos:

```console
mkdir /var/www/web1
mkdir /var/www/web2
echo "<h1>Bienvenido a la web1.</h1>" > /var/www/web1/index.html
echo "<h1>Bienvenido a la web2.</h1>" > /var/www/web2/index.html
chown -R www-data: /var/www/web1/
chown -R www-data: /var/www/web2/
```

Luego nos iremos a /etc/nginx/sites-available para crear los sitios virtuales.

```console
cd /etc/nginx/sites-available/
cp default web1
cp default web2
vi web1
vi web2
cd /etc/nginx/sites-enabled/
ln -s /etc/nginx/sites-available/web1 .
ln -s /etc/nginx/sites-available/web2 .
systemctl reload nginx
```

**Este seria el documento de web1 y web2 (poner web2 donde pone web1):**

```nginx
server {
        listen 80;
        listen [::]:80;
        root /var/www/web1;
        index index.html index.htm index.nginx-debian.html;
        server_name www.web1.org;
        location / {
                try_files $uri $uri/ =404;
                }
}
```

Verificar: ```nginx -t```

**Comprobación:**
Configura la resolución estática en los clientes (```/etc/hosts```) y muestra el acceso a cada una de las páginas.

![3](https://github.com/estebancr1993/nginx/blob/main/imagenes/3.JPG)

![4](https://github.com/estebancr1993/nginx/blob/main/imagenes/4.JPG)

**F) Autentificación, Autorización y Control de acceso.**
```www.web1.org``` se puede acceder desde la red externa y la red interna.
```www.web2.org``` sólo se puede acceder desde la red interna.

**Modificamos nuestro sitio virtual de web2 (web1 acceden las dos redes):**

Añadir en location / { ```allow 192.168.3.0/24;``` y ```deny all;``` que significa que permita a esa red y deniegue a las demás.

```nginx
server {
        listen 80;
        listen [::]:80;
        root /var/www/web2;
        index index.html index.htm index.nginx-debian.html;
        server_name www.web2.org;
        location / {
                try_files $uri $uri/ =404;
                allow 192.168.3.0/24;
                deny all;
        }
}
```

**Verificar fichero y recargar servicio.**

**COMPROBACIÓN**

![5](https://github.com/estebancr1993/nginx/blob/main/imagenes/5.JPG)

![6](https://github.com/estebancr1993/nginx/blob/main/imagenes/6.JPG)

**G) Autentificación, Autorización y Control de acceso.**

```www.web1.org``` contiene un directorio llamado privado.

Configurar una autentificación básica. Sólo puede acceder usuarios válidos.

Primero...

Comprobamos que ```apache2-utils``` se encuentra instalado para poder usar **htpasswd** .

```console
apt policy apache2-utils
apt install apache2-utils
```

Creamos el directorio privado

```console
mkdir /var/www/web1/privado
chown -R www-data: /var/www/web1/ 
```

Creamos usuarios

Comenzamos creando un archivo que almacene los usuarios y contraseñas. Usaremos la utilidad ***htpasswd***.


```console
cd /etc/nginx
mkdir claves
htpasswd -c /etc/nginx/claves/.htpasswd esteban
# la opción -c sólo usar la primera vez.
```

**Configuración del sitio virtual de www.web1.org**

Añadir loacation /privado y dentro ```auth_basic "Acceso denegado";``` y ```auth_basic_user_file 	/etc/nginx/claves/.htpasswd;```

```nginx
server {
        listen 80;
        listen [::]:80;
        root /var/www/web1;
        index index.html index.htm index.nginx-debian.html;
        server_name www.web1.org;
        location / {
                try_files $uri $uri/ =404;
                }
location /privado {
		auth_basic           	"Acceso denegado";
    		auth_basic_user_file 	/etc/nginx/claves/.htpasswd; 
	}
}
```

**Verificar fichero y recargar servicio.**

**Comprobación**

![7](https://github.com/estebancr1993/nginx/blob/main/imagenes/7.JPG)

![7-2](https://github.com/estebancr1993/nginx/blob/main/imagenes/7-2.JPG)


**H) Autentificación, Autorización y Control de acceso.**
```www.web1.org``` contiene un directorio llamado privado.
Desde la red externa pide autorización y desde la red interna NO.

Debemos añadir lo siguiente al sitio virtual de web1, siguiendo el ejercicio anterior:

```nginx
server {
        listen 80;
        listen [::]:80;
        root /var/www/web1;
        index index.html index.htm index.nginx-debian.html;
        server_name www.web1.org;
        location / {
                try_files $uri $uri/ =404;
                }
location /privado {
                satisfy any; #Al poner  any accederá si se cumple alguna de las reglas.
                allow 192.168.3.0/24;
                deny all;
                auth_basic              "Acceso denegadi";
                auth_basic_user_file    /etc/nginx/claves/.htpasswd;
        }
}
```

**Verificar fichero y recargar servicio.**

**Comprobación:**

![8](https://github.com/estebancr1993/nginx/blob/main/imagenes/8.JPG)


**I) Seguridad.**
Configurar el sitio virtual ```www.web1.org``` para que el acceso sea seguro.

Crear directorio donde guardar los certificados.

```console
mkdir /etc/nginx/certificados
```

Instalar el paquete para poder crear los certificados.

```console
sudo apt install openssl ca-certificates
```

Generamos clave privada (dentro de la carpeta certificados):

```openssl genrsa -out web1.key 2048```

Generamos el Certificado (dentro de la carpeta certificados):

```openssl req -new -key web1.key -out web1.csr```

Firmamos el certificado (dentro de la carpeta certificados):

```openssl x509 -req -days 365 -in web1.csr -signkey web1.key -out web1.crt```

Y la añadimos al sitio virtual web1 con ```ssl_certificate /etc/nginx/certificados/web1.crt;``` y ```ssl_certificate_key /etc/nginx/certificados/web1.key;```
y hacemos que no sea neceario poner https:// (redireccionamiento)

```nginx
server {
	listen 443 ssl;
  listen [::]:443 ssl;
  
        ssl_certificate /etc/nginx/certificados/web1.crt;
        ssl_certificate_key /etc/nginx/certificados/web1.key;
        root /var/www/web1;

        index index.html index.htm index.nginx-debian.html;
	
        server_name www.web1.org;

	
	location / {
            try_files $uri $uri/ =404;
        }

	location /privado {
                auth_basic              "Acceso denegado";
                auth_basic_user_file    /etc/nginx/claves/.htpasswd;
                satisfy any;
                allow 192.168.3.0/24;
                deny all;
        }
}
server {
	listen 80;
	listen [::]:80;
	server_name www.web1.org;
	return 301 https://www.web1.org;  # Mediante el código de estado 301, el servidor redirige todas las solicitudes a una determinada fuente URL en el nuevo destino URL.
}
```

**Verificar fichero y recargar servicio.**

**COMPROBACIÓN**

![9](https://github.com/estebancr1993/nginx/blob/main/imagenes/9.JPG)

---

[INICIO](https://github.com/estebancr1993/nginx/blob/main/README.md)
