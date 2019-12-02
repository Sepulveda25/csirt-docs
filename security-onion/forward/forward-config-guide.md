# Guía de Configuración de un Nodo Forward

En la siguiente guía se especifican las formas en la que se puede configurar una instalación de Security Onion para que actúe como un nodo forward, especificando los distintos parámetros y los valores que pueden tomar.

[Volver a documento raíz](https://gitlab.unc.edu.ar/csirt/csirt-docs/tree/master#csirt-docs)

## Tabla de Contenidos
  * [Opciones de instalación](#opciones-de-instalación)
    - [Ansible](#ansible)
    - [Scripts](#scripts)
  * [Virtualización para un Nodo Forward](#virtualización-para-un-nodo-forward)
  * [Instalacion de Security Onion](#instalación-de-security-onion)
    - [Instalación del sistema operativo](#instalación-del-sistema-operativo)
    - [Configuración de interfaz de red de administración](#configuración-de-interfaz-de-red-de-administración)
    - [Montado de segundo disco duro al directorio /nsm](#montado-de-segundo-disco-duro-al-directorio-nsm)

## Opciones de instalación

Hay dos formas de preparar Security Onion para ser utilizado como un nodo forward: Utilizando playbooks de ansible o scripts programados en bash.

### Ansible

[Repositorio con instrucciones para usar ansible](https://gitlab.unc.edu.ar/csirt/sonion-config-ansible)

### Scripts

[Repositorio con instrucciones para usar scripts bash](https://gitlab.unc.edu.ar/csirt/sonion-config-scripts/tree/master/forward_config_script)

## Parámetros de configuración

archivos: 
+ sosetup.conf
+ 

### sosetup.conf


### Afinidad de CPU

##### Cambios

**CAMBIAR - HACER GENERICO LA CANTIDAD DE NUCELOS**

Netsniff-ng es asignado al CPU0.  
Por defecto suricata tiene un management thread en CPU1 y tres worker threads en CPU[2-4].  
Por defecto bro tiene tres threads en CPU[5-7].

#### Netsniff-ng

Ver sección sosetup.conf **LINK TO SECTION**

#### Suricata

[Documentación oficial](https://suricata.readthedocs.io/en/suricata-5.0.0/configuration/suricata-yaml.html#threading)

En el archivo **/etc/nsm/[hostname]-[interfaz]/suricata.yaml** se configura la afinidad de CPU de Suricata.

Suricata es multihilo. Utiliza múltiples CPU o núcleos de CPU para que pueda procesar muchos paquetes de red simultáneamente. (En un motor de un solo núcleo, los paquetes se procesarán uno a la vez).

Hay cuatro módulos de subprocesos: adquisición de paquetes, decodificación y transmisión de la capa de aplicación, detección y salidas.

En el archivo de configuración, bajo la sección de `threading:`, existe la opción `set-cpu-affinity: yes`. Con esta opción, se puede hacer que Suricata establezca núcleos fijos para cada hilo.

En la opción `cpu-affinity:` puede establecer qué CPU / núcleos trabajan con qué hilo. En esta opción hay varios conjuntos de hilos. El conjunto de gestión (`management-cpu-set`), recepción (`receive-cpu-set`), trabajador (`worker-cpu-set`) y veredicto (`verdict-cpu-set`). En este caso el ultimo conjunto no se utiliza.

Para cada conjunto hay varias opciones: cpu, mode y prio. En la opción '`cpu:`', se puede establecer los números de CPU / núcleos que ejecutarán los hilos de ese conjunto. Puede establecer esta opción en "all", usar un rango (0-3) o una lista separada por comas (0,1). La opción `mode:` se puede configurar como "balanced" o "exclusive". Cuando se establece en "balanced", los hilos individuales pueden ser procesados ​​por todos los núcleos configurados en la opción "cpu". Si la opción "mode" está configurada como "exclusive", habrá núcleos fijos para cada hilo. En la opción `prio:` se puede establecer una prioridad para cada hilo. Esta prioridad puede ser low, medium, high o puede establecer la prioridad en "default" (predeterminada). Si no establece una prioridad para una CPU, la configuración en "default" contará.

Ejemplo de configuración:
```yaml
threading:
  set-cpu-affinity: yes
  cpu-affinity:
    - management-cpu-set:
        cpu: [ 1 ]
    - receive-cpu-set:
        cpu: [ 1 ]
    - worker-cpu-set:
        cpu: [ 2-4 ]
        mode: "exclusive"
        prio:
          #low: [ 0 ]
          #medium: [ "1-2" ]
          #high: [ 3 ]
          #default: "high"
  detect-thread-ratio: 1.0
```
En un Security Onion con 8 núcleos de CPU disponibles se configura tal que Suricata utiliza 4 de estos núcleos. Uno para los hilos de gestión (management-cpu-set) y recepción (receive-cpu-set) y tres para los tres hilos trabajadores (worker-cpu-set), cada uno fijado en un núcleo. La cantidad de hilos trabajadores es configurado en Ver sección sosetup.conf **LINK TO SECTION**

**HABLAR SOBRE CAMBIOS DE RENDIMIENTO**

#### Bro

[Documentación oficial](https://docs.zeek.org/en/stable/configuration/#using-pf-ring)

En el archivo **/opt/bro/etc/node.cfg** se configura la afinidad de CPU de Bro.

Security Onion viene con Bro compilado para de tal manera que sea compatible con PF-RING y AF-PACKET para que se pueda activar a varios trabajadores de Bro para manejar más tráfico. Si está monitoreando altos niveles de tráfico, es posible que deba usar la configuración `pin_cpus`.

La cantidad de procesos (`lb_procs=`) es configurado en Ver sección sosetup.conf **LINK TO SECTION**

Para fijar los procesos a núcleos de CPU, debajo de la sección [[hostname]-[interfaz]] se debe insertar `pin_cpus=[núcleos]`, donde [núcleos] son los núcleos en donde se fijaran los procesos.

Ejemplo:
```bash
[sonionforward1-ens192]
type=worker
host=localhost
interface=ens192
lb_method=custom
lb_procs=3
pin_cpus=5,6,7
af_packet_fanout_id=31
af_packet_fanout_mode=AF_Packet::FANOUT_HASH
af_packet_buffer_size=128*1024*1024
```
En un Security Onion con 8 núcleos de CPU disponibles se configura tal que Bro utiliza de manera fija 3 de estos núcleos.

**HABLAR SOBRE CAMBIOS DE RENDIMIENTO**

### Balanceo de carga

El balanceador de carga es seteado a pfring (siendo af-packet la alternativa).

```
# Set pfring as the load balancer (af-packet is the default)
printf "Configurando pfring en /etc/nsm/$(cat /proc/sys/kernel/hostname)-$1/sensor.conf ..\n"
sed -i 's/SURICATA_CAPTURE="af-packet"/SURICATA_CAPTURE="pfring"/' /etc/nsm/$(cat /proc/sys/kernel/hostname)-$1/sensor.conf
```

### Cache de linux

Se configuran estas variables en */etc/sysctl.conf* :
* vm.dirty_background_ratio = 50
* vm.dirty_ratio = 80
* vm.swappiness = 10

```
# Configure Linux Disk Caching
printf "Copiando configuracion de cache de linux a /etc/sysctl.conf y aplicando los nuevos valores ..\n"
cp $2 /etc/sysctl.conf
sysctl -p
```

### Reglas de clasificacion de alertas IDS

Se copian las reglas de clasificacion de alertas IDS a */etc/nsm/rules/classification.config*

```
# Set new classification rules for incoming IDS alerts
printf "Copiando reglas de clasificacion de alertas IDS a /etc/nsm/rules/classification.config ..\n"
cp $3 /etc/nsm/rules/classification.config
```

### Telegraf

Se instala y se configura Telegraf para recopilar e informar métricas a un servidor de Grafana


