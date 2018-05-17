# vShinobi 

Shinobi es la solución de CCTV de código abierto escrita en Node.JS. Diseñado con un sistema de cuentas múltiples, Streams por WebSocket y guardados a WebM. Shinobi puede grabar con cámaras IP y cámaras locales. Es usado en conjunto con la plataforma vOne para visualizar las camaras en streaming.  

## Aspectos claves

Para obtener una lista actualizada de características, visite el sitio web oficial. http://shinobi.video/features

- Visor de lapso de tiempo (Vea el valor de horas de filmación en unos minutos)
- Autenticación de 2 factores
- Derrota el límite de transmisión impuesto por los navegadores
  - Con Base64 (tipo de secuencia) y modo JPEG (opción)
- Graba cámaras IP y cámaras locales
- Streams por WebSocket, HLS (incluye audio) y MJPEG
- Guardar en WebM y MP4
  - Puede guardar audio
- Eventos de inserción: cuando finaliza un video, aparecerá en el panel sin una actualización
- Detección de movimiento (similar a la detección de zona ZoneMinder)
  - Representado por una Motion Guage en cada monitor
- Notificaciones "sin movimiento"
- 1 proceso para que cada cámara haga ambas cosas, grabación y transmisión
- Línea de tiempo para ver eventos y videos de movimiento
- Subcuentas con permisos
  - Visualización del monitor
  - Edición de monitores
  - Eliminación de video
  - Claves de API separadas para la cuenta secundaria
- Los filtros Cron se pueden configurar en base a la cuenta maestra
- Stream Analyzer incorporado (FFprobe GUI)
- Monitorear grupos
  - La instantánea (cgi-bin) debe estar habilitada en la configuración del monitor
- Control de cámaras desde la interfaz
- API
  - Obtener videos
  - Obtener monitores
  - Cambiar modos de monitor: deshabilitado, mirar, grabar
  - Incrustar secuencias
- Desarrollado con Google Material Design Lite, jQuery y Bootstrap


## Instalación

A continuación se muestran los pasos para instalar el aplicativo.


### Paso 1

Instalar gitel manejador de control de versiones el cual necesitas para poder bajar el código del repositorio remoto, así como para incluir nuevos cambios. 

Para instalar git ingresa en la terminal de linux:

    sudo apt-get install git


> NOTA IMPORTANTE: ejecuta con sudo solo los comandos indicados. No loguarse como root para ejecutar los comandos. Utiliza siempre el usuario normal pero pidiendo permisos con sudo cuando sea necesario.

### Paso 2
Instalar ffmpeg y otras librerias para poder grabar, convertir (transcodificar) y hacer streaming de audio y vídeo.


Para instalarlas ingresa en la terminal de linux lo siguiente:

    sudo add-apt-repository ppa:jonathonf/ffmpeg-3
    sudo apt update && sudo apt install ffmpeg libav-tools x264 x265

### Paso 3
Instalar node. Node.js es una plataforma que te permite ejecutar codigo javascript del lado del servidor.

Para instalar node ingresa en la terminal de linux:

    sudo apt update
    sudo apt install curl gnupg2 -y
    curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
    sudo apt install nodejs -y
    sudo apt install npm -y

Prueba:

    node -v
    npm -v

Cada uno debería retornarte la versión instalada.

### Paso 4
Instalar MariaDB. MariaDB es un sistema de gestión de bases de datos derivado de MySQL


Para instalar ingresa en la terminal de linux:

    sudo apt install mariadb-server mariadb-client -y
    
    
> NOTA: Durante la instalacion te pedira ingresar la contraseña para el usuario **root** del gestor de base de datos.

Por defecto, MariaDB solo escucha las conexiones del localhost. Todo el acceso remoto al servidor está denegado por defecto. Si desea habilitar el acceso remoto para poder conectarse desde cualquier lado, ejecute los siguientes comandos:

    sudo nano /etc/mysql/mariadb.conf.d/50-server.cnf

Edite la siguiente linea

    bind-address = 127.0.0.1
    
    cambiarla a
    
    bind-address = 0.0.0.0
    
Guardar los cambios y reniciar el servicio

    sudo service mysql restart

Ya con este cambio tendriamos al gestor escuchando desde cualquier IP.
 
 > NOTA: Esta configuracion aplica si se desea tener separado la base de datos en un servidor y el aplicativo en otro. Si no es el caso y ambos estaran en el mismo servidor se puede omitir esta configuracion.
 

### Paso 5

Descargar vShinobi y configurar.

#### Clona el repositorio: debes tener acceso con tu usaurio de github

    mkdir /home/visionstudio/proyectos
    cd /home/visionstudio/proyectos
    git clone https://github.com/LuisGnzlz1/vShinobi.git
    cd vShinobi
    git checkout master
    
#### Otorgar los permisos respectivos a la carpeta

    chmod -R 755 /home/visionstudio/proyectos/vShinobi
    chmod -R o+w /home/visionstudio/proyectos/vShinobi


#### Ingresar en el siguiente directorio

    cd /home/visionstudio/proyectos/vShinobi/sql
    
#### Abrir MariaDB desde la terminal. Si la BD y el aplicativo esta en el mismo servidor ejecuta lo siguiente:

    mysql -u root -p
       
 > NOTA: Si te pide ingresar contraseña, colocas la del usuario **root** que pusiste durante la instalacion del gestor.

#### Procedemos a instalar la base de datos del vShinobi, corremos las siguientes lineas de comandos:

    source ./user.sql
    source ./framework.sql
    source ./default_data.sql

#### Salimos de la consola de MariaDB

    exit
      
#### Copiamos el archivo de configuracion del sistema y lo renombramos con otro nombre:

    cp /home/visionstudio/proyectos/vShinobi/conf.sample.json /home/visionstudio/proyectos/vShinobi/conf.json
    
#### Editamos el archivo de configuracion si nuestros parametros son distintos a los que trae incluido

    nano /home/visionstudio/proyectos/vShinobi/conf.json
    

### Paso 6

Instalamos las librerias de dependencia del proyecto.
En la terminal de linux colocamos:

    sudo npm install && npm install pm2 -g

Copiamos y renombramos el siguiente el archivo:

    cp /home/visionstudio/proyectos/vShinobi/super.sample.json /home/visionstudio/proyectos/vShinobi/super.json


### Paso 7

Iniciamos el vShinobi. 

En terminal de linux ejecutamos lo siguiente:

	cd /home/visionstudio/proyectos/vShinobi
	pm2 start camera.js
	pm2 start cron.js

Para ver nuestra IP:

    ifconfig
    
Abrimos la siguiente direccion en nuestro navegador **https://IP:8443** y accedemos con los siguientes credenciales:

<pre><code><b>Usuario:</b> visionstudio
<b>Password:</b> Passw0rd
</code></pre>

Para acceder al panel de administrador abrimos la siguiente direccion en nuestro navegador **https://IP:8443/super** y accedemos con los siguientes credenciales:


<pre><code><b>Usuario:</b> admin@visionstudio
<b>Password:</b> Passw0rd
</code></pre>
