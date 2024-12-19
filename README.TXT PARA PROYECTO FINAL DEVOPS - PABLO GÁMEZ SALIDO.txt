README.TXT PARA PROYECTO FINAL DEVOPS - PABLO GÁMEZ SALIDO

Aquí voy a explicar paso por paso toda la configuración que he implementado en mi proyecto final Devops and Cloud Computing.

Título del Proyecto: Migración y Despliegue Automatizado de WordPress en Google Cloud
Nombre y apellidos: Pablo Gámez Salido




Paso 1: Configuración Inicial en Google Cloud

Descripción
Este paso consiste en configurar el entorno inicial en Google Cloud, incluyendo:
- La creación del proyecto.
- Habilitación de APIs necesarias.
- Configuración de facturación y alertas.
- Revisión y asignación de permisos.

Pasos Realizados

1. Acceso a Google Cloud Console
- Accedemos a https://console.cloud.google.com e iniciamos sesión con nuestra cuenta de Google.
- Verificamos el acceso al panel principal.

2. Creación del Proyecto
- Creamos un nuevo proyecto llamado "Proyecto Final DevOps".
- El ID del proyecto se genera automáticamente.
- Configuración de ubicación predeterminada.

3. Habilitación de APIs Necesarias
- Se habilita las siguientes APIs y se documenta su propósito:
  - Kubernetes Engine API: Para gestionar clústeres de Kubernetes y desplegar aplicaciones contenerizadas.
  - Cloud SQL Admin API: Para gestionar bases de datos relacionales como MySQL y PostgreSQL en Google Cloud.
  - Stackdriver Monitoring API: Para obtener métricas, alertas y monitorización de los recursos desplegados.

4. Configuración de Facturación
- Vinculamos una cuenta de facturación al proyecto.
- Configuramos alertas para controlar el gasto del crédito gratuito ($300):
  - 30%, 50%, 70%, 100% del presupuesto: Se configuraron notificaciones por correo electrónico.
- Nombre del presupuesto: "Control de Crédito gratis 300$".

5. Revisión y Asignación de Permisos
- Accedemos a "IAM y administración > Permisos".
- Verificamos que la cuenta principal tiene el rol de *Propietario*.
- Añadimos un usuario con el rol de *Editor* para gestionar recursos sin acceso a la facturación.
- Diferencia entre roles destacados:
  - Propietario: Acceso total, incluida la facturación.
  - Editor: Modifica recursos, pero no gestiona facturación.
  - Lector: Solo observa, sin permisos para cambiar recursos.

Resultado Esperado
Un proyecto de Google Cloud completamente configurado con:
- APIs habilitadas.
- Facturación vinculada y controlada mediante alertas.
- Permisos asignados según necesidades del equipo.

Notas Finales
Este paso asegura que todo el entorno inicial esté listo para los siguientes pasos, garantizando seguridad, control de costos y configuraciones necesarias para el despliegue de los servicios.


Paso 2: Migración de WordPress desde Hostinger

Descripción
Este paso consiste en exportar los datos y archivos del sitio WordPress alojado en Hostinger, organizarlos y prepararlos para el despliegue en Google Cloud.

Pasos Realizados

1. Exportar la Base de Datos
- Ingresamos a phpMyAdmin desde el panel de Hostinger.
- Seleccionamos la base de datos vinculada al sitio WordPress.
- Exportamos la base de datos con las siguientes configuraciones:
  - Método: Rápido.
  - Formato: SQL.
- Descargamos el archivo SQL para usarlo en el despliegue.

2. Descargar Archivos del Sitio
- Accedimos al Administrador de Archivos de Hostinger.
- Descargamos todo el contenido del directorio raíz del sitio (public_html) como un archivo ZIP.
- Incluimos:
  - La carpeta `wp-content` (temas, plugins, subidas).
  - Archivos principales como `wp-config.php`.

3. Preparar los Archivos para el Despliegue
- Extraemos el archivo ZIP y organizamos los archivos en una carpeta llamada `wordpress`.
- Verificamos los archivos importantes:
  - wp-content: Contiene recursos del sitio.
  - wp-config: Archivo de configuración con las credenciales de la base de datos.
