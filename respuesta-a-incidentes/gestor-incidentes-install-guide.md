# Guía de Instalación de gestor de incidentes

descripcion

## Tabla de Contenidos
  * [Opciones de Instalación](#opciones-de-instalación)
    * [Ansible](#ansible)
    * [Manual](#manual)
## Opciones de Instalación

### Ansible

Queres usar ansible? Link a repo, seguir instrucciones

### Manual
# TheHive & Cortex Instalación

En este documento se indica como instalar TheHive & Cortex. Ver instrucciones originales [aquí](https://github.com/TheHive-Project/TheHiveDocs/blob/master/installation/install-guide.md).

## Requisitos

Se requiere tener instalado un sistema operativo de tipo Debian.

## Elasticsearch

Instale el paquete Elasticsearch proporcionado por Elastic

```bash
# Instalación de clave PGP
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-key D88E42B4
# Instalación de clave PGP alternativa
# wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
# Configuración de repositorio Debian
echo "deb https://artifacts.elastic.co/packages/5.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-5.x.list
# Instalar suporte para apt con https
sudo apt install apt-transport-https
# Instalación de Elasticsearch
sudo apt update && sudo apt install elasticsearch
```

El paquete Debian no inicia el servicio de forma predeterminada, para evitar que la instancia se una accidentalmente a un clúster, sin estar configurado adecuadamente.

### Configuración

Edita `/etc/elasticsearch/elasticsearch.yml` y agrega las siguientes lineas:

```
network.host: 127.0.0.1
script.inline: true
cluster.name: hive
thread_pool.index.queue_size: 100000
thread_pool.search.queue_size: 100000
thread_pool.bulk.queue_size: 100000
```

###### Inicia el Servicio

Ahora que Elasticsearch está configurado, inícielo como un servicio y verifique si se está ejecutando:
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

## TheHive

Para instalar usando los paquetes Debian, se usan los siguientes comandos:
```bash
echo 'deb https://dl.bintray.com/thehive-project/debian-stable any main' | sudo tee -a /etc/apt/sources.list.d/thehive-project.list
sudo apt-key adv --keyserver hkp://pgp.mit.edu --recv-key 562CBC1C
sudo apt-get update
sudo apt-get install thehive
```

Si el acceso al servidor `pgp.mit.edu` esta bloqueado el comando `sudo apt-key adv --keyserver hkp://pgp.mit.edu --recv-key 562CBC1C` fallara. En ese caso, en su lugar ejecutar el siguiente comando:

`curl https://raw.githubusercontent.com/TheHive-Project/TheHive/master/PGP-PUBLIC-KEY | sudo apt-key add -`

#### Inicia el Servicio

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

Luego para iniciar la aplicación como un servicio, use los siguientes comandos:
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

Tenga en cuenta que el servicio puede tardar un tiempo en iniciarse. Una vez que se inicia, puede iniciar su navegador y conectarse a `http://YOUR_SERVER_ADDRESS:9000/`.

Tenga en cuenta que el servicio puede tardar un tiempo en iniciarse.

La primera vez que se conecte deberá crear el esquema de la base de datos. Haga clic en "Migrate database" para crear el esquema de base de datos.

Una vez hecho esto, debería ser redirigido a la página para crear la cuenta del administrador.

Una vez creado, debe ser redirigido a la página de inicio de sesión.

**Advertencia**: en esta etapa, si se perdió la creación de la cuenta de administrador, no podrá hacerlo a menos que elimine el índice de TheHive de Elasticsearch. En el caso de que haya cometido un error, primero averigüe cuál es el índice actual de TheHive ejecutando el siguiente comando en un host donde se encuentra el Elasticsearch DB utilizado por TheHive:
```bash
$ curl http://127.0.0.1:9200/_cat/indices?v
```

Los índices que usa TheHive siempre comienzan con `the_hive_` seguido de un número. Supongamos que la salida del comando es:
```bash
health status index       uuid                   pri rep docs.count docs.deleted store.size pri.store.size
yellow open   cortex_1    PC_pLFGBS5G2TNQYr4ajgw   5   1        609            6      2.1mb          2.1mb
yellow open   the_hive_13 ft7GGTfhTr-4lSzZw5r1DQ   5   1     180131            3     51.3mb         51.3mb
```

El índice utilizado por TheHive es `the_hive_13`. Para eliminarlo, ejecute el siguiente comando:
```bash
$ curl -X DELETE http://127.0.0.1:9200/the_hive_13
```

Luego, vuelva a cargar la página o reinicie TheHive.

## Cortex
Para instalar el paquete Cortex Debian, use los siguientes comandos:
```bash
echo 'deb https://dl.bintray.com/thehive-project/debian-stable any main' | sudo tee -a /etc/apt/sources.list.d/thehive-project.list
sudo apt-key adv --keyserver hkp: //pgp.mit.edu --recv-key 562CBC1C
sudo apt-get update
sudo apt-get install cortex
```

Algunos entornos pueden bloquear el acceso al servidor de claves `pgp.mit.edu`. Como resultado, el comando `sudo apt-key adv --keyserver hkp: //pgp.mit.edu --recv-key 562CBC1C` fallará. En ese caso, puede ejecutar el siguiente comando en su lugar:

`curl https://raw.githubusercontent.com/TheHive-Project/Cortex/master/PGP-PUBLIC-KEY | sudo apt-key add -`

Una vez que se instala el paquete, instale los analyzer como se describe en la siguiente sección.

### Analyzers and Responders
Los Analyzers y Responders son aplicaciones autónomas administradas y ejecutadas a través de Cortex. Tienen su [repositorio de GitHub](https://github.com/TheHive-Project/Cortex-Analyzers). 

#### Instalación

Actualmente, todos los Analyzers y Responders compatibles con TheHive Project están escritos en Python 2 o 3. No requieren ninguna fase de compilación, pero sus dependencias tienen
que ser instaladas. Antes de continuar, deberá instalar las dependencias con:
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

#### Configuración

Todos los Analyzers y Responders deben configurarse utilizando la interfaz de usuario web. En la próxima sección se explicara como crear al menos una organización y luego permita que un usuario con el rol `orgAdmin` configure y habilite los analizadores para esa organización. 

### Guía de inicio rápido

#### Paso 1: Configurar Cortex

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

#### Paso 2: Actualice la base de datos

Cortex usa ElasticSearch para almacenar la configuración de usuarios, organizaciones y analizadores. La primera vez que se conecta a la interfaz de usuario web (`http://<CORTEX_IP>:9001` por defecto), debe crear la base de datos haciendo clic en el botón` Actualizar base de datos`.

#### Paso 3: Crear el Super Administrador de Cortex

Luego se le invita a crear el primer usuario. Este es un usuario de administración global de Cortex o `superAdmin`. Esta cuenta de usuario podrá crear organizaciones y usuarios de Cortex.

Luego podrá iniciar sesión con esta cuenta de usuario. Notará que se ha creado la organización predeterminada `cortex` y que incluye su cuenta de usuario, un administrador global de Cortex.

#### Paso 4: Crear una organización

La organización `cortex` predeterminada no se puede utilizar para ningún otro propósito que no sea administrar administradores globales (usuarios con el rol` superAdmin`), organizaciones y sus usuarios asociados. No se puede usar para habilitar/deshabilitar o configurar analizadores. Para hacerlo, debe crear su propia organización dentro de Cortex haciendo clic en el botón `Agregar organización`.

#### Paso 5: Crear un administrador de organización

Cree la cuenta de administrador de la organización (usuario con el rol `orgAdmin`).

Luego, especifique una contraseña para este usuario. Después de hacerlo, cierre sesión e inicie sesión con esa nueva cuenta de usuario. 

#### Paso 6: Habilitar y configurar Analyzers

Habilite los Analyzers que necesita, configúrelos utilizando las pestañas ** Organización **> ** Configuración ** y ** Organización **> ** Analizadores **. Toda la configuración del analizador se realiza mediante la interfaz de usuario web, incluida la adición de claves API y la configuración de límites de velocidad.

#### Paso 7 (Opcional): cree una cuenta para la integración de TheHive

Si está utilizando TheHive, cree una nueva cuenta dentro de su organización con el rol `leer, analizar` y genere una clave API que deberá agregar a la configuración de TheHive.