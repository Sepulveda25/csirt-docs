# Guía de Virtualización para un Nodo Forward

Instrucciones de creación de una maquina virtual por si se desea instalar un nodo forward.

Esta guía se basa en el uso del entorno VMWare ESXI

### Prerequisitos

* Tener un ISO de Security Onion subido a algún datastore del servidor

## Creación de Maquina Virtual

1. En la sección *Virtual Machines* seleccionar *Create / Register VM* y en la ventana que aparece, *Next*.
2. Definí un nombre para la maquina virtual (e.g. sonion-forward-fcefyn) y selecciona la familia de sistema operativo *Linux*; version *Ubuntu Linux (64-bit)*
3. Seleccina el disco físico en donde se creara la maquina virtual con suficiente espacio para crear los discos duros virtuales.
4. Configuracion de componentes:
    * CPU: 8
    * Memory: 16 GB
    * Hard Disk 1: 50 GB - Thin Provisioned
    * Hard Disk 2: *Tamaño suficiente para cumplir con la política de retención* - Thick Provisioned (este disco es el que gradara la captura de paquetes y se llenara rápidamente)
    * Network Adapter 1: *Vlan de manejo de CSIRT*
    * Network Adapter 2: *Mirror port del trafico a monitorear*
    * CD/DVD Drive 1: *Datastore ISO file* (seleccionar el iso de Security Onion)
5. Finaliza la creación de la maquina virtual.