- Revisión del archivo `wp-config` para identificar los valores necesarios:
  - DB_NAME: Nombre de la base de datos.
  - DB_USER: Usuario de la base de datos.
  - DB_PASSWORD: Contraseña de la base de datos.
  - DB_HOST: Servidor de la base de datos.

Resultado Esperado
Archivos y base de datos de WordPress organizados y listos para su despliegue en Google Cloud.

Notas Finales
Este paso asegura que los datos del sitio WordPress están preparados para ser configurados y desplegados en un entorno cloud.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

Paso 3: Docker con WordPress y MySQL
Descripción
En este proyecto, usamos Docker para crear un entorno local con WordPress y una base de datos MySQL. Esto nos permite replicar de forma sencilla un entorno de servidor en cualquier máquina, sin preocuparnos por dependencias específicas.

Parte I: Configuración de Docker | Archivos clave: Docker Compose
- Utilizamos un archivo llamado docker-compose.yml para definir y gestionar múltiples contenedores de forma sencilla.
- Este archivo define dos servicios principales 
  - db: El servicio de base de datos MySQL.
  - wordpress: El servicio de WordPress que conecta con la base de datos.
  - Estructura básica de docker-compose.yml :
version: '3.8'

services:
  db:
    image: mysql:5.7
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: root_password
      MYSQL_DATABASE: wordpress_db
      MYSQL_USER: wordpress_user
      MYSQL_PASSWORD: wordpress_pass
    volumes:
      - db_data:/var/lib/mysql

  wordpress:
    image: wordpress:latest
    ports:
      - "8080:80"
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_USER: wordpress_user
      WORDPRESS_DB_PASSWORD: wordpress_pass
      WORDPRESS_DB_NAME: wordpress_db

volumes:
  db_data:
 
Explicación paso a paso:

version: '3.8': Define la versión de Docker Compose que usamos.

Servicios:

db: Configura el servicio de base de datos MySQL.
image: mysql:5.7: Indica que se usará la imagen oficial de MySQL versión 5.7.
restart: always: Asegura que el contenedor se reinicia si falla.
environment: Define variables de entorno para configurar MySQL.
MYSQL_ROOT_PASSWORD: Contraseña del usuario root.
MYSQL_DATABASE: Nombre de la base de datos a crear.
MYSQL_USER: Usuario para acceder a la base de datos.
MYSQL_PASSWORD: Contraseña del usuario.
volumes: Permite persistir los datos de la base de datos fuera del contenedor.
wordpress: Configura el servicio de WordPress.
image: wordpress:latest: Usa la última versión de WordPress oficial.
ports: "8080:80": Expone el puerto 80 del contenedor como 8080 en nuestra máquina local.
environment: Conecta WordPress con la base de datos.
Volúmenes:

db_data: Permite guardar los datos de MySQL en la máquina anfitriona para evitar que se pierdan al detener el contenedor.

Parte 2: Preparación del entorno
- Paso 1: Crear el archivo docker-compose.yml
	- Guardamos el contenido anterior en un archivo con extensión .yml.
	- Iniciar los servicios con Docker Compose: docker-compose up -d    (up: Inicia los contenedores.-d: Lo hace en segundo plano.)

Parte 3: Verificar que los servicios funcionan:
	- Accedimos al sitio en http://localhost:8080 para comprobar que WordPress estaba disponible.

Parte 4: Personalización del entorno: Edición de wp-config.php
	- Este archivo conecta WordPress con la base de datos. Añadimos configuraciones personalizadas:
	- define( 'DB_NAME', 'wordpress_db' );
          define( 'DB_USER', 'wordpress_user' );
          define( 'DB_PASSWORD', 'wordpress_pass' );
          define( 'DB_HOST', 'db' );

	- Después de editar el archivo, reiniciamos los contenedores: docker-compose restart

Parte  6: Restricciones de Hostinger:
	- Cada base de datos debía estar asociada a un usuario único.
	- No se permitía crear bases de datos adicionales desde el contenedor.

