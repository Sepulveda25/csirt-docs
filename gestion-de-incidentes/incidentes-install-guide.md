# Guía de Instalación de gestor de incidentes

descripcion


[Volver a documento raiz](https://gitlab.unc.edu.ar/csirt/csirt-docs/tree/master#csirt-docs)


## Tabla de Contenidos
  * [Virtualización de gestor de incidentes](#virtualización-de-gestor-de-incidentes)
  * [Instalacion de Sistema Operativo](#instalación-de-sistema-operativo)
  * [TheHive & Cortex Instalación - Opciones de Instalación](#thehive-cortex-instalación-opciones-de-instalación)
    * [Ansible](#ansible)
    * [Manual](#manual)

## Virtualización de gestor de incidentes

Instrucciones de creación de una maquina virtual por si se desea instalar el gestor de incidentes. 
Para la instalacion del gestor de incidentes, se utilizo debian 10.  

Esta guía se basa en el uso del entorno VMWare ESXI

#### Prerequisitos

* Tener un ISO de Debian 10 subido a algún datastore del servidor

### Creación de Maquina Virtual

1. En la sección *Virtual Machines* seleccionar *Create / Register VM* y en la ventana que aparece, *Next*.
2. Definir un nombre para la maquina virtual (e.g. gestor-incidentes) y selecciona la familia de sistema operativo *Linux*; version *Debian GNU/Linux 10 (64-bit)*
3. Seleccina el disco físico en donde se creara la maquina virtual con suficiente espacio para crear el disco duros virtual.
4. Configuracion de componentes:
    * CPU: 8
    * Memory: 8 GB
    * Hard Disk: 60 GB - Thin Provisioned
    * Network Adapter : *Vlan de manejo de CSIRT*
    * CD/DVD Drive 1: *Datastore ISO file* (seleccionar el iso de Debian 10)
5. Finaliza la creación de la maquina virtual.

## Instalación de Sistema Operativo

Se eligió como sistema operativo Debian 10 GNU/Linux 64-bits. Pasos de instalación:

1. Encender la maquina y bootear a un medio booteable de debian-10.x.x-amd64-netinst.iso
    * En el caso de estar en un entorno virtualizado el archivo .iso debe estar cargado al servidor. En el caso de tener una maquina dedicada el .iso debe ser grabado a un pen drive por ejemplo.
2. En el installer menu seleccionar **Graphical install**
3. Seleccionar **English**.
4. En la selección de ubicacion, seleccionar **Argentina** (buscar en *Other*).
5. En la configuración de locale, seleccionar **en_US.UTF-8**.
6. En la configuración de teclado, seleccionar **Spanish (Latin American)**.
7. Si no se puede configurar la red con DHCP, selecionar **Configure network manually**.
8. Ingresar la **direccion IP** que debe tener el servidor, la **mascara de red** y la **dirección IP gateway**.
9. Ingresar las direcciones IP de los **servidores DNS**.
10. Ingresar el **hostname** del sistema (e.g. *thehive-debian*).
11. Ingresar un **nombre de dominio**.
12. Ingresar una **contraseña** para el usuario *root*.
13. Ingresar un **nombre real** y luego un **nombre de usuario** para el usuario principal (debe ser *thehive* para no tener que realizar configuraciones adicionales).
14. Ingresar una **contraseña** para el usuario *thehive*.
15. Partición de disco: seleccionar **Guided - use entire disk**.
16. Seleccionar el único disco disponible para particionar, o sea **/dev/sda**.
17. Seleccionar **All files in one partition**, confirmar los cambios y seleccionar **Yes** para escribir los cambios al disco.
18. Seleccionar **No** cuando pregunta si se desea escanear otro CD o DVD.
19. Seleccionar **Argentina** como el Debian archive mirror y luego seleccionar el servidor que esta seleccionado por defecto. No ingresar ningún HTTP proxy.
20. Seleccionar **No** cuando pregunta si se quiere participar en la encuesta de uso de paquetes.
21. En la selección de software solamente dejar seleccionado **SSH Server** y **standard system utilities**.
22. Seleccionar **Yes** para instalar el GRUB boot loader al master boot record, luego seleccionar el disco duro principal, o sea **/dev/sda**.
23. Seleccionar continuar para finalizar la instalación.

Con esto ya se puede acceder al servidor debian por ssh y realizar el resto de la instalación.

### Configuración de usuario con sudo

Para que el usuario *thehive* pueda utilizar sudo, ingresar al usuario root e ingresar los siguientes comandos.

```bash
# Instalación de sudo
apt install sudo
# Agregar el usuario thehive a la lista de sudoers
usermod -aG sudo thehive
```

Luego cerrar la sesión e ingresar al usuario *thehive* para comprobar que se puede utilizar sudo para escalar privilegios.


## TheHive & Cortex Instalación - Opciones de Instalación

### Ansible

**Queres usar ansible? Link a repo, seguir instrucciones**

### Manual

En este sección se indica como instalar TheHive & Cortex. Ver instrucciones originales [aquí](https://github.com/TheHive-Project/TheHiveDocs/blob/master/installation/install-guide.md).

#### Requisitos

Se requiere tener instalado un sistema operativo de tipo Debian.

#### Elasticsearch

Instalar el paquete Elasticsearch proporcionado por Elastic:
```bash
# Instalación de clave PGP
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-key D88E42B4
# Instalación de clave PGP alternativa (si no funciona el comando anterior)
# wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
# Si te tira el error
# E: gnupg, gnupg2 and gnupg1 do not seem to be installed, but one of them is required for this operation
# Instalar gnupg con el siguiente comando:
# sudo apt-get install -y gnupg2
# Configuración de repositorio Debian
echo "deb https://artifacts.elastic.co/packages/5.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-5.x.list
# Instalar suporte para apt con https
sudo apt install apt-transport-https
# Instalación de Elasticsearch
sudo apt update && sudo apt install elasticsearch
```

El paquete Debian no inicia el servicio de forma predeterminada, para evitar que la instancia se una accidentalmente a un clúster, sin estar configurado adecuadamente.

##### Configuración

Editar `/etc/elasticsearch/elasticsearch.yml` y agregar las siguientes lineas:
```yaml
network.host: 127.0.0.1
cluster.name: hive
thread_pool.index.queue_size: 100000
thread_pool.search.queue_size: 100000
thread_pool.bulk.queue_size: 100000
```

###### Inicia el Servicio

Ahora que Elasticsearch está configurado, iniciarlo como un servicio y verifique si se está ejecutando:
```bash
sudo systemctl daemon-reload
sudo systemctl enable elasticsearch.service
sudo systemctl start elasticsearch.service
sudo systemctl status elasticsearch.service
```

El estado debe ser `active (running)`. Si no se está ejecutando, puede verificar el motivo en los logs:

```bash
sudo journalctl -u elasticsearch.service
```

Es probable que al iniciar el servicio de Elastic este falle porque no esta instalada ninguna version de Java en el servidor. Puede instalarlo con el siguiente comando:

Para debian:
```bash
sudo apt-get install openjdk-11-jre
```

Para Ubuntu server:
```bash
sudo apt install default-jre
```

#### TheHive

Para instalar usando los paquetes Debian, se usan los siguientes comandos:
```bash
echo 'deb https://dl.bintray.com/thehive-project/debian-stable any main' | sudo tee -a /etc/apt/sources.list.d/thehive-project.list
sudo apt-key adv --keyserver hkp://pgp.mit.edu --recv-key 562CBC1C
sudo apt-get update
sudo apt-get install thehive
```

Si el acceso al servidor `pgp.mit.edu` esta bloqueado el comando `sudo apt-key adv --keyserver hkp://pgp.mit.edu --recv-key 562CBC1C` fallara. En ese caso, en su lugar ejecutar el siguiente comando:

`curl https://raw.githubusercontent.com/TheHive-Project/TheHive/master/PGP-PUBLIC-KEY | sudo apt-key add -`

###### Inicia el Servicio

El único parámetro requerido para iniciar TheHive es la clave del servidor (`play.http.secret.key`). Esta clave se usa para autenticar cookies que contienen datos. Si TheHive se ejecuta en modo de clúster, todas las instancias deben compartir la misma clave.
Puede generar la configuración mínima con los siguientes comandos (suponen que se ha creado un usuario dedicado para TheHive, llamado `thehive`):
```bash
sudo mkdir /etc/thehive
(cat << _EOF_
# Secret key
# ~~~~~
# The secret key is used to secure cryptographics functions.
# If you deploy your application to several instances be sure to use the same key!
play.http.secret.key="$(cat /dev/urandom | tr -dc 'a-zA-Z0-9' | fold -w 64 | head -n 1)"
_EOF_
) | sudo tee -a /etc/thehive/application.conf
```

Luego para iniciar la aplicación como un servicio, usar los siguientes comandos:
```bash
sudo systemctl daemon-reload
sudo systemctl enable thehive.service
sudo systemctl start thehive.service
sudo systemctl status thehive.service
```

El estado debe ser `active (running)`. Si no se está ejecutando, puede verificar el motivo en los logs:
```bash
sudo journalctl -u elasticsearch.service
```

Se debe tener en cuenta que el servicio puede tardar un tiempo en iniciarse. Una vez que se inicia, se puede iniciar el navegador y conectarse a `http://YOUR_SERVER_ADDRESS:9000/`.

La primera vez que se conecte se deberá crear el esquema de la base de datos. Haga clic en "Migrate database" para crear el esquema de base de datos.

Una vez hecho esto, sera redirigido a la página para crear la cuenta del administrador.

**Advertencia**: en esta etapa, si se perdió la creación de la cuenta de administrador, no se podrá hacer a menos que elimine el índice de TheHive de Elasticsearch. En el caso de que se haya cometido un error, primero se debe averiguar cuál es el índice actual de TheHive ejecutando el siguiente comando en un host donde se encuentra el Elasticsearch DB utilizado por TheHive:
```bash
$ curl http://127.0.0.1:9200/_cat/indices?v
```

Los índices que usa TheHive siempre comienzan con `the_hive_` seguido de un número. Supongamos que la salida del comando es:
```bash
health status index       uuid                   pri rep docs.count docs.deleted store.size pri.store.size
yellow open   cortex_1    PC_pLFGBS5G2TNQYr4ajgw   5   1        609            6      2.1mb          2.1mb
yellow open   the_hive_13 ft7GGTfhTr-4lSzZw5r1DQ   5   1     180131            3     51.3mb         51.3mb
```

El índice utilizado por TheHive es `the_hive_13`. Para eliminarlo, ejecutar el siguiente comando:
```bash
$ curl -X DELETE http://127.0.0.1:9200/the_hive_13
```

Luego, vuelva a cargar la página o reinicie TheHive.

#### Cortex
Para instalar el paquete Cortex Debian, use los siguientes comandos:
```bash
echo 'deb https://dl.bintray.com/thehive-project/debian-stable any main' | sudo tee -a /etc/apt/sources.list.d/thehive-project.list
sudo apt-key adv --keyserver hkp: //pgp.mit.edu --recv-key 562CBC1C
sudo apt-get update
sudo apt-get install cortex
```

Algunos entornos pueden bloquear el acceso al servidor de claves `pgp.mit.edu`. Como resultado, el comando `sudo apt-key adv --keyserver hkp: //pgp.mit.edu --recv-key 562CBC1C` fallará. En ese caso, puede ejecutar el siguiente comando en su lugar:

`curl https://raw.githubusercontent.com/TheHive-Project/Cortex/master/PGP-PUBLIC-KEY | sudo apt-key add -`

A diferencia de Elastic o TheHive, no es necesario crear un servicio para Cortex, este es creado automaticamente. Puede verificar el estado del servicio con el comando:
```bash
sudo systemctl status cortex.service
```

Si el servicio esta inactivo, se debe inicializar:
```bash
sudo systemctl start cortex.service
```

Una vez que se instala el paquete, instale los analyzer como se describe en la siguiente sección.

##### Analyzers and Responders
Los Analyzers y Responders son aplicaciones autónomas administradas y ejecutadas a través de Cortex. Tienen su [repositorio de GitHub](https://github.com/TheHive-Project/Cortex-Analyzers). 

###### Instalación

Actualmente, todos los Analyzers y Responders compatibles con TheHive Project están escritos en Python 2 o 3. No requieren ninguna fase de compilación, pero sus dependencias tienen que ser instaladas. Antes de continuar, deberá instalar las dependencias con:
```bash
sudo apt-get install -y --no-install-recommends python-pip python2.7-dev python3-pip python3-dev ssdeep libfuzzy-dev libfuzzy2 libimage-exiftool-perl libmagic1 build-essential git libssl-dev
```

Puede que necesite instalar Python de` setuptools` y actualizar pip / pip3:
```bash
sudo pip install -U pip setuptools && sudo pip3 install -U pip setuptools
```

Una vez terminado, clone el repositorio de analizadores de Cortex en el directorio que elija (en nuestro caso fue /opt/):
```bash
git clone https://github.com/TheHive-Project/Cortex-Analyzers
```

Cada Analyzer viene con su propio archivo `required.txt` compatible con pip. Puede instalar todos los requisitos con los siguientes comandos:
```bash
for I in $(find Cortex-Analyzers -name 'requirements.txt'); do sudo -H pip2 install -r $I; done && \
for I in $(find Cortex-Analyzers -name 'requirements.txt'); do sudo -H pip3 install -r $I || true; done
```

A continuación, deberá decirle a Cortex dónde encontrar los Analyzers. Los Analyzers pueden estar en diferentes directorios, como se muestra en este ejemplo ficticio del archivo de configuración de Cortex (`/etc/cortex/application.conf`): 
```
analyzer {
  # Directory that holds analyzers
  path = [
    "/path/to/default/analyzers",
    "/path/to/my/own/analyzers"
  ]

  fork-join-executor {
    # Min number of threads available for analyze
    parallelism-min = 2
    # Parallelism (threads) ... ceil(available processors * factor)
    parallelism-factor = 2.0
    # Max number of threads available for analyze
    parallelism-max = 4
  }
}

responder {
  # Directory that holds responders
  path = [
    "/path/to/default/responder",
    "/path/to/my/own/responder"
  ]

  fork-join-executor {
    # Min number of threads available for analyze
    parallelism-min = 2
    # Parallelism (threads) ... ceil(available processors * factor)
    parallelism-factor = 2.0
    # Max number of threads available for analyze
    parallelism-max = 4
  }
}
```

###### Configuración

Todos los Analyzers y Responders deben configurarse utilizando la interfaz de usuario web. En la próxima sección se explicara como crear al menos una organización y luego permita que un usuario con el rol `orgAdmin` configure y habilite los analizadores para esa organización. 

##### Guía de inicio rápido
`SI LA INSTALACION SE REALIZO CON ANSIBLE SALTAR EL PASO 1`

###### Paso 1: Configurar Cortex

El archivo de configuración de fondo de Cortex es `/etc/cortex/application.conf` de forma predeterminada. Para iniciar Cortex y siempre que Elasticsearch se esté ejecutando en la misma máquina, el único parámetro requerido para comenzar a funcionar es la clave del servidor (`play.http.secret.key`). Esta clave se utiliza para autenticar las cookies que
contener datos, y no solo una ID de sesión. Si Cortex se ejecuta en modo de clúster, todos las instancias deben compartir la misma clave.

Para generar una clave, use la siguiente línea de comando:
```bash
(cat << _EOF_
# Secret key
# ~~~~~
# The secret key is used to secure cryptographics functions.
# If you deploy your application to several instances be sure to use the same key!
play.http.secret.key="$(cat /dev/urandom | tr -dc 'a-zA-Z0-9' | fold -w 64 | head -n 1)"
_EOF_

search.host = ['127.0.0.1:9300']
analyzer.urls = ["/opt/Cortex-Analyzers/analyzers"]

) | sudo tee -a /etc/cortex/application.conf
```

Tenga en cuenta que esta clave secreta es obligatoria para iniciar la aplicación Cortex.

###### Paso 2: Actualice la base de datos

Cortex usa ElasticSearch para almacenar la configuración de usuarios, organizaciones y analizadores. La primera vez que se conecta a la interfaz de usuario web (`http://<CORTEX_IP>:9001` por defecto), debe crear la base de datos haciendo clic en el botón` Actualizar base de datos`.

###### Paso 3: Crear el Super Administrador de Cortex

Luego se le invita a crear el primer usuario. Este es un usuario de administración global de Cortex o `superAdmin`. Esta cuenta de usuario podrá crear organizaciones y usuarios de Cortex.

Luego podrá iniciar sesión con esta cuenta de usuario. Notará que se ha creado la organización predeterminada `cortex` y que incluye su cuenta de usuario, un administrador global de Cortex.

###### Paso 4: Crear una organización

La organización `cortex` predeterminada no se puede utilizar para ningún otro propósito que no sea administrar administradores globales (usuarios con el rol` superAdmin`), organizaciones y sus usuarios asociados. No se puede usar para habilitar/deshabilitar o configurar analizadores. Para hacerlo, debe crear su propia organización dentro de Cortex haciendo clic en el botón `Agregar organización`.

###### Paso 5: Crear un administrador de organización

Cree la cuenta de administrador de la organización (usuario con el rol `orgAdmin`).

Luego ingresar en la organizacion (haciendo click sobre el nombre) y crear un usuario. Después de hacerlo, cierre sesión e inicie sesión con esa nueva cuenta de usuario. 

###### Paso 6: Habilitar y configurar Analyzers y Responders

Habilite los Analyzers que necesita, configúrelos utilizando las pestañas **Organization** > **Analyzers Config**. Toda la configuración del analizador se realiza mediante la interfaz de usuario web, incluida la adición de claves API y la configuración de límites de velocidad. (Lo mismo se realiza para los Responders se los habilita en  **Organization** > **Responders Config**)

###### Paso 7: cree una cuenta para la integración de TheHive

Si está utilizando TheHive, cree una nueva cuenta dentro de su organización con el rol `leer, analizar` y genere una clave API que deberá agregar a la configuración de TheHive.

###### Paso 8: configurar archivo aplication.conf para que TheHive se comunique con Cortex 

Una ver creada la cuenta en Cortex se debe generar un API key, esta debe ser copiada en la dentro del directorio `/etc/thehive/application.conf`:

```
## Enable the Cortex module
play.modules.enabled += connectors.cortex.CortexConnector

cortex {
  "CORTEX-SERVER-ID" {
    # URL of the Cortex server
    url = "http://CORTEX_SERVER:CORTEX_PORT"
    # Key of the Cortex user, mandatory for Cortex 2
    key = "API key"
  }
  # HTTP client configuration, more details in section 8
  # ws {
  #   proxy {}
  #   ssl {}
  # }
  # Check job update time interval
  refreshDelay = 1 minute
  # Maximum number of successive errors before give up
  maxRetryOnError = 3
  # Check remote Cortex status time interval
  statusCheckInterval = 1 minute
}
```
