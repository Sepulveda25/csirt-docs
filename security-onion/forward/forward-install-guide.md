# Guía de Instalación de Nodo Forward

descripcion

## Tabla de Contenidos
  * [Instalacion de Security Onion](#instalación-de-security-onion)
  * [Opciones de Instalación](#opciones-de-instalación)
    * [Ansible](#ansible)
    * [Scripts](#scripts)

## Instalación de Security Onion

1. Encender la maquina y bootear a un medio booteable de Security Onion
    * En el caso de estar en un entorno virtualizado el archivo .iso debe estar cargado al servidor. En el caso de tener una maquina dedicada el .iso debe ser grabado a un pen drive por ejemplo.
2. En el escritorio hacer doble click en el icono con el nombre *Instalacion de Security Onion*
3. Seleccionar *English*.
4. Dejar todo sin seleccionar y continuar.
5. Seleccionar la opción *Erase disk and install SecurityOnion*.
6. Seleccionar el primer disco duro (*/dev/sda*, el mas pequeño).
7. Confirmar los cambios seleccionados y continuar.
8. Seleccionar la zona horaria apropiada (*Córdoba*).
9. Seleccionar la distribución del teclado apropiada (por lo general *Spanish (Latin American)*).
10. Ingresar los nombres y seleccionar una contraseña, valores/formato sugerido:
    * Your name: "*sonion<codigo_dependencia>*" (e.g. *sonionfcefyn*)
    * Your computer's name: "*sonion-forward-<codigo_dependencia>*" (e.g. *sonion-forward-fcefyn*)
    * Pick a username: "*sonion<codigo_dependencia>*" (e.g. *sonionfcefyn*)
11. Esperar que termine la instalación y luego reiniciar la maquina.
12. Ingresar a la sesión y en el escritorio hacer doble click en el icono con el nombre *Setup*
13. Configurar la interfaz de red para la administracion:
    * Seleccionar *Yes, Continue!* y luego *Yes, configure /etc/network/interfaces!*.
    * Seleccionar la interfaz de administración (e.g. *ens160*).
    * Seleccionar *static*.
    * Ingresar la dirección IP que debe tener el servidor, la mascara de red y la dirección IP gateway.
    * Ingresar las direcciones IP de los servidores DNS.
    * Ingresar un *local domain name*.
    * Seleccionar *No, only configure a managenment interface*, luego *Yes, make changes!* y seleccionar *Yes, reboot!* para finaliza la configuración.

Con esto ya se puede acceder al Security Onion por ssh y realizar el resto de la instalación.

## Opciones de Instalación

### Ansible

Queres usar ansible? Link a repo, seguir instrucciones

### Scripts

Queres usar los scripts? Link a repo, seguir instrucciones