Parte 7: Decisión:
	- Configurar WordPress vacío y utilizar herramientas como UpdraftMigrator para migrar el contenido más adelante.

Part 8: Comandos Clave
	- Iniciar servicios:docker-compose up -d
	- Detener servicios:docker-compose down
	- Reiniciar servicios:docker-compose restart
	- Conectar a MySQL dentro del contenedor:docker exec -it <nombre-contenedor> mysql -u <usuario> -p

**Pasos para crear una imagen de Docker lista para subir a Google Cloud

1. Verificar que WordPress esté configurado correctamente
- Antes de crear la imagen definitiva, asegúrate de que WordPress local esté completamente configurado con:
  - Plugins instalados.
  - Temas configurados.
  - Archivos subidos.
  - Contenido actualizado.

2. Exportar la base de datos actualizada
- Accede al contenedor de MySQL y exporta la base de datos:
   docker exec -it mysql bash -c "mysqldump -u root -pexample wordpress > /tmp/wordpress.sql"
- Copia el archivo `wordpress.sql` al sistema local:
   docker cp mysql:/tmp/wordpress.sql ./wordpress.sql

3. Copiar el contenido de WordPress
- Copia los archivos del directorio `wp-content` del contenedor de WordPress:
   docker cp wordpress:/var/www/html/wp-content ./wp-content
- Verifica que los archivos estén completos y en la carpeta local.

4. Crear un archivo `Dockerfile`
- En el directorio del proyecto, crea un archivo llamado `Dockerfile`:
   nano Dockerfile
