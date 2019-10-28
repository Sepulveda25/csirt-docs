# Guía de Instalación de Nodo Forward

En la siguiente guía se especifican los pasos a seguir junto con los valores sugeridos para realizar la instalación de Security Onion y prepararlo para ser usado como un nodo forward.

[Volver a documento raiz](../../README.md)

## Tabla de Contenidos
  * [Virtualización para un Nodo Forward](#virtualización-para-un-nodo-forward)
  * [Instalacion de Security Onion](#instalación-de-security-onion)
    - [Instalación del sistema operativo](#instalación-del-sistema-operativo)
    - [Configuración de interfaz de red de administración](#configuración-de-interfaz-de-red-de-administración)
    - [Montado de segundo disco duro al directorio /nsm](#montado-de-segundo-disco-duro-al-directorio-nsm)
  * [Nodo Forward - Opciones de Instalación](#nodo-forward-opciones-de-instalación)
    - [Ansible](#ansible)
    - [Scripts](#scripts)

## Virtualización para un Nodo Forward

Instrucciones de creación de una maquina virtual por si se desea instalar un nodo forward en un entorno virtualizado.

Esta guía se basa en el uso del entorno VMWare ESXI

#### Prerequisitos

* Tener un ISO de Security Onion subido a algún datastore del servidor

### Creación de Maquina Virtual

1. En la sección **Virtual Machines** seleccionar **Create / Register VM** y en la ventana que aparece, *Next*.
2. Definir un **nombre** para la maquina virtual (e.g. sonion-forward-fcefyn) y selecciona la familia de sistema operativo **Linux**; version **Ubuntu Linux (64-bit)**
3. Seleccionar el **disco físico** en donde se creara la maquina virtual con suficiente espacio para crear los discos duros virtuales.
4. Configuración de componentes (valores a modo de ejemplo para una red con 250Mbps promedio):
    * CPU: 8
    * Memory: 16 GB
    * Hard Disk 1: 50 GB - Thin Provisioned
    * Hard Disk 2: *Tamaño suficiente para cumplir con la política de retención* - Thick Provisioned (este disco es el que almacenará la captura de paquetes, se llenará rápidamente)
    * Network Adapter 1: *Vlan de manejo de CSIRT*
    * Network Adapter 2: *Mirror port del trafico a monitorear*\*
    * CD/DVD Drive 1: *Datastore ISO file* (seleccionar el iso de Security Onion)
5. Finaliza la creación de la maquina virtual.

\* La interfaz virtual que recibe el trafico mirroreado debe tener configurado en su switch virtual que acepte trafico en modo promiscuo (ver opciones de seguridad).

## Instalación de Security Onion


### Instalación del sistema operativo

1. Encender la maquina y bootear a un medio booteable de Security Onion
    * En el caso de estar en un entorno virtualizado el archivo .iso debe estar cargado al servidor. En el caso de tener una maquina dedicada el .iso debe ser grabado a un pen drive por ejemplo.
2. En el escritorio hacer doble click en el icono con el nombre **Instalacion de Security Onion**
3. Seleccionar **English**.
4. Dejar todo sin seleccionar y continuar.
5. Seleccionar la opción **Erase disk and install SecurityOnion**.
6. Seleccionar el primer disco duro (**/dev/sda**, el mas pequeño).
7. Confirmar los cambios seleccionados y continuar.
8. Seleccionar la zona horaria apropiada (**Córdoba**).
9. Seleccionar la distribución del teclado apropiada (por lo general **Spanish (Latin American)**).
10. Ingresar los nombres y seleccionar una contraseña, valores/formato sugerido:
    * Your name: "**sonion<codigo_dependencia>**" (e.g. *sonionfcefyn*)
    * Your computer's name: "**sonion-forward-<codigo_dependencia>**" (e.g. *sonion-forward-fcefyn*)
    * Pick a username: "**sonion<codigo_dependencia>**" (e.g. *sonionfcefyn*)
11. Esperar que termine la instalación y luego reiniciar la maquina.

### Configuración de interfaz de red de administración

1. Ingresar a la sesión y en el escritorio hacer doble click en el icono con el nombre **Setup**
2. Seleccionar **Yes, Continue!** y luego **Yes, configure /etc/network/interfaces!**.
3. Seleccionar la **interfaz de administración** (e.g. *ens160*).
4. Seleccionar **static**.
5. Ingresar la **dirección IP** que debe tener el servidor, la **mascara de red** y la **dirección IP gateway**.
6. Ingresar las direcciones IP de los **servidores DNS**.
7. Ingresar un **local domain name**.
8. Seleccionar **No, only configure a managenment interface**, luego **Yes, make changes!** y seleccionar **Yes, reboot!** para finaliza la configuración.

Con esto ya se puede acceder al Security Onion por ssh y realizar el resto de la instalación.

### Montado de segundo disco duro al directorio /nsm

Instrucciones adaptadas desde [aquí](https://securityonion.readthedocs.io/en/latest/new-disk.html)

1. Determinar la path del disco (por defecto suele ser **/dev/sdb**):
```bash
sudo fdisk -l
```
2. Particionar la nueva unidad usando fdisk o parted
```bash
sudo fdisk /dev/sdb
```
  * Ingresar la opcion `n` para crea una nueva partición y dejar todas las opciones solicitadas en sus valores por defecto. Luego ingresar `w` para escribir los cambios al disco.
3. Formatear la nueva partición usando mkfs
```bash
sudo mkfs.ext4 /dev/sdb1
```
4. Montar la nueva unidad en una ubicación temporal en el sistema de archivos:
```bash
sudo mount /dev/sdb1 /mnt
```
5. Copiar los datos existentes de /nsm a la ubicación temporal:
```bash
sudo cp -av /nsm/* /mnt/
```
6. Desmontar la nueva unidad desde la ubicación temporal:
```bash
sudo umount /mnt
```
7. Cambiar el nombre del /nsm existente:
```bash
sudo mv /nsm /nsm-backup
```
8. Actualicar /etc/fstab para montar la nueva unidad en /nsm:
```bash
sudo nano /etc/fstab
```
  * (Se puede usar blkid para encontrar el UUID de la unidad para escribir en /etc/fstab)
```bash
sudo blkid /dev/sdb1
```
  * Agregar a /etc/fstab, reeplazando `<buscarUUID>`:
```
# mount /dev/sdb1 to /nsm
UUID=<buscarUUID> /nsm ext4 defaults 0 2
```
9. Volver a crear el directorio /nsm después de cambiarle el nombre:
```bash
sudo mkdir /nsm
```
10. Montar el nuevo /nsm:
```bash
sudo mount /dev/sdb1 /nsm
```

## Nodo Forward - Opciones de Instalación

Hay dos formas de preparar Security Onion para ser utilizado como un nodo forward: Utilizando playbooks de ansible o scripts programados en bash.

### Ansible

[Repositorio con instrucciones para usar ansible](https://gitlab.unc.edu.ar/csirt/sonion-config-ansible)

### Scripts

[Repositorio con instrucciones para usar scripts bash](https://gitlab.unc.edu.ar/csirt/sonion-config-scripts/tree/master/forward_config_script)