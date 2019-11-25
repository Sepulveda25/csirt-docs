# Guía de Requerimientos de Hardware de un Nodo Forward

En la siguiente guía se especifican los requerimientos de hardware de un nodo forward de Security Onion. En cada sección se tiene las recomendaciones que están en la documentación oficial de Security Onion y se detallan las modificaciones realizadas a estas recomendaciones.

Un nodo forward solo ejecuta componentes del sensor y reenvía metadatos al servidor maestro. Todo los PCAP permanece local para el sensor y se accede a través del uso de un agente.

- CPU: se utiliza para analizar y almacenar el tráfico de red. A medida que aumenta el ancho de banda monitoreado, se requerirá una mayor cantidad de CPU.
- RAM: se utiliza para caché de escritura y procesamiento de tráfico.
- Disco: se utiliza para el almacenamiento de PCAP y metadatos. Una mayor cantidad de almacenamiento permite un período de retención más largo.

[Documentación oficial](https://securityonion.readthedocs.io/en/latest/hardware.html#sensor-hardware-considerations)

[Volver a documento raíz](https://gitlab.unc.edu.ar/csirt/csirt-docs/tree/master#csirt-docs)

## Tabla de Contenidos
  * [Virtualización](#virtualización)
  * [CPU](#cpu)
  * [RAM](#ram)
  * [Almacenamiento](#almacenamiento)

### Virtualización

De la documentación oficial:

> Se recomienda hardware físico dedicado (especialmente si está monitoreando mucho tráfico) para evitar competir por los recursos. Los sensores se pueden virtualizar, pero se debe asegurar de que se les asignen recursos suficientes.

Por otro lado se recomienda un entorno virtualizado por la facilidad de manejar los sensores desde un lugar central ya que todos los sensores no estarán ubicados físicamente en el mismo lugar.

### CPU

De la documentación oficial:

> Suricata y Bro son muy intensivos en CPU. Mientras más tráfico esté monitoreando, más núcleos de CPU necesitará. Un cálculo aproximado aproximado sería de 200 Mbps por instancia de Snort, trabajador de Suricata o trabajador de Bro. Entonces, si tiene un enlace de 1 Gbps completamente saturado y está ejecutando Snort y Bro, entonces querrá al menos 5 instancias de Snort y 5 trabajadores de Bro, lo que significa que necesitará al menos 10 núcleos de CPU para Snort y Bro con núcleos de CPU adicionales para netsniff-ng y/u otros servicios.

Estos valores recomendados pueden ser reducidos al utilizar la afinidad de CPU, es decir al fijar los procesos de Suricata, Bro y netsniff-ng en núcleos físicos del procesador. De este modo los múltiples procesos/hilos se ejecutan solamente en los procesos especificados en lugar de cualquier CPU.

TODO: **VALORES RECOMENDADOS POR NIVELES DE TRAFICO**

### RAM

De la documentación oficial:

> El uso de RAM depende en gran medida de varias variables:
> 
> - los servicios que habilita
> - los tipos de tráfico que está monitoreando
> - la cantidad real de tráfico que está monitoreando (ejemplo: se puede estar monitoreando un enlace de 1 Gbps pero solo usa 200 Mbps la mayor parte del tiempo)
> - la cantidad de pérdida de paquetes que es "aceptable"
> 
> Las siguientes estimaciones de RAM son una guía aproximada y suponen que va a ejecutar Suricata, Bro y netsniff-ng (captura de paquetes completa) y desea minimizar/eliminar la pérdida de paquetes.
> 
> Si está implementando Security Onion en producción en una red mediana (50Mbps - 500Mbps), debe planear 16GB - 128GB RAM o más.
> 
> Si está implementando Security Onion en producción en una red grande (500Mbps - 1000Mbps), debe planear 128GB - 256GB RAM o más.

Una modificación adicional para tener un mejor rendimiento de la RAM disponible es modificar la forma en la que Linux maneja cache del disco duro en la RAM (en este caso para realizar escrituras al disco). Se tomaron en cuenta recomendaciones de [aquí](https://lonesysadmin.net/2013/12/22/better-linux-disk-caching-performance-vm-dirty_ratio/) y las pruebas de rendimiento realizadas, variando estos valores.

Con esto y las modificaciones relacionadas a CPU, los valores de guía pueden ser reducidos notablemente sin ocasionar perdidas significativas

TODO: **VALORES RECOMENDADOS POR NIVELES DE TRAFICO**

### Almacenamiento

De la documentación oficial:

> Los sensores que tienen habilitada la captura completa de paquetes necesitan mucho almacenamiento. Por ejemplo, suponga que está monitoreando un enlace que promedia 50 Mbps, aquí hay algunos cálculos rápidos: 50 Mb/s = 6.25 MB/s = 375 MB/minuto = 22,500 MB/hora = 540,000 MB/día. Por lo tanto, necesitará aproximadamente 540 GB para un día de pcaps (multiplique esto por la cantidad de días que desea mantener en el disco con fines de investigación/forenses). Cuanto más espacio en disco tenga, más retención de PCAP tendrá para realizar investigaciones después del hecho.
> 
> Se recomienda utilizar el almacenamiento local siempre que sea posible. SAN/iSCSI/FibreChannel/NFS puede funcionar, pero aumentan la complejidad, los puntos de falla y tienen serias implicaciones de rendimiento. Al usar el almacenamiento local, mantiene todo autocontenido y no tiene que preocuparse por competir por los recursos. El almacenamiento local es la mayoría de las veces la solución más rentable también.
