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

descripcion

#### Requisitos de Hardware

Hardware necesario dependiendo de la cantidad de trafico

Cuanto almacenamiento es necesario para cuanto tiempo de retención.

+ Núcleos CPU:
+ RAM:

### TheHive

### Cortex

## ML

Agregar **todo**