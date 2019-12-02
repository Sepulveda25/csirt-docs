# csirt-docs

Documentación general del CSIRT

#### ¿Que hace el csirt?

Cuando ocurre un incidente de seguridad, una organización necesita una forma de responder. Un Equipo de respuesta a incidentes de seguridad informática (CSIRT) es un grupo interno que se encuentra comúnmente dentro de una organización que proporciona servicios y funciones para proteger los activos de esa organización.

Un CSIRT no solo responde necesariamente a incidentes que ya han sucedido. Un CSIRT también puede proporcionar servicios y funciones proactivos, como pruebas de penetración, detección de intrusiones o incluso capacitación en conciencia de seguridad. Estos tipos de servicios pueden ayudar a prevenir incidentes, pero también aumentan el tiempo de respuesta y mitigan los daños. En el caso de que un incidente de seguridad deba ser contenido y mitigado, el CSIRT coordina y supervisa estos esfuerzos.

#### La misión del equipo de CSIRT

+ Proporcionar un servicio de respuesta sistemática a los incidentes de ciberseguridad apoyando a las dependencias de la UNC a través del uso de la mejor tecnología disponible.
+ Sensibilizar en seguridad de las tecnologías de información y comunicación.
+ Incrementar el nivel de conocimiento acerca de la seguridad de la información.
+ Coordinar los esfuerzos para promover las mejores prácticas de la seguridad y crear confianza entre la comunidad del ciberespacio dentro de la UNC.
+ Ayudar a manejar ataques e incidentes de la seguridad de la información.
+ Generar recursos humanos en el campo de la seguridad de la información.
+ Proporcionar un ambiente de confianza para las e-transacciones.
+ Fomentar la confianza, cooperación y colaboración dentro de la UNC.
+ Intercambiar conocimiento con otros centros de respuesta a incidentes.
+ Formación de recursos humanos.

#### Partes del CSIRT

El CSIRT esta compuesto por tres partes: NSM, SIEM y Machine Learning

1. NSM (Monitoreo de seguridad de red) - Proporciona contexto, inteligencia y conciencia situacional de la red.
2. SIEM (Gestión de información y eventos de seguridad) - Se encaraga de almacenar la informacion recolectada para poder consultarla a futuro
3. ML  - Deteccion de intrusiones utilizando técnicas de Machine Learning

