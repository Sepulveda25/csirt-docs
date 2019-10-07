# csirt-docs

Documentación general del CSIRT

+ Que hace el csirt?
+ Por que partes esta compuesto (IDS, SIEM, ML)

## Tabla de Contenidos
  * [Security Onion](#security-onion)
    * [Nodo Master](#nodo-master)
    * [Nodo Forward](#nodo-forward)
  * [Respuesta a Incidentes](#respuesta-a-incidentes)
    * [TheHive](#thehive)
    * [Cortex](#cortex)
  * [ML](#ml)

## Security Onion

### Nodo Master

descripcion

#### Requisitos de Hardware

Hardware necesario dependiendo de la cantidad de trafico

Cuanto almacenamiento es necesario para cuanto tiempo de retención.

+ Núcleos CPU:
+ RAM:

#### Guías

- [Guía de Virtualización](security-onion/master/vm-guide.md)
- [Guía de Instalación](security-onion/master/install-guide.md)
- [Guía de Administración](admin/admin-guide.md)
- [Guía de Configuración](admin/configuration.md)
- [Webhooks](admin/webhooks.md)
- [Cluster Configuration](admin/cluster.md)
- [Updating](admin/updating.md)
- [Backup & Restore](admin/backup-restore.md)
- [Migration Guide](migration-guide.md)
- [API Documentation](api/README.md) (incomplete)

### Nodo Forward

descripcion

#### Requisitos de Hardware

Hardware necesario dependiendo de la cantidad de trafico

Cuanto almacenamiento es necesario para cuanto tiempo de retención.

##### Trafico sostenido promedio <200Mbps
+ Núcleos CPU:
+ RAM:

##### Trafico sostenido promedio >200Mbps & <500(?)Mbps
+ Núcleos CPU:
+ RAM:

Link a documento especificando las pruebas realizadas (?)

#### Guías

- [Guía de Virtualización](security-onion/forward/vm-guide.md)
- [Guía de Instalación](security-onion/forward/install-guide.md)
- [Guía de Administración](admin/admin-guide.md)
- [Guía de Configuración](admin/configuration.md)
- [Webhooks](admin/webhooks.md)
- [Cluster Configuration](admin/cluster.md)
- [Updating](admin/updating.md)
- [Backup & Restore](admin/backup-restore.md)
- [Migration Guide](migration-guide.md)
- [API Documentation](api/README.md) (incomplete)

## Respuesta a Incidentes

Utilizar un gestor de respuestas a incidentes, viene de la necesidad de tener un seguimiento, tanto de los potenciales ataques, como de los que se han llevado a cabo. Tambien es necesario correlacionar eventos, ya que los ataques en general se realizan en varias etapas diferentes. Incorporar estas caracteristicas mencionadas permite tener un mejor panorama de las amenazas.

#### Requisitos de Hardware

VER-> Hardware necesario dependiendo de la cantidad de trafico 
VER-> Cuanto almacenamiento es necesario para cuanto tiempo de retención.


Los requerimientos de hardware son

+ Núcleos CPU: 8vCPU
+ RAM: 8 GB
+ Disco: 60 GB

### TheHive
TheHive es una plataforma de respuesta a incidentes de seguridad gratuita y de código abierto escalable. En esta herramienta, cada investigación corresponde a un caso. En el mensaje que recibe desde Elastalert vienen los Observables, que son campos de la alerta considerados de interés para analizar.

### Cortex
Es una herramienta que sirve para analizar los Observables enviados a TheHive. Se pueden ejecutar operaciones mediante Responders que utilizan los Observables como variables de entrada. El analista puede ejecutar los Respondedores para que realicen algún tipo de acción automatizada.

#### Guías

- [Guía de Virtualización](respuesta-a-incidentes/gestor-incidentes-vm-guide.md)
- [Guía de Instalación](respuesta-a-incidentes/gestor-incidentes-install-guide.md)
- [Guía de Administración](admin/admin-guide.md)
- [Guía de Configuración](admin/configuration.md)
- [Webhooks](admin/webhooks.md)
- [Cluster Configuration](admin/cluster.md)
- [Updating](admin/updating.md)
- [Backup & Restore](admin/backup-restore.md)
- [Migration Guide](migration-guide.md)
- [API Documentation](api/README.md) (incomplete)

## ML

Agregar **todo**