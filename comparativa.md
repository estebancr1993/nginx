# Comparativa con Apache.

Vamos a comparar Apache y NGINX.

### Apache (Ventajas y desventajas)
Apache es la mejor opción para la mayoría de webmasters y desarrolladores. Funciona en múltiples sistemas operativos, incluyendo Windows, Unix y Linux.

**Sus ventajas:**
+ Excelente rendimiento para servir contenido dinámico.
+ Cargar y descargar muchos módulos de forma dinámica.
+ Una conexión por proceso.
+ Excelente soporte y documentación.
+ Ofrece el archivo de configuración por sitio .htaccess. 
+ Módulo mod_evasive y mod_security que proporciona una capa adicional de seguridad.

**Sus desventajas:**
+ No puede procesar una gran cantidad de solicitudes al mismo tiempo.
+ La visualización del contenido estático es más lenta si la comparamos con nginx.
+ Su configuración y administración no es apta para principiantes, si hablamos del servidor.
+ Puede ocasionar problemas de rendimiento en sitios web con mucho tráfico (más de 1.000.000 de solicitudes diarias).

### Nginx (Ventajas y desventajas)
Nginx está especialmente diseñado para sitios web con un tráfico muy alto.

**Sus ventajas:**
Arquitectura por eventos (sin bloqueos) que requiere menos CPU y menos memoria.
Muchas opciones que permiten optimizar el servidor y ofrecer contenido estático. Sirve el contenido estático 2.5 veces más rápido que Apache con menor consumo.
Trabaja excelentemente en entornos de multiprocesador.
Opción para prevenir ataques DDoS.

**Sus desventajas:**
No procesa contenido dinámico de forma nativa, debes configurarlo manualmente.
Pocos módulos disponibles.
Compatible cien por cien con Linux y Unix. Con Windows no es total.
NGINX no admite el archivo .htaccess de Apache.
Falta de herramientas de monitoreo de los registros, se limita a generar archivos de registro.

**Cuando usar:**

*Apache*
- En alojamientos compartidos.
- Si quieres una gran comunidad de soporte y los recursos que ofrece.
- Más fácil de configurar que Nginx, la mayoría de módulos vienen preconfigurados que manera efectiva.

*Nginx*
- Si usas un VPS o servidor dedicado y, cumples alguno de los siguientes requisitos.
- Manejas un sitio web de alto tráfico, además de con mucho contenido estático.
- Si quieres administrar el tráfico entrante y distribuirlo a servidores ascendentes más lentos.

---

## ¿Cuál utilizar?

>Al final, todo dependerá de lo que necesites. Apache, es una buena idea usarlo si necesitas mucha información. Por otro lado, si tu servidor web recibe mucho tráfico, es probable que necesites usar Nginx, pues se comporta mucho mejor en este aspecto. 
>
>Ambos servidores van a funcionar bien y van a mantener nuestra web segura. Ahora es ya cuestión de cada uno que elija el que mejor se adapte a sus necesidades.

---

[INICIO](https://github.com/estebancr1993/nginx/blob/main/README.md)