## Tabla de Contenidos
  * [Security Onion](#security-onion)
    * [Servidor Master](#servidor-master)
    * [Nodo Forward](#nodo-forward)
  * [Gestion de incidentes](#gestión-de-incidentes)
    * [TheHive](#thehive)
    * [Cortex](#cortex)
  * [ML](#Deteccion de intrusiones utilizando Machine Learning)

## Security Onion

Security Onion es un suite de herramientas gratuita y de código abierto desarrollado para detección de intrusos, monitoreo de seguridad empresarial y gestión de logs. Se distribuye en forma de una distribución de Linux o como grupos de paquetes de software descargables.

Entrelaza sin problemas tres funciones principales:
+ captura completa de paquetes;
+ sistemas de detección de intrusos basados en red y en host (NIDS y HIDS, respectivamente);
+ y potentes herramientas de análisis.

Se basa en un modelo distribuido cliente-servidor modificado. Una implementación distribuida estándar está compuesta por el servidor maestro (Servidor Master), uno o más nodos de reenvío (Nodos Forward) y uno o más nodos de almacenamiento (Nodos Storage). Esta arquitectura es ideal; Si bien puede costar más por adelantado, esta arquitectura proporciona una mayor escalabilidad y rendimiento en el futuro, ya que uno simplemente puede "conectar" nuevos nodos de almacenamiento para manejar más tráfico o fuentes de logs.

### Servidor Master

El servidor maestro ejecuta su propia copia local de Elasticsearch, que administra la configuración de búsqueda entre clústeres para la implementación. Esto incluye la configuración para Nodos Storage, pero no para Nodos Forward, ya que no ejecutan componentes de Elastic Stack. Un analista se puede conectar al servidor desde una estación de trabajo cliente para ejecutar consultas y recuperar datos.

El servidor maestro ejecuta los siguientes componentes (modo de producción con mejores prácticas):

+ Elasticsearch
+ Logstash
+ Kibana
+ Curador
+ Elastalert
+ Redis (solo si está configurado para enviar a un nodo de almacenamiento)
+ OSSEC
+ Sguild

#### Requisitos de Hardware

Un servidor maestro empresarial debe tener 8 núcleos de CPU como mínimo, 16-128 GB de RAM y suficiente espacio en disco (se recomiendan varios terabytes) para cumplir con sus requisitos de retención de logs. Los requerimientos pueden variar dependiendo de la cantidad de logs nuevos que son recibidos y de la cantidad de consultas que se realizan a los logs ya almacenados.

Cuanto almacenamiento es necesario para cuanto tiempo de retención.

+ Núcleos CPU:
+ RAM:

#### Guías

- [Guía de Instalación](security-onion/master/master-install-guide.md)
- [Guía de Administración](admin/admin-guide.md)
- [Guía de Configuración](admin/configuration.md)
- [Cluster Configuration](admin/cluster.md)
- [Updating](admin/updating.md)
- [Backup & Restore](admin/backup-restore.md)
- [Migration Guide](migration-guide.md)
- [Guía de Configuracion de Logstash](https://gitlab.unc.edu.ar/csirt/csirt-docs/tree/master/security-onion/master/logstash/master-logstash-guide.md#gu%C3%ADa-de-configuracion-de-logstash)

### Nodo Forward

Cuando se usa un Nodo Forward, los componentes de Elastic Stack no están instalados. Syslog-NG reenvía todos los registros a Logstash en el servidor maestro a través de un túnel autossh, donde se almacenan en Elasticsearch en el servidor maestro, o se reenvían a la instancia Elasticsearch del nodo de almacenamiento (si el servidor maestro se ha configurado para usar un nodo de almacenamiento). A partir de ahí, los datos pueden consultarse mediante el uso de la búsqueda búsqueda cruzada entre clústeres.

Los Nodos Forward ejecutan los siguientes componentes (modo de producción con mejores prácticas):

+ Bro
+ Snort / Suricata
+ Netsniff-NG
+ OSSEC
+ Syslog-NG

#### Requisitos de Hardware

Ver "Guía de Requerimientos de Hardware" abajo..

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

- [Guía de Requerimientos de Hardware](security-onion/forward/forward-hardware-guide.md#guía-de-requerimientos-de-hardware-de-un-nodo-forward)
- [Guía de Instalación](security-onion/forward/forward-install-guide.md#guía-de-instalación-de-nodo-forward)
- [Guía de Administración](admin/admin-guide.md)
- [Guía de Configuración](admin/configuration.md)
- [Cluster Configuration](admin/cluster.md)
- [Updating](admin/updating.md)
- [Backup & Restore](admin/backup-restore.md)
- [Migration Guide](migration-guide.md)
- [API Documentation](api/README.md) (incomplete)

## Gestión de incidentes

Utilizar un gestor de respuestas a incidentes, viene de la necesidad de tener un seguimiento, tanto de los potenciales ataques, como de los que se han llevado a cabo. También es necesario correlacionar eventos, ya que los ataques en general se realizan en varias etapas diferentes. Incorporar estas características mencionadas permite tener un mejor panorama de las amenazas.

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
Es una herramienta que sirve para analizar los Observables enviados a TheHive. Se pueden ejecutar operaciones mediante Responders que utilizan los Observables como variables de entrada. El analista puede ejecutar los Responders para que realicen algún tipo de acción automatizada.

#### Guías

- [Guía de Instalación](gestion-de-incidentes/incidentes-install-guide.md#guía-de-instalación-de-gestor-de-incidentes)
- [Guía de Configuración](admin/configuration.md)
- [Guía de Administración](gestion-de-incidentes/guia-administracion.md)
- [Webhooks](https://gitlab.unc.edu.ar/csirt/thehive-cortex-webhooks/tree/master#instalacion-webhooks-para-thehive-y-respuesta-automatica-a-alertas)
- [API Documentation](api/README.md) (incomplete)
- [Responders](https://gitlab.unc.edu.ar/csirt/thehive-cortex-responders/tree/master#responders-para-cortex-analyzer)
- [Como crear un responder](gestion-de-incidentes/responders-doc/como-crear-un-responder.md#cómo-escribir-un-responder)
- [Como ejecutar un responder](gestion-de-incidentes/responders-doc/ejecutar-responder.md#como-ejecutar-un-responder)

## Deteccion de intrusiones utilizando Machine Learning

De forma paralela, se desarrollo un Sistema de Deteccion de Intrusiones (IDS) utilizando un algoritmo de Arbol de Decision, el cual fue entrenado con datos propios
de las dependencias de la Universidad. El sistema clasifica los flujos de conexiones en alguno de los siguientes tipos:

+ Trafico Benigno
+ Escaneo de puertos
+ Ataque de denegacion de servicio
+ Ataque de inyeccion SQL
+ Acceso a SSH por fuerza bruta

El mismo sistema se encarga de generar alertas, las cuales se almacenan en un log llamado **alert.log**. Los datos de las alertas son enviados al servidor master
de Security Onion.

- [Guia de uso](https://gitlab.unc.edu.ar/csirt/ml_implementation)
