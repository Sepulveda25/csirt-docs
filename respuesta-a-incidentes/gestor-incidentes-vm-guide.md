# Guía de Virtualización para gestor de incidentes

Instrucciones de creación de una maquina virtual por si se desea instalar el gestor de incidentes. 
Para la instalacion del gestor de incidentes, se utilizo debian 10.  

Esta guía se basa en el uso del entorno VMWare ESXI

### Prerequisitos

* Tener un ISO de Debian 10 subido a algún datastore del servidor

## Creación de Maquina Virtual

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