- Escribe lo siguiente en el archivo:
   FROM wordpress:latest

   # Copiar los archivos personalizados
   COPY wp-content /var/www/html/wp-content

   # Copiar la configuración de subida
   COPY uploads.ini /usr/local/etc/php/conf.d/uploads.ini

   # Instalar cliente de MySQL (opcional)
   RUN apt-get update && apt-get install -y mariadb-client

   # Exponer el puerto 80
   EXPOSE 80
   ```
- Guarda y cierra el archivo.

5. Crear la imagen de Docker
- Construye la imagen:
   docker build -t wordpress-migracion .
- Verifica que la imagen fue creada:
   docker images
- Deberías ver una imagen llamada `wordpress-migracion`.

6. Probar la imagen localmente (opcional)
- Detén los contenedores actuales:
   docker-compose down
- Ejecuta un contenedor desde la nueva imagen:
   docker run -d -p 8080:80 --name wordpress-prueba wordpress-migracion
- Verifica que el sitio funcione correctamente en [http://localhost:8080](http://localhost:8080).

----------------------------------------------------------------------------------------------------------------------------------------------------------------------

4 PASO: Configuración y Despliegue de WordPress en Google Cloud

1º Introducción a Google Cloud Platform:
Google Cloud Platform (GCP) es una plataforma de servicios en la nube que ofrece una variedad de herramientas y servicios para almacenar, ejecutar y administrar aplicaciones. En este proyecto, usamos GCP para desplegar un WordPress funcional y accesible en internet.
¿Por qué usar GCP?
	- Escalabilidad: Permite manejar tráfico variable.
	- Integraciones: Compatible con una amplia gama de servicios.
	- Seguridad: Ofrece medidas avanzadas para proteger la infraestructura.

2º Creación del Proyecto en GCP
Configuración Inicial:
	- Accedemos a la consola de Google Cloud en console.cloud.google.com.
	- Creamos un proyecto nuevo:
		- Nombre: proyecto-wordpress.
		- ID del proyecto: Generado automáticamente.

3. Configuración de la Instancia de Máquina Virtual
Creación de la VM
	- Navegamos a "Compute Engine" > "Instancias de VM" y hacemos clic en "Crear Instancia".
	- Configuramos los parámetros:
		- Nombre: 
		- Región: Elegimos una cercana a nuestros usuarios.
		- Tipo de máquina: e2-medium (2 vCPU, 4 GB RAM).
		- Imagen del sistema operativo: Debian o Ubuntu.
		- Disco de arranque: 10 GB SSD.
		- Reglas de firewall: Permitimos HTTP y HTTPS.
Conexión a la VM
- Usamos SSH desde la consola de GCP o una herramienta como PuTTY
- Creamos una clave privada con PuTTYgen
- Nos dirigimos al apartado de metadatos de Compute Engine --> Claves SSH y metemos el texto generado.
- Nos dirigimos a PuTTY y metemos la clave secreta y nos logeamos con el nombre que hayamos puesto.

Despliegue del Sitio WordPress: Descarga y Configuración de WordPress
Descargamos WordPress:
cd /var/www/html
sudo wget https://wordpress.org/latest.tar.gz
sudo tar -xvzf latest.tar.gz
sudo mv wordpress/* .
sudo rm -rf wordpress latest.tar.gz

onfiguramos los permisos:
sudo chown -R www-data:www-data /var/www/html
sudo chmod -R 755 /var/www/html

Editamos el archivo wp-config.php:

Configuración de Base de Datos
Accedemos a MySQL:
sudo mysql -u root -p

Creamos una base de datos para WordPress:
CREATE DATABASE wordpress_db;
CREATE USER 'wp_user'@'localhost' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON wordpress_db.* TO 'wp_user'@'localhost';
FLUSH PRIVILEGES;
EXIT;


Configuración de Dominio: Vinculación del Dominio
Configuramos el dominio en el proveedor de DNS (Hostinger):
- Apuntamos los registros A y CNAME a la IP externa de nuestra VM
- Aseguramos que el dominio apunte correctamente.

Verificamos la configuración:
- Abrimos el dominio en un navegador.
- Deberíamos ver el sitio de WordPress.

Migración del Contenido:
- Utilizamos el plugin UpdraftMigrator para migrar contenido desde el sitio actual en Hostinger.
- Pasos:
	- Instalamos el plugin en ambos sitios (local y remoto).
	- Creamos un respaldo en el sitio original.
	- Restauramos el respaldo en el nuevo sitio en GCP.
--------------------------------------------------------------------------------------------------------------------------------------------------------
 
Pso 4: Subida de archivos y preparación del servidor

1º Crear un Bucket en Google Cloud Storage
- Ve a la consola de Google Cloud Google Cloud Console.
- Navega a Storage > Buckets en el menú lateral.
- Haz clic en el botón Crear Bucket.
- Configura el Bucket:
	- Nombre del Bucket: Escoge un nombre único
	- Clase de Almacenamiento: Selecciona Standard
	- Ubicación: Elige una región cercana a tu instancia: europe-west1
	- Deja las demás configuraciones por defecto.
- Haz clic en Crear.

2º Subir los archivos al Bucket
- Mediante el bucket lo puedes subir por la web. (Subir archivos)

3º Transferir los Archivos al Servidor
Una vez los archivos estén en el bucket, procederemos a moverlos desde el bucket a la instancia de Google Cloud.
- Conéctate a tu instancia de Google Cloud a través de SSH:
	- Desde la consola de Google Cloud, ve a Compute Engine --> Instancias de VM. --> Haz clic en SSH en tu instancia.
- Usa el comando gsutil dentro de la instancia para descargar los archivos desde el bucket al servidor:
gsutil cp -r gs://NOMBRE_DEL_BUCKET /ruta/destino  EJEMPLO: gsutil cp -r gs://descargamigracionpablo /home/migracionwordpress
- Una vez descargados, verifica que los archivos están en la ruta correcta:
ls /home/migracionwordpress

4º Descomprimir el Archivo public.HTML.zip 
- Accede a tu instancia de Google Cloud mediante SSH: Si no estás conectado aún, puedes usar la consola SSH desde Google Cloud o tu cliente (como PuTTY).
- Navega hasta el directorio donde subiste el archivo: Normalmente, lo subiste al directorio
- Descomprime el archivo: Usa el comando unzip para descomprimir: unzip public_html.zip -d /home/migracionwordpress/descargamigracionpablo/descargasmigracion
- Verifica los archivos: Asegúrate de que los archivos se descomprimieron correctamente: ls /home/migracionwordpress/descargamigracionpablo/descargasmigracion

----------------------------------------------------------------------------------------------------------------------------
Paso 5: configuración de tu servidor y WordPress para asegurarnos de que todo funcione correctamente
1º Instalar Docker y Docker Compose en tu instancia
- Conéctate a tu instancia por SSH: Ya que estás usando PuTTY o cualquier cliente SSH, asegúrate de estar dentro de tu instancia.
- Actualiza los paquetes existentes: sudo apt update && sudo apt upgrade -y
- Instala Docker: Ejecuta los siguientes comandos:
sudo apt install -y apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt update
sudo apt install -y docker-ce
- Verifica que Docker esté funcionando: sudo systemctl status Docker
- Instala Docker Compose:
sudo curl -L "https://github.com/docker/compose/releases/download/2.33.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose --versión
- Agrega tu usuario al grupo de Docker: sudo usermod -aG docker $USER (Importante: cerrar y volver a abrir la sesión SSH para que los cambios surtan efecto.)
- Iniciar los servicios con Docker Compose: Ejecuta el siguiente comando desde la carpeta donde está tu archivo docker-compose.yml:
- Verificar los contenedores en ejecución: Asegúrate de que ambos contenedores están en ejecución: docker ps

2º Abrir el puerto en Google Cloud
- Acceder a la consola de Google Cloud
- Ir a la sección de reglas de firewall: En el menú lateral, ve a Redes VPC --> Reglas de firewall
- Crear una nueva regla de firewall
- Configura los siguientes campos:
	- Nombre: Algo descriptivo
	- Red: Selecciona la red donde se encuentra tu instancia
	- Prioridad: Déjalo como está
	- Tráfico coincidente
		- Dirección del tráfico: Entrada.
		- Acción en coincidencia: Permitir.
	- Destino: Todos los instancias en la red.
	- Filtros de origen:
		- Rango de IP de origen: 0.0.0.0/0
	- Protocolos y puertos
		- Activa la opción -->Especificar protocolos y puertos.
		- Selecciona "TCP" y especifica el puerto 8080

23º Acceder a tu aplicación
- Abre tu navegador y accede a la dirección:
- http://34.175.101.0:8080

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

Paso 6: Migración utilizando UpdraftMigrator

1º Preparar el sitio de origen (Hostinger)
- Accede al panel de WordPress en Hostinger.
- Ve a UpdraftPlus --> Migrator
- Crea un backup completo del sitio
-Una vez creado, utiliza la opción de migrar/cargar en otro sitio para generar el enlace de migración.

2º Preparar el sitio de destino (Google Cloud):
- Asegúrate de estar en el panel de WordPress de tu servidor en Google Cloud.
- Ve a UpdraftPlus --> Migrator.
- Usa el enlace generado desde el sitio de origen para iniciar la migración

3º Ejecutar la migración:
- Sigue las instrucciones para importar todos los datos del sitio original a tu WordPress en Google Cloud.
- Espera a que se complete el proceso (puede tardar dependiendo del tamaño del sitio).

4º Verificar la migración:
-Una vez completada la migración, navega por tu sitio en el servidor de Google Cloud para verificar que todo esté funcionando correctamente.
- Asegúrate de que el contenido, los plugins, y los temas estén en su lugar.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------
Paso 7: Documentación del Balanceador de Carga y Autoscaling en Google Cloud

1º Creación de una Plantilla de Instancia
- Vamos a Compute Engine --> Plantillas de instancia.
- Hacemos clic en Crear plantilla de instancia.
- Configuramos:
	- Nombre: web-novusnoc-pablo
	- Tipo de máquina: `e2-medium`.
	- Imagen del sistema operativo: Ubuntu 22.04.
	- Discos: Tamaño de 10 GB.
	- Red: Habilitamos el tráfico HTTP y HTTPS.
Hacemos clic en Crear.

2º Configuración del Grupo de Instancias Administrado
- Vamos a Compute Engine --> Grupos de instancias.
- Hacemos clic en Crear grupo de instancias.
- Configuramos:
	- Nombre:grupo-novusnoc-pablo
	- Ubicación: Seleccionamos Zona única
	- Plantilla de instancia: Seleccionamos web-novusnoc-pablo
	- Tamaño inicial: 1.
- Activamos Escalado automático:
	- Métrica: Uso de CPU.
	- Umbral de activación: 75%.
	- Tamaño mínimo: 1.
	- Tamaño máximo: 3.
- Hacemos clic en Crear.

3º Configuración del Balanceador de Carga
- Vamos a Network Services --> Balanceo de carga.
- Hacemos clic en Crear balanceador de carga y seleccionamos HTTP y HHTPS Load Balancer.
- Configuramos:
- Backend:
	- Seleccionamos el grupo de instancias `grupo-novusnoc-pablo`
	- Configuramos los puertos (80 para HTTP).
	- Creamos una verificación de estado:
	- Tipo: HTTP
	- Puerto: 80
	-  Ruta: `/`.
- Frontend:
	- Creamos una regla de reenvío para el puerto 80.
	- Seleccionamos una nueva dirección IP global.
	- Hacemos clic en Crear.

¿Qué es la verificación de estado?:
La verificación de estado es un mecanismo que usa el balanceador de carga para verificar que las instancias estén funcionando correctamente.
- Cómo funciona:
  - Envía solicitudes HTTP/HTTPS a una ruta especificada.
  - Si la instancia responde con un código de estado exitoso (como 200), se considera saludable.
- Propósito:
  - Asegurar que solo las instancias saludables reciban tráfico.
  - Retirar temporalmente las instancias que no pasan la verificación.

4º Pruebas de Autoscaling
- Simulamos carga en la instancia principal:
	- Instalamos herramientas de prueba:
     sudo apt install stress
   - Generamos carga:
     stress --cpu 4 --timeout 300

- Observamos cómo se crean nuevas instancias en Compute Engine --> Grupos de instancias.
- Verificamos que el balanceador distribuye el tráfico entre las instancias activas.
- Confirmamos que las instancias adicionales se eliminan cuando la carga disminuye.

*Conclusión*
Con el balanceador de carga y el autoscaling configurados, el sistema puede manejar aumentos de tráfico de manera eficiente y reducir costos cuando la carga disminuye. Este proceso asegura escalabilidad y redundancia para aplicaciones web.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------


Paso 8 despliegue de WordPress en Google Cloud con kubernetes:

1. Configurar el clúster en Google Kubernetes Engine (GKE)

Paso 1: Crear un clúster de GKE
1. Accede a la consola de Google Cloud y ve a la sección de "Kubernetes Engine".
2. Haz clic en Crear Clúster.
3. Configura el clúster:
   - Nombre del clúster: `wordpress-cluster`
   - Tipo de clúster: Estándar
   - Zona: `europe-west1-b`
   - Nodos:
     - Tipo de máquina: `e2-medium`
     - Número de nodos: 2
4. Guarda y espera a que se cree el clúster.

Paso 2: Configurar `kubectl`
1. En la consola de Cloud Shell, ejecuta:
   gcloud container clusters get-credentials wordpress-cluster --zone=europe-west1-b --project=proyecto-final-devops-pablo
2. Verifica que `kubectl` esté configurado correctamente:
   kubectl get nodes

2. Configuración de la Base de Datos en Cloud SQL

Paso 1: Crear una instancia de Cloud SQL
1. Ve a la sección de Cloud SQL en Google Cloud.
2. Haz clic en Crear Instancia y selecciona MySQL.
3. Configura:
   - Nombre de la instancia: `wordpress-sql`
   - Contraseña de root: `example`
   - Región: `europe-west1`
4. Guarda y espera a que se cree la instancia.

Paso 2: Crear una base de datos y usuario
1. Accede a la instancia de `wordpress-sql`.
2. Crea la base de datos:
   CREATE DATABASE wordpress;
3. Crea un usuario y dale permisos:
   CREATE USER 'pablo-new-bbdd'@'%' IDENTIFIED BY 'pablo2024';
   GRANT ALL PRIVILEGES ON wordpress.* TO 'pablo-new-bbdd'@'%';

Paso 3: Configurar la conexión autorizada
1. Ve a la configuración de la instancia y habilita la red:
   - Agrega tu rango de IP pública o usa `0.0.0.0/0` para permitir el acceso desde cualquier lugar (no recomendado para producción).

3. Configuración del Proxy de Cloud SQL en Kubernetes

Paso 1: Crear la clave de la cuenta de servicio
1. Ve a la sección **IAM y Administración > Cuentas de servicio**.
2. Crea una cuenta de servicio:
   - Nombre: `sql-proxy`
   - Rol: **Cloud SQL Cliente**
3. Descarga el archivo JSON de la clave.

Paso 2: Subir la clave al clúster
1. En la consola de Cloud Shell, sube el archivo:
   kubectl create secret generic cloudsql-instance-credentials --from-file=key.json=cloudsql-key.json

Paso 3: Configurar el proxy en el despliegue
1. Modifica el manifiesto de Kubernetes (`wordpress-deployment.yaml`) para incluir el contenedor del proxy:
   containers:
   - name: cloud-sql-proxy
     image: gcr.io/cloudsql-docker/gce-proxy:1.33.3
     command: ["/cloud_sql_proxy", "-instances=proyecto-final-devops-pablo:europe-west1:wordpress-sql=tcp:3306", "-credential_file=/secrets/cloudsql/key.json"]
     volumeMounts:
     - name: cloudsql-instance-credentials
       mountPath: /secrets/cloudsql
       readOnly: true

2. Añade el volumen correspondiente:
   volumes:
   - name: cloudsql-instance-credentials
     secret:
       secretName: cloudsql-instance-credentials


4. Despliegue de WordPress en GKE

Paso 1: Crear el manifiesto del despliegue
1. Crea un archivo `wordpress-deployment.yaml` con el siguiente contenido:
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: wordpress
   spec:
     replicas: 2
     selector:
       matchLabels:
         app: wordpress
     template:
       metadata:
         labels:
           app: wordpress
       spec:
         containers:
         - name: wordpress
           image: wordpress:latest
           ports:
           - containerPort: 80
           env:
           - name: WORDPRESS_DB_HOST
             value: "127.0.0.1:3306"
           - name: WORDPRESS_DB_USER
             value: "pablo-new-bbdd"
           - name: WORDPRESS_DB_PASSWORD
             value: "pablo2024"
           - name: WORDPRESS_DB_NAME
             value: "wordpress"
         volumes:
         - name: cloudsql-instance-credentials
           secret:
             secretName: cloudsql-instance-credentials

2. Aplica el manifiesto:
   kubectl apply -f wordpress-deployment.yaml

Paso 2: Verificar el despliegue
1. Revisa los pods:
   kubectl get pods
2. Asegúrate de que estén en estado `Running`.

5. Configuración del Balanceador de Cargas

Paso 1: Crear el servicio
1. Crea un archivo `wordpress-service.yaml`:
   apiVersion: v1
   kind: Service
   metadata:
     name: wordpress-service
   spec:
     selector:
       app: wordpress
     ports:
     - protocol: TCP
       port: 80
       targetPort: 80
     type: LoadBalancer

2. Aplica el servicio:
   kubectl apply -f wordpress-service.yaml

Paso 2: Obtener la IP del balanceador
1. Revisa el servicio:
   kubectl get service wordpress-service

2. Copia la IP externa proporcionada.

6. Pruebas Finales y Ajustes

Paso 1: Acceder a WordPress
1. Accede a la URL del balanceador de cargas (IP externa).
2. Completa la configuración inicial de WordPress.

Paso 2: Verificar la conexión con la base de datos
1. Inicia sesión en el contenedor de WordPress:
   kubectl exec -it $(kubectl get pods -l app=wordpress -o jsonpath='{.items[0].metadata.name}') -- bash

2. Usa el cliente MySQL para conectarte:

   mysql -h 127.0.0.1 -u pablo-new-bbdd -ppablo2024 wordpress
 
Paso 3: Ajustes finales
1. Verifica los logs:
   kubectl logs -l app=wordpress

2. Asegúrate de que el sitio funcione correctamente y realiza pruebas de carga.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------


















