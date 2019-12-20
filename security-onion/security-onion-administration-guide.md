# Guía de Administración de Security Onion

En la siguiente guia se detalla la informacion para gestionar y administrar las herramientas del sistema operativo

[Volver a documento raíz](https://gitlab.unc.edu.ar/csirt/csirt-docs/tree/master#csirt-docs)

## Tabla de Contenidos

* [Cuenta de Administración](#cuenta-de-administración)
* [Actualizacion](#actualización)
* [Salt](#salt)
* [Nodo Forward](#nodo-forward)
  * [Guía de visualización de perdidas](#guía-de-visualización-de-perdidas)

## Cuenta de Administración

Durante la instalación del Servidor Master o del Nodo Forward utilizando Ansible se crea un usuario llamado `admin`. Para administrar estos dispositivos se recomienda utilizar esta cuenta en lugar de utilizar la cuenta root por las siguientes razones:
- En el Servidor Master algunos servicios (docker en particular) no pueden ser reiniciados correctamente estando logeado en la cuenta root.
- En el Nodo Forward si uno esta logeado en la cuenta root las alertas generadas por Suricata son impresas a la terminal.

En caso de necesitar realizar acciones que requieren mayores privilegios siempre se puede utilizar sudo.

## Actualización

Se recomienda utilizar los scripts propios de Security Onion para actualizar el sistema.

[Link a guia de actualizacion](https://securityonion.readthedocs.io/en/latest/upgrade.html)


## Salt

Security Onion ofrece una herramienta para administrar multiples nodos forward con facilidad: Salt.

Salt ofrece un bus de comunicación dinámico para infraestructuras que se puede utilizar para orquestación, ejecución remota, gestión de configuración, etc.
La instalacion debe realizarse tanto en el nodo master como tambien en cada uno de los nodos forward.

Este paquete es totalmente opcional, en el caso de querer utilizarlo debe instalar el paquete correspondiente (ver guía) y luego acceder al script de ansible ubicado en sonion-config-ansible/host_vars/template_forward.yml o sonion-config-ansible/host_vars/template_master.yml y modificar la linea:

```bash
INSTALL_SALT: 'yes'
```

Para mas informacion, puede leer la guía oficial:

[Guía de Salt](https://securityonion.readthedocs.io/en/latest/salt.html)

## Nodo Forward

### Guía de visualización de perdidas

En un nodo forward se puede utilizar un script desarrollado por Security Onion y modificado para solamente visualizar las perdidas en las distintas herramientas de captura y procesamiento. El script original que esta en el nodo forward se llama *sostat* (/usr/sbin/sostat).

[Script de visualización de perdidas](https://gitlab.unc.edu.ar/csirt/sonion-forward-packet-loss)

Ejecutar este script con sudo:
```bash
sudo ./sostat_packet_loss.sh
```

#### Estadísticas de perdidas

##### NIC
Indica si hay perdidas en la interfaz de red física/virtual de la computadora.

##### pf_ring
Indica si el balanceador de carga observa perdidas a cada instancia/proceso de Suricata. La suma de las perdidas es igual a los valores de la siguiente sección.

##### IDS Engine (suricata) packet drops
Indica si hay perdida de paquetes en Suricata. Si la el porcentaje de paquetes perdidos es muy alto puede considerar:
- Aumentar la cantidad de procesos y fijarlos (ver afinidad de CPU) de Suricata en los parámetros de instalación con ansible.
- Aumentar la cantidad de RAM del nodo.

##### Bro
Indica si hay perdida de paquetes en Bro. Si la el porcentaje de paquetes perdidos es muy alto puede considerar:
- Aumentar la cantidad de procesos y fijarlos (ver afinidad de CPU) de Bro en los parámetros de instalación con ansible.
- Aumentar la cantidad de RAM del nodo.

##### Netsniff-NG
Indica si netsniff-ng pierde paquetes al capturar trafico en una interfaz. Si  informa una pérdida de paquetes, puede considerar una o más de las siguientes opciones en */etc/nsm/HOSTNAME-INTERFACE/sensor.conf* o en los parámetros de instalación con ansible:
- aumentar PCAP_RING_SIZE.
- agregar a PCAP_OPTIONS la opción --mmap para habilitar IO mapeado en memoria.
- agregar a PCAP_OPTIONS la opción -b0 fara fijar el proceso en el núcleo de procesador 0.

Tenga en cuenta que todas las opciones harán que netsniff-ng consuma más RAM.