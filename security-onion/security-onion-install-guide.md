# Guía de Instalación de Security Onion

En la siguiente guía se especifican los pasos a seguir junto con los valores sugeridos para realizar la instalación de Security Onion y prepararlo para ser usado como un nodo forward o como un servidor master.

[Volver a documento raíz](https://gitlab.unc.edu.ar/csirt/csirt-docs/tree/master#csirt-docs)

## Tabla de Contenidos
  * [Virtualización para un Security Onion](#virtualización-para-un-security-onion)
  * [Instalación de Ubuntu Server](#instalación-de-ubuntu-server)
    - [Instalación del sistema operativo](#instalación-del-sistema-operativo)
    - [Montado de segundo disco duro al directorio /nsm](#montado-de-segundo-disco-duro-al-directorio-nsm)
  * [Instalación y configuración de Security Onion](#instalación-y-configuración-de-security-onion)
    - [Ansible](#ansible)

## Virtualización para un Security Onion

Instrucciones de creación de una maquina virtual por si se desea instalar un Security Onion en un entorno virtualizado.

Esta guía se basa en el uso del entorno VMWare ESXI

#### Prerequisitos

* Tener un ISO de Ubuntu Server 16.04 (ubuntu-16.04.6-server-amd64.iso) subido en algún datastore del servidor.

### Creación de Maquina Virtual

1. En la sección **Virtual Machines** seleccionar **Create / Register VM** y en la ventana que aparece, *Next*.
2. Definir un **nombre** para la maquina virtual (e.g. sonion-forward-fcefyn) y selecciona la familia de sistema operativo **Linux**; version **Ubuntu Linux (64-bit)**.
3. Seleccionar el **disco físico** en donde se creara la maquina virtual con suficiente espacio para crear los discos duros virtuales.
4. Configuración de componentes (valores a modo de ejemplo para un nodo forward monitoreando una red con 250Mbps promedio):
    * CPU: 8
    * Memory: 16 GB
    * Hard Disk 1: 50 GB - Thin Provisioned
    * Hard Disk 2 (Agregar nuevo disco duro): *Tamaño suficiente para cumplir con la política de retención* - Thin Provisioned (este disco es el que almacenará la captura de paquetes en el nodo forward o los logs recolectados en el servidor master)
    * Network Adapter 1: *Vlan de administración de CSIRT*
    * Solo Nodo Forward: Network Adapter 2 (Agregar nuevo adaptador de red): *Mirror port del trafico a monitorear*\*
    * CD/DVD Drive 1: *Datastore ISO file* (seleccionar el iso de Ubuntu Server)
5. Finaliza la creación de la maquina virtual.

\* La interfaz virtual que recibe el trafico mirroreado debe tener configurado en su switch virtual que acepte trafico en modo promiscuo (ver opciones de seguridad).

## Instalación de Ubuntu Server

### Instalación del sistema operativo

1. Encender la maquina y bootear a un medio booteable de Ubuntu Server 16.04.
    * En el caso de estar en un entorno virtualizado el archivo .iso debe estar cargado al servidor. En el caso de tener una maquina dedicada el .iso debe ser grabado a un pen drive por ejemplo.
2. Seleccionar **English**.
3. Seleccionar **Install Ubuntu Server with the HWE kernel**.
4. Seleccionar **English**.
5. Navegar y seleccionar **Argentina** (other -> South America -> Argentina)
6. Seleccionar **United States - en_US.UTF-8**.
7. Al preguntar "Detect keyboard layout" seleccionar **No** y en las siguientes dos pantallas seleccionar **Spanish (Latin America)**.
8. En la sección de configuración de hardware de red (en el nodo forward, como hay dos interfaces de red seleccionar la primera como primaria). La instalación intentara auto-configurarse, cuando falla la configuración de la red con DHCP continuar y al solicitar "Network configuration method" seleccionar **Configure network manually**
    - Ingresar la **dirección IP** que debe tener el servidor, la **mascara de red** y la **dirección IP gateway**.
    - Ingresar las direcciones IP de hasta 3 **servidores DNS**, separados por espacios.
9. Ingresar los nombres solicitados y seleccionar una contraseña, valores/formato sugerido:
    - Hostname: "**[dependencia]-[servicio]-[versión]**" (e.g. *fcefyn-csirt-sonion-forward-v1* o *sonion-master*)
    - Domain name
    - Full name: "**sonion[codigo_dependencia]**" (e.g. *sonionfcefyn* o *sonionmaster*)
    - Username: "**sonion[codigo_dependencia]**" (e.g. *sonionfcefyn* o *sonionmaster*)
10. Al preguntar "Encrypt your home directory?" seleccionar **No**.
11. Confirmar la zona horaria detectada (o configurar un servidor NTP)
12. Al solicitar "Partitioning method" seleccionar **Guided - use entire disk**.
13. Seleccionar el primer disco duro (**sda** o **/dev/sda**, el mas pequeño).
14. Confirmar los cambios seleccionados (escribiendo los cambios al disco) y continuar.
15. Al solicitar información sobre un HTTP proxy simplemente continuar.
16. Al preguntar "How do you want to manage upgrades on this system?" seleccionar **No automatic updates**.
17. Al solicitar el software a instalar solo dejar seleccionado (con la barra espaciadora) **standard system utilities** y **OpenSSH Server** y continuar.
18. Al preguntar "Install the GRUB boot loader to the master boot record" seleccionar **Yes** (si pregunta en que disco, seleccionar /dev/sda).
19. Seleccionar **Continue** para finalizar la instalación, la maquina sera reiniciada.

Con esto ya se puede acceder al servidor por ssh y realizar el resto de la instalación.

### Montado de segundo disco duro al directorio /nsm

En la carpeta */nsm* se almacenaran las capturas de paquetes del nodo forward o los logs recolectados por el servidor master.

Instrucciones adaptadas desde [aquí](https://securityonion.readthedocs.io/en/latest/new-disk.html)

1. Determinar la dirección del segundo disco a montar (por defecto suele ser **/dev/sdb**):
```bash
sudo fdisk -l
```
2. Particionar la nueva unidad usando fdisk o parted
```bash
sudo fdisk /dev/sdb
```
  * Ingresar la opción `n` para crear una nueva partición y dejar todas las siguientes opciones solicitadas en sus valores por defecto. Luego ingresar la opción `w` para escribir los cambios al disco.
3. Formatear la nueva partición usando mkfs
```bash
sudo mkfs.ext4 /dev/sdb1
```
4. Actualicar /etc/fstab para montar la nueva unidad en /nsm:
```bash
sudo nano /etc/fstab
```
  * (Se puede usar blkid para encontrar el UUID de la unidad para escribir en /etc/fstab)
```bash
sudo blkid /dev/sdb1
```
  * Agregar a /etc/fstab, reeplazando `<buscarUUID>` con UUID resultado del comando anterior:
```
# mount /dev/sdb1 to /nsm
UUID=<buscarUUID> /nsm ext4 defaults 0 2
```
5. Crear el directorio /nsm:
```bash
sudo mkdir /nsm
```
6. Montar el nuevo /nsm:
```bash
sudo mount /dev/sdb1 /nsm
```

## Instalación y configuración de Security Onion

El Ubuntu Server se puede preparar para ser utilizado como un nodo forward o un servidor master utilizando playbooks de ansible.

### Ansible

[Repositorio con instrucciones para usar ansible](https://gitlab.unc.edu.ar/csirt/sonion-config-ansible)
